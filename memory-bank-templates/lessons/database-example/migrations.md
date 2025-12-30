# Sub-Topic: Database Migrations

**Module**: Database | [Back to Module Index](_module-index.md)
**Last Updated**: 2024-01-15
**Total Lessons**: 6
**Related Paths**: migrations/, src/database/migrator/

---

## L001: Zero-Downtime Migration Strategy

**Context**: When deploying database schema changes in production without service interruption

**Challenge**: Traditional migrations cause downtime as tables are locked during ALTER operations, affecting user experience

**Best Practice**:
- Use backward-compatible migrations in multiple phases
- Phase 1: Add new columns as nullable (no data migration yet)
- Phase 2: Deploy application code that writes to both old and new columns
- Phase 3: Backfill data in background (with batching)
- Phase 4: Deploy code that reads from new columns
- Phase 5: Remove old columns in another migration
- Never change column types directly; create new column and migrate

**Code Pattern**:
```sql
-- Phase 1: Add new nullable column
ALTER TABLE users ADD COLUMN email_verified_at TIMESTAMP NULL;

-- Phase 3: Backfill in batches (background job)
UPDATE users
SET email_verified_at = NOW()
WHERE email_verified = true
  AND email_verified_at IS NULL
LIMIT 1000; -- Run repeatedly until complete
```

**Anti-Pattern**:
```sql
-- DON'T: This locks the entire table
ALTER TABLE users
  MODIFY COLUMN status VARCHAR(20) NOT NULL DEFAULT 'active',
  ADD COLUMN new_field VARCHAR(100);
```

**Effectiveness**:
- Created: 2024-01-10
- Applied: 12 times
- Last Applied: 2024-01-18
- Success Rate: 100% (12/12 successful)
- Status: Active

**Related**: [Task-DB-Migration-2024-01], [Task-User-Schema-2024-01]
**Tags**: #migration #zero-downtime #production #deployment

---

## L002: Migration Rollback Procedures

**Context**: When a migration fails in production and needs to be rolled back safely

**Challenge**: Failed migrations can leave database in inconsistent state; rollbacks might lose data if not planned

**Best Practice**:
- Always write `up` and `down` migrations together
- Test rollback in staging before production deployment
- For data migrations, keep backup of modified data
- Use transactions where possible (DDL support varies by database)
- Document manual rollback steps for complex migrations
- Never delete old columns until several releases after migration

**Code Pattern**:
```javascript
// Migration with proper rollback
export async function up(db) {
  await db.schema.createTable('orders', (table) => {
    table.increments('id');
    table.integer('user_id').notNullable();
    // ... other columns
  });
}

export async function down(db) {
  await db.schema.dropTable('orders');
}
```

**Effectiveness**:
- Created: 2024-01-10
- Applied: 8 times
- Last Applied: 2024-01-18
- Success Rate: 100% (8/8 successful)
- Status: Active

**Related**: [Task-DB-Migration-2024-01], [Task-Order-System-2024-01]
**Tags**: #migration #rollback #disaster-recovery

---

## L003: Data Migration Best Practices

**Context**: When migrating large amounts of data between schema versions

**Challenge**: Large data migrations can timeout, lock tables, or consume excessive memory

**Best Practice**:
- Process data in batches (1000-10000 rows per batch)
- Use background jobs for large migrations
- Add progress logging for monitoring
- Make migrations idempotent (safe to run multiple times)
- Consider using database-specific bulk operations
- Test with production-sized data in staging

**Code Pattern**:
```javascript
// Idempotent batch migration
async function migrateUserEmails() {
  let offset = 0;
  const batchSize = 1000;

  while (true) {
    const users = await db('users')
      .whereNull('email_normalized')
      .limit(batchSize)
      .offset(offset);

    if (users.length === 0) break;

    await db.transaction(async (trx) => {
      for (const user of users) {
        await trx('users')
          .where('id', user.id)
          .update({
            email_normalized: user.email.toLowerCase().trim()
          });
      }
    });

    console.log(`Migrated ${offset + users.length} users`);
    offset += users.length;
  }
}
```

**Effectiveness**:
- Created: 2024-01-12
- Applied: 6 times
- Last Applied: 2024-01-17
- Success Rate: 83% (5/6 successful, 1 needed optimization)
- Status: Active

**Related**: [Task-User-Normalization-2024-01]
**Tags**: #migration #data-migration #batch-processing

---

## L004: Schema Version Control

**Context**: Managing database schema changes across development, staging, and production environments

**Challenge**: Schema drift between environments leads to deployment failures and bugs

**Best Practice**:
- Use migration framework (e.g., Knex, Flyway, Alembic)
- Never manually modify production schema
- Include migrations in version control with descriptive names
- Name migrations with timestamp prefix: `20240120_add_email_verification`
- Run migrations as part of deployment pipeline
- Track migration history in database (`schema_migrations` table)

**Code Pattern**:
```bash
# Migration naming convention
migrations/
├── 20240110_create_users_table.js
├── 20240112_add_email_to_users.js
├── 20240115_create_orders_table.js
└── 20240120_add_order_status_index.js

# Run migrations in CI/CD
npm run migrate:latest
```

**Effectiveness**:
- Created: 2024-01-10
- Applied: 10 times
- Last Applied: 2024-01-19
- Success Rate: 100% (10/10 successful)
- Status: Active

**Related**: [Task-DB-Setup-2024-01]
**Tags**: #migration #version-control #devops

---

## L005: Migration Testing Strategies

**Context**: Ensuring migrations work correctly before production deployment

**Challenge**: Untested migrations can fail in production, causing downtime and data loss

**Best Practice**:
- Test migrations against production-sized datasets in staging
- Test both `up` and `down` migration paths
- Include migration tests in CI pipeline
- Verify data integrity after migration
- Test migration performance (execution time)
- Simulate failure scenarios and rollback

**Code Pattern**:
```javascript
// Migration test
describe('AddEmailVerificationMigration', () => {
  beforeEach(async () => {
    await migrator.rollback();
    await seedTestData();
  });

  it('should add email_verified_at column', async () => {
    await migrator.up();
    const columns = await db('users').columnInfo();
    expect(columns).toHaveProperty('email_verified_at');
  });

  it('should rollback successfully', async () => {
    await migrator.up();
    await migrator.down();
    const columns = await db('users').columnInfo();
    expect(columns).not.toHaveProperty('email_verified_at');
  });
});
```

**Effectiveness**:
- Created: 2024-01-14
- Applied: 5 times
- Last Applied: 2024-01-18
- Success Rate: 100% (5/5 successful)
- Status: Active

**Related**: [Task-Migration-Testing-2024-01]
**Tags**: #migration #testing #ci-cd

---

## L006: Handling Large Table Migrations

**Context**: Migrating tables with millions of rows that standard approaches would lock

**Challenge**: ALTER TABLE on large tables can lock for hours, causing production outage

**Best Practice**:
- Use online schema change tools (pt-online-schema-change for MySQL, pg_repack for PostgreSQL)
- For adding indexes: use CONCURRENT option (PostgreSQL) or online DDL (MySQL 5.6+)
- Create new table, copy data, swap atomically for major changes
- Schedule migrations during low-traffic periods
- Monitor replication lag during migration

**Code Pattern**:
```sql
-- PostgreSQL: Create index without locking
CREATE INDEX CONCURRENTLY idx_users_email ON users(email);

-- MySQL: Online DDL (5.6+)
ALTER TABLE users
ADD INDEX idx_email (email)
ALGORITHM=INPLACE, LOCK=NONE;
```

**Anti-Pattern**:
```sql
-- DON'T: This locks the table for hours on large tables
ALTER TABLE users ADD INDEX idx_email (email);
```

**Effectiveness**:
- Created: 2024-01-15
- Applied: 4 times
- Last Applied: 2024-01-19
- Success Rate: 100% (4/4 successful)
- Status: Active

**Related**: [Task-Index-Performance-2024-01]
**Tags**: #migration #performance #large-tables #indexing

---

## Navigation

[← Back to Module Index](_module-index.md) | [View All Database Lessons](_module-index.md#sub-topics)
