---
layout: respec
title: did:compositejwk Method v0.1
respec: >
  {
    "name": "did:compositejwk",
    "status": "CG-DRAFT",
    "latest": "https://cybersecurity-links.github.io/did:compositejwk/v0.1/",
    "repository": "https://github.com/Cybersecurity-LINKS/did:compositejwk",
    "issues": "https://github.com/Cybersecurity-LINKS/did:compositejwk/issues",
    "group": {
      "name": "Credentials Community Group",
      "url": "https://www.w3.org/community/credentials/",
      "list": "public-credentials",
      "patentUri": "https://www.w3.org/community/about/agreements/cla/"
    },
    "editors": [
      {
        "name": "Andrea Vesco",
        "url": "https://www.linkedin.com/in/avesco",
        "company": "LINKS Foundation",
        "companyURL": ""
      },
      {
        "name": "Alessio Claudio",
        "url": "https://www.linkedin.com/in/alessio-claudio-ab260923b",
        "company": "LINKS Foundation",
        "companyURL": ""
      }
    ],
    "bibliography": {
    }
  }
---

[//]: # (add month/day values to references)

<section id="abstract">
  <p>
   This specification describes the did:compositejwk method to deal with Post-Quantum/Traditional (PQ/T) hybrid signatures in Verifiable Credentials.
  </p>
</section>

<section id="sotd">
  <p>
    This specification was published by the Cybersecurity Research Group @ LINKS Foundation. 
  </p>
  <p>  
    This specification is experimental, do not use it in any production setting. 
  </p>
  <p>  
    <a href="https://github.com/Cybersecurity-LINKS/did:compositejwk/issues"> GitHub Issues </a> are preferred for discussion of this specification.
  </p>
</section>

<style>
.red43 {
  color: red;
}
</style>

## DID Method Specification

`did:compositejwk` is a deterministic transformation of a `compositeJwk` into a DID Document.
### compositeJwk

The `compositeJwk` is a new <a href="https://www.w3.org/TR/did-core/#verification-material">Verification Material</a> property introduced to handle Post-Quantum/Traditinal (PQ/T) hybrid keys. This object contains the PQ and traditional public keys, both JWK encoded, and the algId string representing the name of algorithms used to generate the hybrid signature.

```json
"compositeJwk": {
  "algId": ".. composite key OID ..",
  "pqPublicKey": {
     ".. PQ JWK encoded key .."
  },
  "traditionalPublicKey": {
    ".. Traditional JWK encoded key .."
  }
}
```

<pre class="example nohighlight" title="A compositeJwk example">
"compositeJwk": {
  "algId": "id-MLDSA44-Ed25519-SHA512",
  "pqPublicKey": {
    "kty": "ML-DSA",
    "alg": "ML-DSA-44",
    "kid": ".. key thumbprint ..",
    "pub": ".. encoded public key .."
  },
  "traditionalPublicKey": {
    "crv": "Ed25519",
    "x": ".. x coordinate ..",
    "kty": "OKP",
    "kid": ".. key thumbprint .."
  }
}
</pre>


### DID Format

<pre class="example nohighlight" title="did format">

did-compositejwk-format   := did:compositejwk:base64url-value
base64url-value  := [A-Za-z0-9_-]+
</pre>

The `base64url-value` is a <a href="https://datatracker.ietf.org/doc/html/rfc4648#section-5">base64url</a> encoded `compositeJwk`.

### DID Operations

#### Create

To create the DID:

1. Generate or load a `compositeJwk`
1. Serialize it into a UTF-8 string
1. Encode that string using base64url
1. Attach the prefix `did:compositejwk:`

To create the DID Document:

The `compositeJwk` is used to generate the <a href="https://www.w3.org/TR/did-core/#dfn-did-documents">DID Document</a> in the following form with the
<a href="https://datatracker.ietf.org/doc/html/rfc4648#section-5">base64url</a> encoded value replacing `${base64url-value}`, and the `compositeJwk` structure replacing `${composite-jwk}`:

<pre class="example nohighlight" title="DID Document example">
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/jws-2020/v1"
  ],
  "id": "did:compositejwk:${base64url-value}",
  "verificationMethod": [
    {
      "id": "did:compositejwk:${base64url-value}#0",
      "type": "CompositeJsonWebKey",
      "controller": "did:compositejwk:${base64url-value}",
      "compositeJwk": "${composite-jwk}"
    }
  ]
}
</pre>

To create the DID URL:

Since `did:compositejwk` only contains a single composite public key (a PQ and a Traditional public key), the DID URL fragment identifier is always a fixed `#0` value. If the PQ and/or Traditional JWK contains a `kid` value, it is _not_ used as the reference, `#0` is the only valid value.

#### Read
1. Remove the prefix `did:compositejwk:`
2. Decode the remaining string using [base64url](https://datatracker.ietf.org/doc/html/rfc4648#section-5)
3. Parse the decoded string as UTF-8 JSON
4. Validate the `compositeJwk` properties
5. Generate a DID Document using the composite public key 

#### Update
Not supported.

#### Deactivate
Not supported.

## Security and Privacy Considerations

Since the `did:compositejwk` method is similar to the `did:jwk` method, see also [did:jwk](https://github.com/quartzjer/did-jwk/blob/main/spec.md#security-and-privacy-considerations)


<section id='conformance'>
  <!-- This section is filled automatically by ReSpec. -->
</section>