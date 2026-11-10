# Entra ID Sign In Error Codes

Common codes seen during sign in investigations, checked against Microsoft's official reference. In sign in logs these may appear as just the number (for example `50126`) or with the prefix `AADSTS` (for example `AADSTS50126`).

> **Codes change.** Microsoft states error codes and messages can change at any time. Always confirm with the [live error lookup](https://login.microsoftonline.com/error).

## Authentication and sign in

| Code | Microsoft name | Plain language meaning | Analyst note |
|---|---|---|---|
| **50053** | IdsLocked, or malicious IP block | Either the account is locked from too many failed sign ins, **or** the sign in came from an IP with malicious activity | Check the **Failure reason** to see which. Don't assume compromise |
| **50126** | InvalidUserNameOrPassword | Wrong username or password | Some are normal user mistakes. Many in a short time from one IP may be password guessing |
| **50074** | UserStrongAuthClientAuthNRequiredInterrupt | MFA was required **and the user did not pass the MFA challenge** | A correct password followed by a failed MFA can mean someone else has the password |
| **50105** | EntitlementGrantsNotFound | User isn't assigned to a role for the app | Usually an access setup issue, not an attack |
| **50125** | PasswordResetRegistrationRequiredInterrupt | Sign in paused so the user could reset their password or register password reset info | Usually benign. Check if sign in completed, and whether security info was changed from an unusual IP or location |

## Conditional Access and device

| Code | Microsoft name | Plain language meaning | Analyst note |
|---|---|---|---|
| **53003** | BlockedByConditionalAccess | A Conditional Access policy blocked access | Check the **Conditional Access** tab to see which policy and why |
| **50097** | DeviceAuthenticationRequired | The device must be authenticated before access | Often an unmanaged or unregistered device |

## Look up any code

Add the number to the end of this link: `https://login.microsoftonline.com/error?code=` followed by the code. For example: [50126](https://login.microsoftonline.com/error?code=50126)

## Reference

- [Microsoft Entra authentication and authorization error codes](https://learn.microsoft.com/en-us/entra/identity-platform/reference-error-codes)
