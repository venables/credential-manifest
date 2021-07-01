Credential Manifest
==================

**Specification Status:** Strawman

**Latest Draft:**
  [identity.foundation/credential-manifest](https://identity.foundation/credential-manifest)

**Editors:**
~ [Daniel Buchner](https://www.linkedin.com/in/dbuchner/) (Microsoft)
~ [Brent Zundel](https://www.linkedin.com/in/bzundel/) (Evernym)
~ [Jace Hensley](https://www.linkedin.com/in/jacehensley/) (Bloom)
~ [Daniel McGrogan](https://www.linkedin.com/in/dtmcgrogan/) (Workday)

<!-- -->
**Participate:**
~ [GitHub repo](https://github.com/decentralized-identity/credential-manifest)
~ [File a bug](https://github.com/decentralized-identity/credential-manifest/issues)
~ [Commit history](https://github.com/decentralized-identity/credential-manifest/commits/master)

------------------------------------

## Abstract

For User Agents (e.g. wallets) and other service that wish to engage with Issuers to acquire credentials, there must exist a mechanism for assessing what inputs are required from a Subject to process a request for credential(s) issuance. The _Credential Manifest_ is a common data format for describing the inputs a Subject must provide to an Issuer for subsequent evaluation and issuance of the credential(s) indicated in the Credential Manifest.

_Credential Manifests_ do not themselves define the contents of the output credential(s), the process the Issuer uses to evaluate the submitted inputs, or the protocol Issuers, Subjects, and their User Agents rely on to negotiate credential issuance.

## Status of This Document

Credential Manifest is a draft specification being developed within the [Decentralized Identity Foundation](https://identity.foundation) (DIF), and intended for ratification as a DIF recommended data format. This spec will be updated to reflect relevant changes, and participants are encouraged to contribute at the following repository location: https://github.com/decentralized-identity/credential-manifest


## Terminology

[[def:Decentralized Identifiers, Decentralized Identifier, DID]]
~ Unique ID URI string and PKI metadata document format for describing the cryptographic keys and other fundamental PKI values linked to a unique, user-controlled, self-sovereign identifier in a target system (i.e. blockchain, distributed ledger).

[[def:Claim, Claims]]
~ An assertion made about a [[ref:Subject]]. Used as an umbrella term for
Credential, Assertion, Attestation, etc.

[[def:Issuer, Issuers]]
~ Issuers are entities that issue credentials to a [[ref:Holder]].

[[def:Holder, Holders]]
~ Holders are entities that recieve credentials from [[ref:Issuers]], possibly first submitting proofs the the Issuer to satisfy the requirements described in a Presentation Definition.

[[def:Output Descriptor, Output Descriptors]]
~ Output Descriptors are used by an Issuer to describe the credentials they are offering to a [[ref:Holder]]. See [Output Descriptor](#output-descriptor)

[[def:Output Descriptor Object, Output Descriptor Objects]]
~ Output Descriptor Objects are populated with properties describing the [[ref:Claims]] the [[ref:Issuer]] is offering the [[ref:Holder]]

[[def:Credential Application, Credential Applications]]
~ Credential Application are objects embedded within target claim negotiation formats that pass information from the [[ref:Holder]] to the [[ref:Issuer]]. See [Credential Application](#credential-application)

[[def:Display Mapping Object, Display Mapping Objects]]
~ Display Mapping Objects are used to render UI based on information from and about a [[ref:Claim]]. See [Display Mapping Object](#display-mapping-object)

[[def:Labeled Display Mapping Object, Labeled Display Mapping Objects]]
~ ref:Labeled Display Mapping Objects extend from [[ref:Display Mapping Objects]]. See [Labeled Display Mapping Object](#labeled-display-mapping-object)

[[def:Credential Fulfillment, Credential Fulfillments]]
~ Credential Fulfillments are objects embedded within target claim negotiation formats that unify the presentation of [[ref:Claims]] to a [[ref:Holder]] in accordance with the output an [[ref:Issuer]] specified in a [[ref:Credential Manifest]]. See [Credential Fulfillment](#credential-fulfillment).

## Credential Manifest

_Credential Manifests_ are a resource format that defines preconditional requirements, Issuer style preferences, and other facets User Agents utilize to help articulate and select the inputs necessary for processing and issuance of a specified credential.


<section>

::: example Credential Manifest - All features exercised
```json
[[insert: ./test/credential-manifest/all_features.json]]
```
:::

</section>

### General Composition

_Credential Manifests_ are JSON objects composed as follows:

- The object ****MUST**** contain an `issuer` property, and its value ****MUST**** be an object composed as follows:
    - The object ****MUST**** contain a `id` property, and its value ****MUST**** be a valid URI string that identifies who the issuer of the credential(s) will be.
    - The object ****MAY**** contain a `name` property, and its value ****MUST**** be a string that ****SHOULD**** reflect the human-readable name the Issuer wishes to be recognized by.
    - The object ****MAY**** contain a `styles` property, and its value ****MUST**** be an object or URI, as defined by the Entity Styles specification.
- The object ****MUST**** contain an `output_descriptors` property. It's vault ****MUST**** be an array of Output Descriptor Objects, the composition of which are described in the [`Output Descriptor`](#output-descriptor) section below
- The [[ref:Credential Manifest]] ****MAY**** include a `format` property. If present, its value ****MUST**** be the same structure as [Presentation Definition's `format` property](https://identity.foundation/presentation-exchange/#presentation-definition). This property informs the [[ref:Holder]] of the [[ref:Calim]] format the [[ref:Issuer]] can issuer in.
    For example:

```json
{
  "credential_manifest": {
    "id": "WA-DL-CLASS-A",
    "output_descriptors": [],
    "format": {
      "jwt": {
        "alg": ["EdDSA", "ES256K", "ES384"]
      },
      "jwt_vc": {
        "alg": ["ES256K", "ES384"]
      },
      "jwt_vp": {
        "alg": ["EdDSA", "ES256K"]
      },
      "ldp_vc": {
        "proof_type": [
          "JsonWebSignature2020",
          "Ed25519Signature2018",
          "EcdsaSecp256k1Signature2019",
          "RsaSignature2018"
        ]
      },
      "ldp_vp": {
        "proof_type": ["Ed25519Signature2018"]
      },
      "ldp": {
        "proof_type": ["RsaSignature2018"]
      }
    }
  }
}
```

- The object ****MAY**** contain a `presentation_definition` object, and its value ****MUST**** be a [Presentation Definition](https://identity.foundation/presentation-exchange/#presentation-definition) object, as defined by the [DIF Presentation Exchange](https://identity.foundation/presentation-exchange) specification.


### Output Descriptor

[[ref:Output Descriptors]] are objects used to describe the [[ref:Claims]] a [[ref:Issuer]] if offering to a [[ref:Holder]].

[[ref:Output Descriptor Objects]] contain schema URI that links to the schema of the offered output data, and information about how to display the output to the Holder.

<section>

::: example Output Descriptors - Simple Example
```json
[[insert: ./test/output-descriptors/simple.json]]
```
:::

</section>

#### Output Descriptor Object

[[ref:Output Descriptor Objects]] are composed as follows:

- The [[ref:Output Descriptor Object]] ****MUST**** contain an `id` property. The value of the `id` property ****MUST**** be a string that does not conflict with the `id` of another [[ref:Output Descriptor Object]] in the same [[ref:Credential Manifest]].
- The [[ref:Output Descriptor Object]] ****MUST**** contain a `schema` property, and its value ****MUST**** be an array composed of schema objects for the schema(s) of the credentials to be issued.
- The [[ref:Output Descriptor Object]] ****MAY**** contain a `name` property, and if present its value ****SHOULD**** be a human-friendly name that describes what the credential represents.
- The [[ref:Output Descriptor Object]] ****MAY**** contain a `description` property, and if present its value ****MUST**** be a string that describes what the credential is in greater detail.
- The [[ref:Output Descriptor Object]] ****MAY**** contain a `style` property, and its value ****MUST**** be an object or URI, as defined by the [DIF Entity Styles](https://identity.foundation/credential-manifest/wallet-rendering/#entity-styles) specification.
- The [[ref:Output Descriptor Object]] ****MAY**** contain a `display` property, and its value ****MUST**** be an object composed as defined in the [DIF Data Display](https://identity.foundation/credential-manifest/wallet-rendering/#data-display) specification.

:::

</section>

## Resource Location

Credential Manifests ****SHOULD**** be retrievable at known, semantic locations that are generalized across all entities, protocols, and transports. This specification does not stipulate how Credential Manifests must be located, hosted, or retrieved, but does advise that Issuers ****SHOULD**** make their Credential Manifests available via an instance of the forthcoming semantic personal datastore standard being developed by DIF, W3C, and other groups (e.g. Identity Hubs).

## Credential Application

Credential Application are objects embedded within target claim negotiation formats that pass information from the [[ref:Holder]] to the [[ref:Issuer]]

- The [[ref: Credential Application]] object ****MUST**** contain a `credential_application` property. Its value ****MUST**** be an object with the following properties:
  - The `credential_submission` object ****MUST**** contain an `id` property. The value of this property ****MUST**** be a unique identifier, such as a UUID
  - The `credential_submission` object ****MUST**** contain a `manifest_id` property. The value of this property ****MUST**** be the id of a valid Credential Manifest.
  - The `credential_submission` ****MUST**** have a `format` property if the related [[ref:Credential Manifest]] specifies a `format` property. Its value ****MUST**** be a _subset_ of the `format` property in the [[ref:Credential Manifest]] that this [[ref:Credential Submission]] is related to. This object informs the [[ref:Issuer]] which formats the [[ref:Holder]] wants to recieve the [[ref:Claims]] in.
- The [[ref: Credential Application]] object ****MUST**** contain a `presentation_submission` property IF the related [[ref:Credential Manifest]] contains a `presentation_definition`. Its value ****MUST**** be be a valid [Presentation Submission](https://identity.foundation/presentation-exchange/#presentation-submission):

```
// NOTE: VP, OIDC, DIDComm, or CHAPI outer wrapper properties would be at outer layer
```

<section>

::: example Credential Application - Simple Example
```json
[[insert: ./test/credential-application/sample.json]]
```
:::

</section>

### Embed Targets

The following section details where the _Credential Application_ is to be embedded within a target data structure.

#### Embed Locations

The following are the locations at which the `credential_application` and, conditionally, the `presentation_submission` objects ****MUST**** be embedded for known target formats. For any location besides the top level of the embed target, the location is described in JSONPath syntax.

Target     | Location
---------- | --------
OpenID     | top-level
DIDComms   | `$.presentations~attach.data.json`
VP         | top-level
CHAPI      | `$.data`

### JSON Schema
The following JSON Schema Draft 7 definition summarizes the rules above:

<section>

::: example Credential Application - Schema
```json
[[insert: ./test/credential-application/schema.json]]
```
:::

## Credential Fulfillment

[[ref:Credential Fulfillments]] are objects embedded within target [[ref:Claim]] negotiation formats that express how the outputs presented as proofs to a [[ref:Holder]] are provided in accordance with the outpus specified in a [[ref:Credential Manifest]]. Embedded [[ref:Credential Fulfillment]] objects ****MUST**** be located within target data format as the value of a `credential_fulfillment` property, which is composed and embedded as follows:

- The `credential_fulfillment` object ****MUST**** be included at the top-level of an Embed Target, or in the specific location described in the [Embed Locations table](#embed-locations) in the [Embed Target](#embed-target) section below.
- The `credential_fulfillment` object ****MUST**** contain an `id` property. The value of this property ****MUST**** be a unique identifier, such as a [UUID](https://tools.ietf.org/html/rfc4122).
- The `credential_fulfillment` object ****MUST**** contain a `manifest_id` property. The value of this property ****MUST**** be the `id` value of a valid [[ref:Credential Manifest]].
- The `credential_fulfillment` object ****MUST**** include a `descriptor_map` property. The value of this property ****MUST**** be an array of _Output Descriptor Mapping Objects_, just like [Presentation Submission's](https://identity.foundation/presentation-exchange/#presentation-submission) `descriptor_map` property.

```
// NOTE: VP, OIDC, DIDComm, or CHAPI outer wrapper properties would be at outer layer
```
<section>

::: example Credential Fulfillment - Simple Example
```json
[[insert: ./test/credential-fulfillment/sample.json]]
```
:::

</section>

### Embed Targets

The following section details where the _Credential Fulfillment_ is to be embedded within a target data structure, as well as how to formulate the [JSONPath](https://goessner.net/articles/JsonPath/) expressions to select the [[ref:Claims]] within the target data structure.

#### Embed Locations

The following are the locations at which the `credential_fulfillment` object ****MUST**** be embedded for known target formats. For any location besides the top level of the embed target, the location is described in JSONPath syntax.

Target     | Location
---------- | --------
OpenID     | top-level
DIDComms   | `$.presentations~attach.data.json`
VP         | top-level
CHAPI      | `$.data`

### JSON Schema
The following JSON Schema Draft 7 definition summarizes the rules above:

<section>

::: example Credential Fulfimment - Schema
```json
[[insert: ./test/credential-fulfillment/schema.json]]
```
:::

</section>

## Appendix

### Embed Target Examples

#### Credential Fulfillment

<tab-panels selected-index="0">

<nav>
  <button type="button">Verifiable Presentation</button>
</nav>

<section>

::: example Credential Fulfillment - Verifiable Presentation
```json
[[insert: ./test/credential-fulfillment/appendix.json]]
```
:::

</section>

#### Credential Application

<tab-panels selected-index="0">

<nav>
  <button type="button">Verifiable Presentation</button>
</nav>

<section>

::: example Credential Application - Verifiable Presentation
```json
[[insert: ./test/credential-application/appendix.json]]
```
:::

</section>


## References

[[spec]]
