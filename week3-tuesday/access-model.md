# KijaniKiosk Access Model

## Design Table

| Path | Owner | Group | Mode | ACLs | Why |
|---|---|---|---|---|---|
| `/opt/kijanikiosk/` | root | kijanikiosk | 750 | — | Parent directory. 750 lets only group members traverse into it at all; everyone else is blocked before they can even see what's inside. |
| `/opt/kijanikiosk/api/` | kk-api | kk-api | 750 | `michael:r-x` | Owned by its own service account and its own private group so no other service can read it by default. The ACL gives the admin visibility for troubleshooting without changing group ownership. |
| `/opt/kijanikiosk/payments/` | kk-payments | kk-payments | 750 | `michael:r-x` | Same pattern as `api/`. Payments code is the most sensitive of the three, so isolation here matters most. |
| `/opt/kijanikiosk/logs/` | kk-logs | kk-logs | 750 | — | Same pattern again. No ACL needed yet since nothing else currently reads this path. |
| `/opt/kijanikiosk/config/` | root | kijanikiosk | 750 (dir) / 640 (files) | `michael:r-x` on dir, `michael:r--` on files | Root owns the secrets so no single service account can modify them. The shared `kijanikiosk` group lets all three services read what they need via basic permissions — see limitation below. ACL adds the admin without putting him in the group. |
| `/opt/kijanikiosk/shared/logs/` | kk-logs | kk-logs | 2770 (SGID) | per-user ACLs for kk-api, kk-payments, michael + matching defaults | Three different accounts need three different access levels (write, read, read) on one directory — basic owner/group/other can't express that, so ACLs are required. SGID makes every new file inherit the `kk-logs` group automatically, so permissions don't silently break as files get created. Default ACLs make the per-user grants apply to future files too, not just the ones that exist today. |
| `/opt/kijanikiosk/scripts/deploy.sh` | root | root | 750 | — | Removes the SUID bit and world-write from the original misconfiguration. Owned by root since it's executed by a root cron job; no service account needs access to it. |

## Why group permissions in some places, ACLs in others

- **Basic group permissions** are used wherever one account (or one shared group) needs one consistent level of access. `api/`, `payments/`, `logs/` are each scoped to a single service.
- **ACLs** are used wherever more than one specific identity needs a *different* level of access to the same object — `shared/logs/` needs write for one service and read for two others; `config/` and the service directories need the admin to have read access without joining the service's own group.

## Known limitation

`config/` uses the shared `kijanikiosk` group for simplicity, which means `kk-api` can technically read `payments-api.env` and `kk-payments` can read `db.env`, even though neither needs the other's secret. A stricter design would use per-file ACLs instead (`kk-api` on `db.env` only, `kk-payments` on `payments-api.env` only) so each service can read only its own credentials. Not implemented here to keep the model simple, but flagged as a follow-up.
