# cloudflare-tunnel-config-checker-service

<!-- 561-group-standards:badges:start -->
[![Red Cup Engineering](https://img.shields.io/badge/561%20Group-Red%20Cup%20Engineering-c0392b)](https://github.com/red-cup-engineering)
[![License: MIT](https://img.shields.io/badge/license-MIT-c0392b)](LICENSE)
[![Indigenous data: CARE](https://img.shields.io/badge/Indigenous%20data-CARE-4b6f44)](https://github.com/red-cup-engineering/.github/blob/main/GOVERNANCE.md)
[![Tips welcome](https://img.shields.io/badge/tips-welcome-d4a017)](https://github.com/561-group/.github/blob/main/TIPS.md)
<!-- 561-group-standards:badges:end -->

Verify that a named tunnel's route binding and systemd unit conform to the fixed laptop-owned token-file ingress shape.

## Opt-in host binary version qualification

The static route/unit verifier does **not** establish that an installed or running `cloudflared` binary is patched. For a bounded **installed-file** check, add both `--cloudflared-bin=/absolute/path/to/cloudflared` and `--min-cloudflared-version=YYYY.M.P` to the existing `verify-ingress` invocation. For the October 2026 eMudhra certificate compromise, Cloudflare's required floor is **2026.10.2** (or **2026.10.3** for non-Homebrew macOS). See [Cloudflare's 2026-10-20 incident bulletin](https://blog.cloudflare.com/emudhra-certificate-compromise/).

Example: after supplying this service's existing route and unit expectations, append:

```sh
--cloudflared-bin=/home/emsenn/.local/bin/cloudflared \
--min-cloudflared-version=2026.10.2
```

The two arguments must be paired. The verifier runs that exact **absolute** executable with `--version`, without shell or token access, and refuses missing/non-executable binaries, malformed/prerelease versions, old versions, and nonzero exits. Leaving both flags absent keeps the existing route/unit contract unchanged, without implying any binary was checked. Never pass a token value as an argument.

This proves only the tested file's self-reported version. It is **not** a proof that a service's current process executes that file, that every connector on the host was upgraded, or that the tunnel has reconnected and the application has passed its ingress, anonymous-protection and OAuth checks. Those require original host PID, service, route and independent consumer observations. A systemd unit with `--no-autoupdate` cannot be presumed upgraded just because this source was changed.
