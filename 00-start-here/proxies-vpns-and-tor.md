# Proxies, VPNs, and Tor

Many alerts involve an IP address that isn't where the user really is. Attackers hide behind these services, but so do normal users. This page explains the difference.

## The main types

| Type | What it is | Common legitimate use | Why attackers like it |
|---|---|---|---|
| **VPN** | An encrypted tunnel. Traffic appears to come from the VPN provider's server | Company remote access, personal privacy | Hides their real location |
| **Datacenter proxy** | A server in a hosting provider that forwards traffic | Business tools, testing | Cheap and easy to rotate |
| **Residential proxy** | Traffic routed through real home devices, often without the owner fully knowing | Rare for normal users | Looks like a regular home user, so it slips past reputation checks |
| **Callback proxy** | An IP used by malware to call back to an attacker | Very rare | Commonly linked to malware command and control |
| **Tor** | A volunteer network that bounces traffic through several relays. The last one is the **exit node** | Privacy, journalism, censorship | Very hard to trace |
| **Tunnel** | General term for any service that wraps traffic and hides the source (VPN, proxy, remote desktop) | Varies | Varies |

## Common IP reputation tags

Tags differ between tools. These examples come from Spur and similar services.

| Tag | Meaning | How to read it |
|---|---|---|
| **TUNNEL** | The IP is a VPN or proxy exit | Find out which service. A company VPN is very different from an anonymous one |
| **PROXY** | The IP is linked to proxy infrastructure | Traffic may be routed through a middleman, hiding the real source |
| **CALLBACK_PROXY** | The IP is used as a callback proxy | Treat seriously. Often linked to malware |
| **GEO_MISMATCH** | The location an IP claims differs from where it really is | Don't rely on geolocation alone |
| **LOGIN_BRUTEFORCE** | The IP has been seen guessing passwords | Higher concern on failed or successful sign ins |
| **AD_FRAUD** (some tools) | Linked to advertising fraud | Shady traffic, but not proof of account compromise |
| **anonymous: false** | Not flagged as anonymous | Doesn't prove the IP is safe |

## How this affects triage

- **A VPN or proxy doesn't prove an attack.** Ask: is it the company's own VPN, or one the user normally uses?
- **Check whether the user normally signs in this way.** Sign in history tells you a lot
- **Residential proxies are tricky.** The IP may look completely normal. Lean on device, authentication, and behavior checks instead
- **A Tor exit node signing in to a company account is unusual.** Most companies would expect to escalate it
- **Impossible travel often comes from VPNs.** See the [Impossible Travel runbook](../01-runbooks/impossible-travel.md)

## Checking an IP

Use **Spur** to see whether an IP is a VPN, proxy, or Tor and which service it belongs to. Use the **dan.me.uk exit node list** to confirm Tor. Links are in [Helpful tools](README.md#helpful-tools), along with a reminder about what's safe to paste into public sites.

> **Tor tip:** The full node list includes relays that never send traffic to websites. For checking a sign in or connection, use the exit node list.
