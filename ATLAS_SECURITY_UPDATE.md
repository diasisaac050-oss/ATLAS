# ATLAS - Security + Presence hearing fix

Presence "appears but does not hear": microphone foreground type is now
retried until Android grants it BEFORE the mic opens; recognizer is rebuilt
after cancel/hard errors; startup watchdog; duplicate-start guard.
Also fixed: stop-speaking button left the mic dead, AI history corrupted after
an error, reminder alarm still rang after deleting the reminder, new bubble
destroyed by the old bubble's close animation.

Security: backups off, HTTPS-only + system CAs only, PBKDF2 PIN + lockout,
Vault locks on leaving the app, Keystore AES-GCM SecureStorage, no redirects /
no raw server errors, request rate limit, crash trace hidden in release,
secrets redacted, R8 + resource shrinking. ApiKeyConfig.java untouched.
