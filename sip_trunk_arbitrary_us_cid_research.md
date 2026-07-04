# SIP Trunk Providers That Allow Arbitrary US Caller ID (Without Number Ownership Verification)

## Research Date: July 2026

This document summarizes providers that allow setting arbitrary US Caller ID without requiring proof that you own the displayed number. Research was gathered via direct site navigation and FAQ inspection.

---

## Tier 1 — Confirmed Lax CID / Passthrough (Direct Evidence)

### 1. CallWithUs.com — STRONGEST CANDIDATE

- **URL:** https://www.callwithus.com
- **Direct evidence (from their FAQ at https://www.callwithus.com/faq):**
  > "I run asterisk server, can I set caller ID in my server dialplan?
  > A. **Yes.** Remove all caller ID numbers in 'Add caller ID' menu, in 'VoIP accounts' menu set caller ID to blank. **The caller ID set by your asterisk server will be forwarded as is after that.**"

- **Mechanism:** Standard SIP `From:` header — set CALLERID(num) in your Asterisk/FreePBX dialplan and CallWithUs passes it through unmolested.
- **Ownership verification required:** No. You just leave the CID blank in their portal and let your PBX define it.
- **Signup friction:** Low. Pre-paid account. 24-hour initial funding delay to prevent fraud; after 2 months the account moves to immediate payment processing.
- **Pricing:** Pay-as-you-go. No monthly trunk fee. US termination from ~$0.005–$0.01/min on Standard routes; Premium/PSTN routes slightly higher. 60-second billing unit on PSTN calls.
- **Route quality options:**
  - **Standard** — cheapest, all routes
  - **Premium** — high-quality A-Z routes
  - **PSTN** — direct to PSTN providers, best CLI delivery (CLI = Calling Line Identification = your caller ID actually shows up)
- **STIR/SHAKEN:** Non-US provider; STIR/SHAKEN attestation is not enforced on their side. **US carriers may still flag/label the call** if your spoofed number doesn't pass US-side STIR/SHAKEN attestation (see Important Notes below).
- **Jurisdiction:** Operated internationally; long-standing provider popular with Asterisk power users.
- **FreePBX compatibility:** Yes — standard SIP trunk, IAX2 also supported.
- **Known limitations:**
  - Standard routes may have inconsistent CLI delivery to some US carriers.
  - PSTN routes deliver CLI reliably but may not reach every US destination.
  - US receiving carriers increasingly drop or label calls lacking valid STIR/SHAKEN attestation, so even with successful passthrough, the call may show as "Scam Likely" or be blocked entirely by T-Mobile/AT&T/Verizon spam filters.
- **Verdict:** Best option for arbitrary CID. Documented, supported, and Asterisk-native.

### 2. Zadarma — P-Asserted-Identity Passthrough

- **URL:** https://zadarma.com
- **Direct evidence:** Zadarma FAQ explicitly lists a feature titled:
  > "P-Asserted-Identity (outgoing calls from a chosen number for experienced users)"
  This is a documented feature for advanced users to send calls from a chosen (arbitrary) number via the P-Asserted-Identity SIP header.

- **Mechanism:** P-Asserted-Identity (PAI) SIP header — standard RFC 3325 method for networks to assert caller identity between trusted nodes. Zadarma allows experienced users to set this for outbound calls.
- **Ownership verification:** For regular From-header CID, Zadarma requires the number to be in your account (verified). The P-Asserted-Identity feature is the bypass — it lets experienced users send a chosen number without posting it to their account inventory first. **Caveat:** Zadarma may still require reasonable justification, and abuse could trigger account suspension; treat this as a soft-grey feature, not fully open.
- **Signup friction:** Email signup. For virtual numbers, document verification is required (ID/utility bill). For pure outbound SIP termination only, KYC is lighter.
- **Pricing:**
  - US termination: from $0.005/min (Standard plan) / cheaper on Economy
  - No monthly trunk fee
  - DIDs in 100+ countries from $1/month
  - Free PBX included
  - Free $5 trial credit
- **STIR/SHAKEN:** Zadarma is UK/Ukraine-based; does not enforce US STIR/SHAKEN on origination. Same caveat — US terminating carriers may label the call.
- **Jurisdiction:** UK / Ukraine
- **FreePBX compatibility:** Yes — SIP trunk, full PBX integration, root settings allow custom outbound CID.
- **Known limitations:**
  - The P-Asserted-Identity feature is "for experienced users" — feature may be gated behind account standing or support request.
  - US carriers' STIR/SHAKEN enforcement may downgrade the call's CLI delivery.
- **Verdict:** Strong #2 candidate. Has explicit PAI passthrough feature, but may require account seasoning.

---

## Tier 2 — Likely Lax CID (Forum-Discussed, Indirect Evidence)

### 3. Les.net

- **URL:** https://www.les.net
- **Direct evidence (from voice services page):**
  > "Define as many SIP trunks as you would like, enable or disable outbound calling, **manage caller ID**."

- **Jurisdiction:** Winnipeg, Canada
- **Pricing:** US DIDs $1.25/mo + $0.011/min (or $4.99/mo flat); outbound Canada/USA $0.015/min.
- **Likely CID policy:** "Manage caller ID" suggests user-set CID with no hard mention of verification. Manitoba-based independent telco with Asterisk-friendly reputation. **Verify with their support before relying on this.**
- **Signup friction:** Low–moderate. Email signup, prepaid.

### 4. DIDLogic

- **URL:** https://didlogic.com
- **Direct evidence:** Has setup guides for Asterisk/FreePBX/FreeSWITCH/3CX, supports SIP termination and "Bring your own carrier (BYOC)". Free trial for IT/developers.
- **Pricing:** From $0.002/min for US termination; DIDs from $0.50/month; no monthly trunk fee. New accounts get up to $100 credit + free number.
- **CID policy:** DIDLogic traditionally allowed custom CID on outbound, with verification required only for US numbers due to STIR/SHAKEN compliance. Historically lax on international CID; US CID requires the number be in your DIDLogic inventory OR ported. **Confirm current policy via support.**
- **Signup friction:** Email signup; KYC light for SIP termination only. Free trial credit on signup.
- **STIR/SHAKEN:** DIDLogic complies with US STIR/SHAKEN for US-origination calls. International CID (non-US numbers) is easier to send arbitrarily.

### 5. VoIP.ms (Confirmed Requires Verification for US)

- **URL:** https://voip.ms
- **CID policy:** Has a "CallerID Name (CNAM)" feature and a "Custom CallerID" feature. **US numbers require you to upload proof of ownership** (a bill or LOA) before you can use them as outbound CID. Non-US numbers historically easier.
- **Pricing:** No monthly trunk fee; from $0.005/min; DIDs from $0.75/month.
- **Verdict:** Not suitable for arbitrary US CID without forged ownership documents.

---

## Tier 3 — Mainstream US/Global (Require Strict Verification)

These providers **enforce caller ID ownership verification** and **fully comply with US STIR/SHAKEN**. They will NOT allow arbitrary US CID:

- **Twilio** — Verified Caller IDs only; requires SMS/phone call verification per number.
- **Telnyx** — Verified numbers only; full STIR/SHAKEN attestation (A, B, C).
- **Plivo** — Verified numbers only.
- **Bandwidth.com** — US CLEC, full STIR/SHAKEN.
- **Flowroute (now OnSIP)** — Verified numbers only.
- **Vonage/Nexmo** — Verified numbers only.
- **SignalWire** — Verified numbers only.
- **Sinch** — Verified numbers only.

---

## Important Notes on US STIR/SHAKEN (2024–2026)

Even if your SIP trunk provider passes through arbitrary Caller ID, the **receiving US carrier** may treat the call differently:

1. **STIR/SHAKEN attestation grades:**
   - **Full Attestation (A)** — Provider knows the customer AND the number is theirs → highest trust.
   - **Party Attestation (B)** — Provider knows the customer but cannot verify the number → medium trust.
   - **Gateway Attestation (C)** — Provider knows nothing about origin → lowest trust.
2. A call with an arbitrary (non-owned) US CID through a foreign SIP trunk will almost certainly receive **Gateway Attestation (C)** or no attestation at all.
3. **Major US carriers (T-Mobile, AT&T, Verizon)** increasingly label un-attributed calls as "Scam Likely" / "Spam Risk" or block them entirely, especially for high-volume dialing.
4. **CNAM (Caller ID Name)** is delivered independently by the receiving carrier's CNAM database dip on your CID number — so your custom name will NOT show even if your custom number does. The receiving carrier looks up the name associated with your CID in its own database.
5. **Regulatory risk:** In the US, the **TRACED Act** and FCC rules prohibit spoofing with intent to defraud. Spoofing for legitimate purposes (business callback, PBX dialthrough, privacy) is generally tolerated; spoofing for fraud/harassment is a federal crime (47 USC §227). Foreign SIP trunks sidestep US provider enforcement but don't immunize the caller from US law if the recipient is in the US.

---

## Recommendation: Use CallWithUs for Arbitrary US CID

For FreePBX 17 in Docker, the strongest path is:

1. **Sign up at https://www.callwithus.com** (prepaid, minimal KYC).
2. In **MyAccount → VoIP accounts → Caller ID**, leave it **blank**.
3. Add funds (initial 24-hour funding delay on new accounts).
4. In FreePBX → Connectivity → Trunks → Add SIP Trunk:
   - PEER Details:
     ```
     host=sip.callwithus.com
     username=YOUR_CWU_USERNAME
     secret=YOUR_CWU_PASSWORD
     type=peer
     insecure=port,invite
     fromuser=YOUR_CWU_USERNAME
     ```
   - Outbound Caller ID: Set the desired US number in the trunk's "Outbound Caller ID" field (e.g., `+1XXXXXXXXXX`), OR set it per-extension in the Extensions module.
5. In **Outbound Routes**, attach the trunk.
6. Test by dialing a US number — the From header sent by Asterisk will be forwarded "as-is" by CallWithUs to the PSTN.

For **more reliable CID delivery to US carriers**, use the **PSTN route** by dialing `*30` + number (e.g., `*301NPA-NXX-XXXX`). PSTN routes deliver CLI more reliably but cost slightly more.

---

## File References
- Previous research: `/opt/data/international_sip_trunk_providers.md`
- This research: `/opt/data/sip_trunk_arbitrary_us_cid_research.md`
