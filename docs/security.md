# Security notes

`cc-session` runs an interactive coding agent with the permissions you give it.
Treat it as a remote shell.

## Checklist

- Bind `ttyd` to `127.0.0.1` by default.
- If you expose it through nginx, terminate TLS and require authentication.
- Do not commit `~/.cc-sessions/.nginx-credentials` or any password.
- Use `CC_PERMISSION_MODE=auto` or `acceptEdits` for normal work.
- Use `bypassPermissions` only in a disposable VM/container.
- Session logs under `~/.cc-sessions/<name>/session.log` may contain source code,
  command output, environment variables, or tokens. Restrict permissions and
  delete old logs.
- Do not run an agent as root unless the whole machine is disposable.
