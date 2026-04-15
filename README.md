# EsReal vURL Public µAPI

EsReal Protocol developed by Ciram Consulting BV
EsReal Protocol Open Source for EsReal ORG

This Protcol is used in products for EsReal BV created by Ciram Consulting BV

EsReal vURLs are **Verified URLs**: cryptographically protected links
that point to structured, verifiable data.

This repository documents the **public HTTP interface** to read and
verify a vURL, so that external products (websites, apps, back-office
systems, ...) can:

-   Scan or paste a vURL.
-   Call a **simple HTTP endpoint**.
-   Receive a **validated JSON payload** plus verification metadata.
-   Automatically pre-fill forms or trigger business logic based on
    trusted data.

------------------------------------------------------------------------

## 1. High-level overview

A vURL looks like this:

    https://esreal.org/v/personalbasic/?obj=f0caa95e-a3d8-4ec4-8a1a-d0f2ab4492cc&datahash=38ce1d6aba06b72bc6c4786a43cba14616d0d2f1d870cc0576bd21bcdfdbd704&ts=1764231029&sig=wGg_aujhz6VjW0TZISAqfyuLU-4ivBvarYSigzpYdeE

Key ideas:

-   `/v/personalbasic/` ⇒ `personalbasic` is the **vURL type** (schema
    of the data).
-   `obj`, `datahash`, `ts`, `sig` ⇒ cryptographic parameters that make
    the vURL **tamper-evident**.
-   When you add `&json=true`, the URL behaves like a **mini API** and
    returns a structured JSON document *if and only if* all internal
    validation checks succeed.

------------------------------------------------------------------------

## 2. URL format

### 2.1 Base pattern

    https://esreal.org/v/{type}/?obj={uuid}&datahash={hex}&ts={timestamp}&sig={signature}

-   `{type}` = vURL data type.
-   Required parameters:
    -   `obj`
    -   `datahash`
    -   `ts`
    -   `sig`

### 2.2 JSON mode

Append:

    &json=true

This will return a validated JSON payload when the vURL is valid.

------------------------------------------------------------------------

## 3. Response structure

Top-level response:

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

------------------------------------------------------------------------

## 4. personalbasic schema example

  Field           Description
  --------------- ---------------
  first_name      First name
  last_name       Last name
  email           Email
  birth_date      Birth date
  country         Country
  postal_code     Postal code
  city            City
  verifier_name   Verifier name
  verifier_type   Verifier type

------------------------------------------------------------------------

## 5. Validation rules

Always verify:

-   All values in `checks` must be `true`
-   `reliability` must be `"100%"`

If not: treat the data as **untrusted**.

------------------------------------------------------------------------

## 6. Example usage (JavaScript)

``` js
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

------------------------------------------------------------------------

## 7. Privacy & compliance

-   Store only required data.
-   Follow GDPR and local privacy laws.
-   Never share vURL data without consent.

------------------------------------------------------------------------

## 8. Summary

-   vURLs are cryptographically verifiable.
-   `json=true` turns them into a public validation API.
-   External systems can safely auto-fill data when checks pass.
