# FHIR specifications JSON

FHIR, pronounced "fire," stands for Fast Healthcare Interoperability Resources.
FHIR is standard for exchanging healthcare information between different
systems, designed to be flexible and easy to implement. It uses modern web
technologies like RESTful APIs and JSON, XML, or RDF for data representation.

<https://www.hl7.org/>

FHIR provides multiple releases. The current release as of 2024 is release 5
which is known as "R5".

<https://www.hl7.org/R5/>

This repository contains the worldwide-compatible FHIR R5 specifications files
using JSON, and explains how to create this repository for yourself if you wish.

On the FHIR R5 downloads page, the most important file is the definitions file.
This is the master set of worldwide-compatible definitions that should be the
first choice whenever generating any implementation artifacts.

<https://www.hl7.org/fhir/R5/downloads.html>

If you're specifically interested in HL7 FHIR for GIG Cymru NHS Wales, then see this:

<https://simplifier.net/guide/fhir-standards-wales-implementation-guide/Home>

## Do it yourself

Here's how you can create this repository from scratch:

```sh
mkdir fhir-specifications && cd $_
```

### FHIR R5 JSON downloads

Download files:

- **`definitions.json.zip`**: This is the master set of definitions.

- **`examples.json.zip`**: All the example resources in JSON format.

- **`fhir.schema.json.zip`**: JSON Schema is the vocabulary that enables JSON
  data consistency, validity, and interoperability at scale.

Run:

```sh
mkdir -p r5/json/definitions
curl -sSLO https://www.hl7.org/fhir/R5/definitions.json.zip &&
unzip -d r5/json/definitions definitions.json.zip && rm $_

mkdir -p r5/json/examples
curl -sSSO https://www.hl7.org/fhir/R5/examples-json.zip
unzip -d r5/json/examples examples-json.zip && rm $_

mkdir -p r5/json/schema
curl -sSSO https://www.hl7.org/fhir/R5/fhir.schema.json.zip
unzip -d r5/json/schema fhir.schema.json.zip && rm $_
```

For more about these see subdirectory [r5/json](r5/json).
