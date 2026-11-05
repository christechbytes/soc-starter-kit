# Change Management Templates

Copy these into your own tool (a spreadsheet, ticket system, or document). All examples are fictional.

## Change Request Form

| Field | Entry |
|---|---|
| Change ID | |
| Requested by | |
| Date requested | |
| System or device | |
| Description of change | |
| Reason for change | |
| Risk level (Low / Medium / High) | |
| Testing plan | |
| Rollback plan (how to undo it) | |
| Planned date and time | |
| Approved by | |
| Date approved | |
| Implemented by | |
| Date and time implemented | |
| Result (Successful / Rolled back) | |

## Change Log

One row per change. This is what you check during an investigation.

| Change ID | Date | System | Change | Requested by | Approved by | Implemented by | Status |
|---|---|---|---|---|---|---|---|
| CHG-001 | 2026-01-10 | demo-firewall-01 | Added rule allowing HTTPS to web server | J. Smith | A. Lee | J. Smith | Completed |
| CHG-002 | 2026-01-14 | demo-orders-db | Restricted database access to internal network only | M. Brown | A. Lee | M. Brown | Completed |
| CHG-003 | 2026-01-20 | demo-firewall-01 | Updated IPS sensor | J. Smith | Pending | | Awaiting approval |

## Tips

- **Never approve your own change.** The person who approves should be different from the person who requests it
- **Emergency changes still get recorded.** Document them as soon as possible after the fact
- **Rollback plans matter.** If you can't undo a change, it's higher risk
