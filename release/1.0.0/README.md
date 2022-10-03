# Software Input/Output Data

Schema.org profile for specifying software input/output data possibilities, used for software metadata descriptions.

**Authors**: Maarten van Gompel and Daniel Garijo

**Profile available at**: [https://w3id.org/software-iodata](https://w3id.org/software-iodata)

**Supported serializations**: [JSON-LD](https://softwareunderstanding.github.io/software-iodata/release/1.0.0/software-iodata.jsonld) (`application/ld+json`), [Turtle](https://softwareunderstanding.github.io/software-iodata/release/1.0.0/software-iodata.ttl) (`text/turtle`) and [HTML](https://softwareunderstanding.github.io/software-iodata/release/1.0.0/). See the code snippet below for an example on how to retrieve the profile in Turtle with a `curl` command:

```
curl -sH "accept:text/turtle" -L https://w3id.org/software-iodata
```

## Introduction

This profile describes vocabulary terms needed to describe, as part the
metadata of software applications, what data is consumed (input) or
produced (output) by an application. The profile is meant to be used with
[schema.org](https://schema.org/), [codemeta](https://codemeta.github.io) and the 
[software type profile](https://w3id.org/software-types).

Our goal is to introduce as little additional vocabulary as possible and only
extend schema.org and codemeta where representation gaps are present.

## Why do we need a schema.org profile for describing data being consumed and produced by software?

The current schema.org and codemeta vocabulary do not provide a clear way to
describe, for a `schema:SoftwareApplication`, the type of data that this
software may consume or produce.

We introduce two simple and limited properties that allow this.

This availability of specifying data input/output possibilities allows for
more accurate software metadata descriptions. 

**Disclaimer**: this work aims to create a profile that may be incorporated into codemeta or schema.org. The profile has persistent identifiers, but, if standardized, the classes and properties defined here may be absorbed into the above vocabularies.

## Properties

We introduce the following properties:

* ``consumesData`` - The software takes the specified data as one  of its inputs. The target object is typically interpreted as a template describing a class of data. For example, For example, a Dataset in NetCDF format containing precipitation and time variables (in their respective units)
* ``producesData`` - The software produces the specified data as one of its output. The target object is typically interpreted as a template describing a class of data. For example, a text document in CSV format.

The domain of both these properties is `schema:SoftwareApplication` (or any of
its subclasses) or `schema:SoftwareSourceCode`. The former is preferred as it
is more specific and combines nicely with the usage of `schema:targetProduct` as
proposed in https://w3id.org/software-types. If the domain is
`SoftwareSourceCode`, then these properties are interpreted as less specific (read
as: *some undefined build target of the source code consumes/produces certain type of data*).

The range of the properties is a `schema:CreativeWork` (or any of its subclasses), this
offers a high degree of flexibility and reuses as much as possible of the
existing schema.org properties for e.g. expressing content/encoding types,
natural languages etc. The object is to be typically interpreted as a
template data type, describing a class of data that meets the constraints as laid out by
further properties on that data. However, if enough constraining properties are
specified, it can also be used to specifically refer to a single particular dataset.

We recommend use of the following schema.org subclasses of `CreativeWork` as object:

* [MediaObject](https://schema.org/MediaObject)
    * [AudioObject](https://schema.org/AudioObject)
    * [VideoObject](https://schema.org/VideoObject)
    * [ImageObject](https://schema.org/ImageObject)
    * [3DModel](https://schema.org/3DModel)
* [DigititalDocument](https://schema.org/DigitalDocument)
    * [TextDigitalDocument](https://schema.org/TextDigitalDocument)
    * [PresentationDigitalDocument](https://schema.org/PresentationDigitalDocument)
    * [SpreadsheetDigitalDocument](https://schema.org/SpreadsheetDigitalDocument)
* [Dataset](https://schema.org/Dataset)

The example below describes software metadata for a fictitious speech recognition tool that takes an audio file and produces a textual transcription:

```json
{
    "@context": [
        "https://raw.githubusercontent.com/codemeta/codemeta/2.0/codemeta.jsonld",
        "https://raw.githubusercontent.com/schemaorg/schemaorg/main/data/releases/13.0/schemaorgcontext.jsonld",
        "https://w3id.org/software-types",
        "https://w3id.org/software-iodata"
    ],
    "@type": "SoftwareSourceCode",
    "name": "MySpeechRecognizer",
    "codeRepository": "https://github.com/someuser/MySpeechRecognizer",
    ...,
    "targetProduct": [
        {
            "type": "CommandLineApplication",
            "executableName": "transcribe",
            "name": "My Speech Recognition Tool",
            "runtimePlatform": "Linux"
            "consumesData": {
                "@type": "AudioObject",
                "encodingFormat": "audio/mp3",
                "inLanguage": {
                     "@id": "https://iso639-3.sil.org/code/eng",
                     "@type": "Language",
                     "name": "English"
                     "identifier": "eng"
                }
            },
            "producesData": {
                "@type": "TextDigitalDocument",
                "encodingFormat": "text/plain",
                "inLanguage": {
                     "@id": "https://iso639-3.sil.org/code/eng",
                     "@type": "Language",
                     "name": "English"
                     "identifier": "eng"
                }
            }
        },
    ]
}
```

The `consumesData` and `producesData` properties are deliberately limited and do 
not provide a full specification of the input/output of software. Both
properties merely describe what kind data is consumed or produced as one of the inputs or outputs of a software tool, with no guarantees
and no explicit relations between any of them. If multiple objects are specified,
it's not defined whether this is a union or intersection. The constraints on the 
data classes themselves (in the above example: `encodingFormat`,`inLanguage`), however, 
should be interpreted as an intersection.

Our aim here is just to give software metadata the ability to express some
information on what input and output a particular piece of software can accept
or produce, so that users reading the metadata can for example make an
informed decision whether certain software is useful for their purposes given
the data they have or expect to produce.

We do not express how this data is passed to or obtained from the software, nor
what data should or should not be passed/obtained together, nor what the exact
relation between the input and output data is. All that  would be up to other more
complex initiatives like [OpenAPI](https://www.openapis.org) or
[HydraCG](http://www.hydra-cg.com/spec/latest/core/). We merely enrich the
metadata description so that data expectations can be communicated to the
end-user.

## Implementations

Support for our `software_iodata` extension to codemeta/schema.org is
being implemented in the following software:

* [codemetapy](https://github.com/proycon/codemetapy) - Python library and command-line tool for converting to codemeta and creating/manipulating existing codemeta descriptions.
* [codemeta-harvester](https://github.com/proycon/codemeta-harvester) - Automatically harvests and converts software metadata to codemeta

## Related approaches

Many vocabularies exist to describe software or its constituent parts, e.g., the [software description ontology](https://w3id.org/okn/o/sd/), [description of a project vocabulary](http://usefulinc.com/ns/doap#), [hydra](https://www.hydra-cg.com/spec/latest/core/) (for API description), the common workflow language (description of inputs and outputs of software components, etc.), etc.  Our proposed profile does not aim to redefine any new term related to software, but propose a lightweight profile that can be easily incorporated into schema.org or codemeta.

## Examples

You can consult the following projects as examples that make use of this profile:

* [frog](https://github.com/LanguageMachines/frog/blob/master/codemeta.json)
* [ucto](https://github.com/LanguageMachines/ucto/blob/master/codemeta.json)
* [libfolia](https://github.com/LanguageMachines/libfolia/blob/master/codemeta.json)

Furthermore, the [CLARIAH Tools Portal](https://tools.dev.clariah.nl/) is build on the aforementioned implementations and may offer further examples of codemeta that also incorporates this software application type profile.

## Acknowledgement

This work was indirectly and partially funded through the [CLARIAH-PLUS project](https://clariah.nl).

This work has been supported by the Madrid Government (Comunidad de Madrid-Spain) under the Multiannual Agreement with Universidad Politécnica de Madrid in the line Support for R&D projects for Beatriz Galindo researchers, in the context of the V PRICIT (Regional Programme of Research and Technological Innovation)
