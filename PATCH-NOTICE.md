# dsh-purge full build — patch notice / 修改说明

This is a locally modified derivative of **dsh-purge 1.1.77**. It is not an
official upstream release. Keep this notice, the upstream `NOTICE`, and the
upstream attribution when redistributing.

## Base

| Item | Value |
| --- | --- |
| Upstream | <https://github.com/YuJunZhiXue/dsh-purge> |
| Base tag / commit | `v1.1.77` / `ac58b3e35509da23bd75b6250f01007590524102` |
| Base license | Apache-2.0 (`LICENSE`, `NOTICE` retained) |
| Target harness | DSH `0.2.1-rc.2` |

## Restored content

The public 1.1.73+ line no longer ships `skills/redteam`, and 1.1.77 stubs the
own-server allowlist (`writeAllowText()` is a no-op, `permittedHost()` always
returns `false`). This build restores both from the community mirror:

| Item | Value |
| --- | --- |
| Mirror | <https://github.com/mmparko/dsh-purge> |
| Mirror tag / commit | `v1.1.71` / `5e757260a0be055892b48ec520d451cc82e1c191` |
| Restored skills | `skills/redteam/*.md`, 23 files, 2,605 lines |
| Mirror license | MIT (the mirror keeps the upstream MIT `LICENSE`) |

The restored skill files are upstream/mirror content, not authored by this
build. They are included unchanged.

## Local patches (3 code commits)

1. **`feat: restore 23 redteam skills from the 1.1.71 mirror`**
   - Adds `skills/redteam/*.md`.
   - Adds `"skills"` to `package.json` `files` so tarball installs include it.
2. **`feat: restore the own-server allowlist on the 1.1.77 gate`**
   - Restores `writeAllowText()` / `permittedHost()` / `isRangeTarget()` from
     v1.1.71.
   - Wires the allowlist into the new v1.1.77 `publicHostHit()` gate.
   - Restores the own-servers textarea and save button in `client.js`.
   - Rewords the hint: exact IPs or full hostnames only; no CIDR, wildcard,
     or suffix matching.
3. **`fix: insert the bundle root row for tarball installs`**
   - Adds an explicit `dsh-purge` insert to `cordis.patch.yml`.
   - Needed because a manual `file:` tarball install does not create the
     bundle-root row, so `lib/index.js` would not apply.

A fourth docs commit adds this notice and `INSTALL.md`; it does not change
runtime behavior.

Diff footprint against upstream `v1.1.77`:

- Non-skill code: 4 files, **73 insertions / 10 deletions**.
- Restored skills: 23 files, **2,605 insertions**.
- Original integration logic written for this build: roughly 10 lines; most of
  the non-skill diff is verbatim restoration from v1.1.71 or configuration.

## The one behavioral change

Upstream 1.1.77 deliberately allows only loopback and RFC1918 addresses. This
build adds **exact, explicitly registered public IPs or full hostnames** from
`$DSH_HOME/net-scope-allow.txt`. It does **not** add CIDR ranges, wildcards,
suffix matching, or DNS-based blanket trust.

Use this build only against systems you own or have explicit written
authorization to test. The authors of the upstream project and of this build
accept no liability for misuse.

## Attribution and redistribution

- Upstream author: **YuJunZhiXue** — <https://github.com/YuJunZhiXue/dsh-purge>
- Mirror maintainer: **mmparko** — <https://github.com/mmparko/dsh-purge>
- The upstream README requires attribution and forbids selling the project,
  its code, prompts, or derivatives (no paid courses, paid plugins, token
  resale, or similar).
- This build is not affiliated with or endorsed by the upstream author.
- Keep `LICENSE`, `NOTICE`, and this file with any copy you distribute.
