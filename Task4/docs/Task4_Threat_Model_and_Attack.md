# Task 4 - Explain What Went Wrong and the Threat Model

## 1) Where the security value is generated

The security-sensitive value is the session token generated in:

- Class: `Login`
- Method: `generateSessionToken()`
- File location: `sources-code/Login.java` lines 183-189

Generation logic:

```java
private String generateSessionToken() {
    Random random = new Random();
    StringBuilder sb = new StringBuilder(16);
    for (int i = 0; i < 16; i++) {
        sb.append("ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789"
            .charAt(random.nextInt(62)));
    }
    return sb.toString();
}
```

The token is saved as `sessionToken` in `SharedPreferences` during `createSession()` (lines 174-176), immediately after successful credential check.

## 2) Why this method is unsuitable for its security role

`java.util.Random` is not designed for cryptographic security. It is deterministic and has a 48-bit internal state. In Android/Java, `new Random()` is seeded from predictable runtime state (commonly related to current time), so outputs can often be narrowed when token creation time is approximately known.

For a session token, unpredictability is the core requirement. Even though the token appears long (16 chars from 62-char alphabet), effective security is limited by PRNG predictability/state recovery, not by visual complexity.

The token acts as proof of authenticated state, so unpredictability is required.

So the core mistake is: a non-cryptographic PRNG (`Random`) was used for an authentication/session value.

## 3) Attacker knowledge/observation needed to exploit

To exploit this weakness, the attacker needs:

1. Token generation algorithm details:
   - token length = 16
   - charset = `A-Za-z0-9`
   - generator = `java.util.Random`
2. Approximate token creation time window (e.g., victim login observed within a few seconds).
3. A way to test or use guessed tokens:
   - session restore/check logic, or
   - ability to write app session storage on a rooted device.

## 4) Realistic attacker model and capabilities

### Attacker model
A two-level attacker model is used:

- **Baseline attacker**: has APK access, can decompile code, and can observe approximate user login time.
- **Stronger attacker**: in addition to baseline capabilities, has rooted-device access and can read/write app-private storage.

### Capabilities

- Can decompile APK and read Java logic.
- Can learn exact token format and generator API.
- Can observe approximate victim login time (screen observation, usage timing, or logs).
- On rooted devices, can read/write app-private `SharedPreferences` files.

### Goal
Bypass authentication or impersonate a logged-in user by reproducing or injecting a valid session token without knowing the user password.

## 5) Step-by-step attack scenario (short)

1. **Reverse engineer**: attacker decompiles the APK and finds `generateSessionToken()` in `Login.java`.
2. **Collect timing**: attacker observes victim login at time `T`, with an uncertainty of about +/-5 seconds.
3. **Generate candidates**: attacker runs the same `Random`-based algorithm for seeds in `[T-5000, T+5000]` milliseconds (about 10,001 seed candidates).
4. **Token trial/injection**:
   - if the application accepts session state based only on stored token value, a guessed token may restore authenticated state;
   - in a rooted-device scenario, attacker can directly write candidate tokens into `SessionPrefs` and trigger app session flow.
5. **Compromise**: once a matching token is accepted, attacker gains authenticated session behavior (impersonation/session hijack).

## 6) Security impact

Using predictable randomness for session tokens weakens authentication guarantees. The attack cost becomes significantly lower when attacker can narrow token creation time or interact repeatedly with token validation logic.

