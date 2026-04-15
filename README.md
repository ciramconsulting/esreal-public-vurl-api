EsReal vURL Public µAPI
> **Protocol developed by [Ciram Consulting BV](https://ciram-consulting.com)**
> Published as Open Source under the EsReal ORG initiative.
> Used in products for **EsReal BV**, created and maintained by **Ciram Consulting BV**.
---
About
This repository documents the public HTTP interface to read and verify a vURL (Verified URL).
EsReal vURLs are cryptographically protected links that point to structured, verifiable data. External products - websites, apps, back-office systems - can use this interface to:
Scan or paste a vURL
Call a simple HTTP endpoint
Receive a validated JSON payload plus verification metadata
Automatically pre-fill forms or trigger business logic based on trusted data
---
Developed by
	
Developer	Ciram Consulting BV
Protocol owner	EsReal BV
Open Source initiative	EsReal ORG
License	Open Source - see LICENSE
> This open source repository is maintained by Ciram Consulting BV as part of the EsReal ecosystem.
> Commercial products built on this protocol are developed by Ciram Consulting BV for EsReal BV.
---
1. High-level overview
A vURL looks like this:
```
https://esreal.org/v/personalbasic/?obj=f0caa95e-a3d8-4ec4-8a1a-d0f2ab4492cc&datahash=38ce1d6aba06b72bc6c4786a43cba14616d0d2f1d870cc0576bd21bcdfdbd704&ts=1764231029&sig=wGg_aujhz6VjW0TZISAqfyuLU-4ivBvarYSigzpYdeE
```
Key ideas:
`/v/personalbasic/` → `personalbasic` is the vURL type (schema of the data)
`obj`, `datahash`, `ts`, `sig` → cryptographic parameters that make the vURL tamper-evident
Adding `&json=true` turns the URL into a mini API that returns a structured JSON document if and only if all internal validation checks pass
---
2. URL format
2.1 Base pattern
```
https://esreal.org/v/{type}/?obj={uuid}&datahash={hex}&ts={timestamp}&sig={signature}
```
Parameter	Required	Description
`type`	✓	vURL data type / schema
`obj`	✓	Object UUID
`datahash`	✓	Cryptographic hash of the data
`ts`	✓	Unix timestamp
`sig`	✓	Cryptographic signature
2.2 JSON mode
Append `&json=true` to any vURL to activate API mode:
```
https://esreal.org/v/personalbasic/?obj=...&json=true
```
Returns a validated JSON payload when the vURL is valid.
---
3. Response structure
```json
{
  "vurldata": { },
  "checks": { },
  "reliability": "100%",
  "transaction": "...",
  "datahash_opreturn": "...",
  "confirmations": "23193",
  "blockhash": "...",
  "chain": "DGB"
}
```
---
4. `personalbasic` schema
Field	Description
`first_name`	First name
`last_name`	Last name
`email`	Email address
`birth_date`	Date of birth
`country`	Country
`postal_code`	Postal code
`city`	City
`verifier_name`	Name of the verifying party
`verifier_type`	Type of the verifying party
---
5. Validation rules
Always verify the following before using vURL data:
All values in `checks` must be `true`
`reliability` must be `"100%"`
> ⚠️ If either condition is not met, treat the data as **untrusted** and do not use it.
---
6. Example usage
JavaScript
```js
async function fetchVurl(vurl) {
  const url = vurl + "&json=true";
  const res = await fetch(url);
  const data = await res.json();

  const trusted = data.reliability === "100%" &&
    Object.values(data.checks).every(Boolean);

  if (!trusted) throw new Error("vURL not trusted");

  return data.vurldata;
}
```
---
7. Privacy & compliance
Store only the data that is strictly required for your use case
Follow GDPR and applicable local privacy laws
Never share vURL data with third parties without explicit user consent
---
8. Summary
	
What is a vURL?	A cryptographically verifiable URL pointing to structured data
How to use it?	Append `&json=true` and call the URL as a REST endpoint
When is data trusted?	When all `checks` are `true` and `reliability` is `"100%"`
---
Contact & support

Who built this?	Ciram Consulting BV for EsReal BV

For questions about this protocol or integration support:
Ciram Consulting BV
Developer of the EsReal Protocol
🌐 ciram-consulting.com
For EsReal product information:
🌐 esreal.be
