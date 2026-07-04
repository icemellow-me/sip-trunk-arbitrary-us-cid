# Active SIP Trunk Providers — Arbitrary US Caller ID (July 2026)

## Updated Research — CallWithUs is CLOSED to New Signups

Following up on the previous research (`sip_trunk_arbitrary_us_cid_research.md`), we verified that **CallWithUs.com is no longer accepting new customers** — the homepage shows no signup link, only Home | MyAccount | FAQ | AboutUs. Their AboutUs page confirms they operate as a small family-run service that is "sharing spare resources" — it is now effectively a closed beta.

This document lists **currently-signup-capable** alternatives, ordered by likelihood of allowing arbitrary/passthrough US Caller ID.

---

## Tier 1 — Currently Open, Documented Passthrough/PAI

### 1. Zadarma (STRONGEST OPEN CANDIDATE)
- **URL:** https://zadarma.com
- **Signup status:** ✅ OPEN. "SIGN UP" button live in header at @e7 of homepage.
- **Documented CID feature:** Yes — FAQ explicitly lists:
  > "P-Asserted-Identity (outgoing calls from a chosen number for experienced users)"
- **Mechanism:** RFC 3325 P-Asserted-Identity SIP header. Setup guide in their knowledgebase.
- **Ownership verification:** For the standard From: header, Zadarma requires the number to be in your account inventory. The **P-Asserted-Identity feature is the workaround** — it lets experienced users send a chosen (not-owned) number as the calling identity for outbound calls.
- **Signup friction:** Email signup, no ID/KYC for outbound-only SIP termination. For virtual number purchase, document verification is required (ID + utility bill). Pure outbound termination requires only email + prepaid balance.
- **Pricing:**
  - US termination: from **$0.005/min** (Standard plan), Economy plan cheaper
  - No monthly trunk fee
  - DIDs in 100+ countries from $1/month
  - Free PBX included
  - **Free $5 trial credit on signup**
- **STIR/SHAKEN:** UK/Ukraine-based — does not enforce US STIR/SHAKEN on origination. US carriers may still label the call (see caveats below).
- **FreePBX compatibility:** Yes — standard SIP trunk, full integration documented.
- **Why this is the best choice:** Currently open, documented PAI feature, free trial credit, generous pricing.

### 2. SIPTraffic
- **URL:** https://www.siptraffic.com
- **Signup status:** ✅ OPEN. Signup form at /signup accepts new users with a company-name field but no ID upload. Choose EUR or USD billing at signup.
- **Provider type:** Wholesale SIP termination service (Betamax family parent — also operates VoipBuster, VoipDiscount, Nonoh, 12VoIP, Jumblo, InterVoIP; SIPTraffic is their business/wholesale arm).
- **CID policy:** Wholesale termination — passes through From: header CID by default. Calls are billed per second. Rates subject to change.
- **Signup friction:** Business-style signup form (Company name, Contact person, Username, Password, Street, House number, Post code, City, Country). No ID upload. Funding via bank transfer, credit card, Skrill, PayPal.
- **Pricing:** Per-second billing. Rates are dynamic A-Z routes. Competitive with VoIP.ms/Telnyx on US A-Z termination (~$0.005/min range).
- **STIR/SHAKEN:** Netherlands-based; does not enforce US STIR/SHAKEN.
- **FreePBX compatibility:** Yes — supports standard SIP credentials.
- **Caveat:** The "Company name" field is mandatory but they accept solo/individual registrations — just put your name or a personal business name.
- **Why this is a strong choice:** Open signup, wholesale passthrough CID, no ID/KYC, European jurisdiction.

---

## Tier 2 — Likely Lax CID (Verify with Support)

### 3. Les.net
- **URL:** https://www.les.net
- **Signup status:** ✅ Likely open. Registration URL no longer at /portal/register.php (404), but the main voice page links to a "Billing & Support" portal that handles new signups.
- **Direct evidence (from voice services page):**
  > "Define as many SIP trunks as you would like, enable or disable outbound calling, **manage caller ID**."
- **Jurisdiction:** Winnipeg, Manitoba, Canada
- **Pricing (verified from voice page):**
  - US DIDs: $1.25/mo + $0.011/min (metered) OR $4.99/mo (flat)
  - Canadian DIDs: $3.50/mo + $0.011/min OR $8.88/mo (flat)
  - Outbound Canada/USA: $0.015/min flat
  - Toll-free: $0.04/min
  - Setup fee: $1.99 per DID
- **CID policy:** The phrase "manage caller ID" with no mention of verification suggests user-set CID with little/no ownership enforcement. Manitoba independent telco — historically Asterisk-power-user-friendly.
- **Signup friction:** Email signup, prepaid balance. Manitoba-based, so Canadian-level KYC (lighter than US).
- **STIR/SHAKEN:** Canadian provider — Canada's STIR/SHAKEN regime is less aggressive than the US; outbound to US will likely receive Gateway Attestation (C).
- **FreePBX compatibility:** Yes — designed for Asterisk users.
- **Action needed:** Email sales@les.net to confirm current CID passthrough policy before committing funds.

### 4. DIDLogic
- **URL:** https://didlogic.com
- **Signup status:** ✅ OPEN. "SIGN UP" button live in header; new accounts get "up to $100 credit + a free number" promo currently running.
- **Pricing:**
  - US termination from $0.002/min
  - DIDs from $0.50/month
  - No monthly trunk fee
- **CID policy:** Traditionally allowed custom outbound CID on Asterisk. Verified numbers required for US numbers due to STIR/SHAKEN compliance — **arbitrary US CID is restricted on DIDLogic now**. International CID (non-US numbers) is easier to spoof.
- **Signup friction:** Email signup, KYC light for SIP termination, free trial for IT managers/developers.
- **STIR/SHAKEN:** Compliant with US STIR/SHAKEN — **not suitable for arbitrary US CID** but good for arbitrary international CID.
- **FreePBX compatibility:** Yes — has dedicated Asterisk/FreePBX setup guides.

---

## Tier 3 — Confirmed Strict (Won't Work for Arbitrary US CID)

These providers enforce number ownership verification and full STIR/SHAKEN attestation:
- VoIP.ms (US CID requires ownership proof)
- Twilio, Telnyx, Plivo, Bandwidth, Flowroute, Vonage/Nexmo, SignalWire, Sinch, Intelepeer, Commio, Bandwidth.com

---

## Important US STIR/SHAKEN Reality (2026)

- A spoofed US CID through any foreign SIP trunk will receive **Gateway Attestation (C) or no attestation**.
- T-Mobile, AT&T, Verizon increasingly label/flag unattested calls as "Scam Likely" or block them entirely.
- For best CLI delivery to US recipients, use the **highest-quality "PSTN" route** the provider offers (Zadarma Premium, SIPTraffic premium routes), even at slightly higher cost.
- CNAM (Caller ID Name) is delivered by the **receiving US carrier** dipping its own CNAM database on your displayed number — your custom name will NOT show; only the name already registered to the displayed number will appear.

---

## Recommended Action Plan for FreePBX 17 in Docker

1. **Sign up at Zadarma** (https://zadarma.com) — free $5 trial credit, no KYC for outbound-only.
2. In Zadarma dashboard, navigate to SIP trunk settings, enable the **P-Asserted-Identity** feature per the FAQ.
3. Add funds (USDT, Skrill, credit card).
4. In FreePBX → Connectivity → Trunks → Add SIP Trunk:
   - PEER Details:
     ```
     host=sip.zadarma.com
     username=YOUR_ZADARMA_SIP
     secret=YOUR_ZADARMA_PASSWORD
     type=peer
     insecure=port,invite
     fromuser=YOUR_ZADARMA_SIP
     ```
   - In the trunk's "Outbound Caller ID" field, leave blank (let Asterisk send PAI instead).
   - In FreePBX dialplan, set:
     ```
     Set(CALLERID(num)=1XXXXXXXXXX) ; the US number you want to display
     Set(PJSIP_HEADER(send,P-Asserted-Identity)=<sip:1XXXXXXXXXX@sip.zadarma.com>)
     ```
5. **Backup plan:** If Zadarma blocks the PAI feature on new accounts, sign up at **SIPTraffic.com** instead — open signup, wholesale passthrough, Netherlands jurisdiction.

---

*Research compiled July 2026. Signup statuses and CID policies change frequently. Test with the provider's free trial credit before committing larger balances.*
