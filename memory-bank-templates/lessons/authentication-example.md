# Module: Authentication

**Last Updated**: 2024-01-15
**Total Lessons**: 3
**Related Paths**: src/auth/, src/services/auth/, src/middleware/auth/

---

## L001: JWT Token Lifecycle Management

**Context**: When implementing user authentication with JWT tokens for session management

**Challenge**: Token expiration caused frequent user re-logins and poor user experience, especially for longer sessions

**Best Practice**:
- Implement separate refresh token mechanism (longer expiry) alongside access token
- Store tokens in HttpOnly cookies to prevent XSS attacks
- Auto-refresh access token 5 minutes before expiry
- Implement silent refresh to avoid user interruption
- Handle edge cases: concurrent refreshes, network failures

**Code Pattern**:
```typescript
// Refresh strategy
const shouldRefresh = (expiresAt: number): boolean => {
  const fiveMinutes = 5 * 60 * 1000;
  return Date.now() >= expiresAt - fiveMinutes;
};

// Silent refresh implementation
const silentRefresh = async (refreshToken: string) => {
  try {
    const { accessToken } = await api.refreshToken(refreshToken);
    // Update token in HttpOnly cookie
    return accessToken;
  } catch (error) {
    // Redirect to login on failure
    handleAuthFailure();
  }
};
```

**Anti-Pattern**:
```typescript
// DON'T: Store tokens in localStorage (vulnerable to XSS)
localStorage.setItem('token', accessToken);

// DON'T: Wait for token to expire before refreshing
if (isTokenExpired(token)) { refresh(); }
```

**Related**: [Task-Auth-2024-01], [authentication]
**Tags**: #authentication #jwt #security #ux

---

## L002: OAuth Integration Error Handling

**Context**: When integrating third-party OAuth providers (Google, GitHub, etc.)

**Challenge**: OAuth flows can fail at multiple points (user denial, network issues, provider downtime), leading to poor user experience without proper error handling

**Best Practice**:
- Implement state parameter validation to prevent CSRF attacks
- Handle all OAuth error cases with user-friendly messages
- Provide fallback authentication methods
- Log OAuth errors for debugging (without exposing sensitive data)
- Implement timeout handling for provider callbacks

**Code Pattern**:
```typescript
// OAuth error handling
enum OAuthError {
  USER_DENIED = 'access_denied',
  INVALID_STATE = 'invalid_state',
  PROVIDER_ERROR = 'server_error',
}

const handleOAuthCallback = async (code: string, state: string) => {
  // Validate state
  if (!validateState(state)) {
    throw new Error(OAuthError.INVALID_STATE);
  }

  try {
    return await exchangeCodeForToken(code);
  } catch (error) {
    logOAuthError(error); // Log for debugging
    throw new UserFacingError('Authentication failed. Please try again.');
  }
};
```

**Related**: [Task-OAuth-2024-01], [authentication]
**Tags**: #oauth #error-handling #security

---

## L003: Session Management and Concurrent Logins

**Context**: When users access the application from multiple devices/browsers simultaneously

**Challenge**: Invalidating sessions across devices without affecting active sessions on other devices

**Best Practice**:
- Use session identifiers to track individual sessions
- Store session metadata (device, location, timestamp) for user visibility
- Implement selective logout (single device vs all devices)
- Provide session management UI for users to view/revoke sessions
- Handle token refresh conflicts when multiple tabs are open

**Code Pattern**:
```typescript
// Session tracking
interface Session {
  id: string;
  userId: string;
  deviceInfo: string;
  createdAt: Date;
  lastActive: Date;
}

// Logout options
const logout = async (userId: string, sessionId?: string) => {
  if (sessionId) {
    // Logout single session
    await revokeSession(userId, sessionId);
  } else {
    // Logout all sessions
    await revokeAllSessions(userId);
  }
};
```

**Related**: [Task-Session-2024-01], [authentication]
**Tags**: #session-management #multi-device #ux

---
