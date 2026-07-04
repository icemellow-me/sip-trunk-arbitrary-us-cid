# SIP Trunk Providers — Arbitrary US Caller ID (July 2026)

Research on SIP trunk providers that allow setting an arbitrary US phone number as outbound Caller ID **without requiring proof of number ownership**.

## Background

The previous research (`sip_trunk_arbitrary_us_cid_research.md`) identified **CallWithUs.com** as the strongest candidate — their FAQ explicitly states:

> "I run asterisk server, can I set caller ID in my server dialplan?
> A. **Yes.** Remove all caller ID numbers in 'Add caller ID' menu, in 'VoIP accounts' menu set caller ID to blank. The caller ID set by your asterisk server will be forwarded as is after that."

Unfortunately, **CallWithUs is no longer accepting new customers** (no signup link anywhere on their homepage, only Home | MyAccount | FAQ | AboutUs).

## Current Top Candidates (Currently Open for Signup)

### Tier 1 — Documented Passthrough / PAI

1. **Zadarma** — https://zadarma.com — Signup OPEN. Has documented feature titled `"P-Asserted-Identity (outgoing calls from a chosen number for experienced users)"`. UK/Ukraine jurisdiction. Free $5 trial credit. US termination from $0.005/min.

2. **SIPTraffic** — https://www.siptraffic.com — Signup OPEN (business-style form). Netherlands-based wholesale SIP termination. Passes through `From:` header CID by default. Per-second billing.

### Tier 2 — Likely Lax CID (verify with support)

3. **Les.net** — Canada, voice page says `"manage caller ID"`. Email sales@les.net to confirm current policy.

4. **DIDLogic** — historically lax on international CID, but US CID now requires verification due to STIR/SHAKEN.

### Tier 3 — Confirmed Strict (won't work for arbitrary US CID)

Twilio, Telnyx, Plivo, Bandwidth, Flowroute, Vonage/Nexmo, SignalWire, Sinch, VoIP.ms (US CID requires ownership proof).

## FreePBX 17 Setup (Zadarma + P-Asserted-Identity)

```asterisk
; In FreePBX trunk PEER Details
host=sip.zadarma.com
username=YOUR_ZADARMA_SIP
secret=YOUR_ZADARMA_PASSWORD
type=peer
insecure=port,invite
fromuser=YOUR_ZADARMA_SIP
```

```asterisk
; In dialplan (or use the trunk's Outbound Caller ID field)
Set(CALLERID(num)=1XXXXXXXXXX) ; the US number you want to display
Set(PJSIP_HEADER(send,P-Asserted-Identity)=<sip:1XXXXXXXXXX@sip.zadarma.com>)
```

## US STIR/SHAKEN Caveat

A spoofed US CID through any foreign SIP trunk will receive Gateway Attestation (C) or no attestation. T-Mobile, AT&T, Verizon increasingly label/flag unattested calls as "Scam Likely" or block them entirely. CNAM (Caller ID Name) is delivered by the *receiving* US carrier dipping its own CNAM database on your displayed number — your custom name will NOT show.

## Files

- `active_sip_providers_arbitrary_us_cid.md` — Full research document with provider-by-provider breakdown, FreePBX setup, and STIR/SHAKEN caveats.
- `sip_trunk_arbitrary_us_cid_research.md` — Earlier phase of research documenting CallWithUs and the broader landscape.

## Disclaimer

For educational and legitimate-business-callback / PBX dialthrough use cases. Spoofing with intent to defraud is a US federal crime (47 USC §227 / TRACED Act). Foreign SIP trunks sidestep US provider enforcement but do not immunize the caller from US law if the call terminates to a US subscriber.

## Research Date

July 2026. Signup statuses and CID policies change frequently — always test with the provider's free trial credit before committing larger balances.
