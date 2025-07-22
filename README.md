# FHIR specifications JSON

FHIR, pronounced "fire," stands for Fast Healthcare Interoperability Resources. FHIR is standard for exchanging healthcare information between different systems, designed to be flexible and easy to implement. It uses modern web technologies like RESTful APIs and JSON, XML, or RDF for data representation.

<https://www.hl7.org/>

This repository contains the worldwide-compatible FHIR specifications files
using JSON, and explains how to create this for yourself if you wish.

<https://www.hl7.org/fhir/downloads.html>

This is the master set of worldwide-compatible definitions that should be the
first choice whenever generating any implementation artifacts that you want to
work as a baseline across all worldwide HL7 FHIR implementations, extensions,
derivations, and the like.

If you're specifically interested in HL7 FHIR for GIG Cymru NHS Wales, then see this:

<https://simplifier.net/guide/fhir-standards-wales-implementation-guide/Home>

## Do it yourself

Here's how you can create this repository from scratch:

```sh
mkdir fhir-specifications-json && cd $_
```

Download the HL7 FHIR JSON definitions, examples, and validations:

```sh
curl -sSLO https://www.hl7.org/fhir/definitions.json.zip &&
unzip -d fhir-definitions-json definitions.json.zip && rm $_

curl -sSSO https://www.hl7.org/fhir/examples-json.zip
unzip -d fhir-examples-json examples-json.zip && rm $_

curl -sSSO https://www.hl7.org/fhir/fhir.schema.json.zip
unzip -d fhir-validations-json fhir.schema.json.zip && rm $_
```

## FHIR specifications JSON files

Explain these FHIR specifications JSON files:

- conceptmaps.json
- dataelements.json
- profiles-others.json
- profiles-resources.json
- profiles-types.json
- search-parameters.json
- valuesets.json
- version.info

These JSON files collectively define a FHIR Implementation Guide or specification package. 

These JSON files contain the same FHIR specification content as their XML counterparts. FHIR supports both JSON and XML representations equally.

**Use JSON when:**

- Building modern REST APIs
- Working with JavaScript/web applications
- Need smaller payload sizes
- Want simpler parsing

**Use XML when:**

- Integrating with legacy systems
- Need schema validation
- Working with systems requiring XML signatures
- Need namespace support

Both formats are equally valid in FHIR and contain identical information, just structured differently.

### conceptmaps.json

**Purpose**: Define mappings between different code systems

```json
{
  "resourceType": "Bundle",
  "entry": [{
    "resource": {
      "resourceType": "ConceptMap",
      "id": "gender-to-v2",
      "source": "http://hl7.org/fhir/ValueSet/administrative-gender",
      "target": "http://terminology.hl7.org/ValueSet/v2-0001",
      "group": [{
        "element": [{
          "code": "male",
          "target": [{
            "code": "M",
            "equivalence": "equivalent"
          }]
        }]
      }]
    }
  }]
}
```

**Use**: Translation tables between coding systems (SNOMED↔ICD-10, LOINC↔local codes)

### dataelements.json

**Purpose**: Reusable data field definitions with validation rules

```json
{
  "resourceType": "Bundle",
  "entry": [{
    "resource": {
      "resourceType": "DataElement",
      "id": "blood-pressure-systolic",
      "element": [{
        "path": "systolic",
        "type": [{"code": "integer"}],
        "min": 1,
        "max": "1",
        "minValueInteger": 0,
        "maxValueInteger": 300
      }]
    }
  }]
}
```

**Use**: Standardized data dictionary for forms, questionnaires, and data collection

### profiles-others.json

**Purpose**: Profiles for non-resource types (extensions, operations, naming systems)

```json
{
  "resourceType": "Bundle",
  "entry": [
    {
      "resource": {
        "resourceType": "StructureDefinition",
        "type": "Extension",
        "id": "patient-race",
        "url": "http://hl7.org/fhir/us/core/StructureDefinition/us-core-race",
        "context": [{
          "type": "element",
          "expression": "Patient"
        }]
      }
    },
    {
      "resource": {
        "resourceType": "OperationDefinition",
        "id": "patient-everything",
        "code": "everything",
        "resource": ["Patient"],
        "system": false,
        "type": false,
        "instance": true
      }
    }
  ]
}
```

**Contains**:

- Extension definitions
- Operation definitions
- Search parameter definitions
- Naming system definitions
- Capability statements

### profiles-resources.json

**Purpose**: Profiles (constraints) on FHIR base resources

```json
{
  "resourceType": "Bundle",
  "entry": [{
    "resource": {
      "resourceType": "StructureDefinition",
      "id": "us-core-patient",
      "type": "Patient",
      "derivation": "constraint",
      "differential": {
        "element": [
          {
            "id": "Patient.identifier",
            "min": 1,
            "mustSupport": true
          },
          {
            "id": "Patient.name",
            "min": 1,
            "mustSupport": true
          }
        ]
      }
    }
  }]
}
```

**Use**: Country/organization-specific requirements on resources (US Core, UK Core)

### profiles-types.json

**Purpose**: Profiles on FHIR data types and primitive types

```json
{
  "resourceType": "Bundle",
  "entry": [{
    "resource": {
      "resourceType": "StructureDefinition",
      "id": "us-core-birthsex",
      "type": "code",
      "kind": "primitive-type",
      "derivation": "constraint",
      "differential": {
        "element": [{
          "id": "code",
          "binding": {
            "strength": "required",
            "valueSet": "http://hl7.org/fhir/us/core/ValueSet/birthsex"
          }
        }]
      }
    }
  }]
}
```

**Contains**:

- Constrained primitive types (special string formats, code patterns)
- Complex type profiles (special Address, Identifier formats)
- Custom data type definitions

### search-parameters.json

**Purpose**: Defines searchable fields and how to query them

```json
{
  "resourceType": "Bundle",
  "entry": [{
    "resource": {
      "resourceType": "SearchParameter",
      "id": "patient-birthdate",
      "code": "birthdate",
      "base": ["Patient"],
      "type": "date",
      "expression": "Patient.birthDate",
      "comparator": ["eq", "ne", "gt", "lt", "ge", "le"]
    }
  }]
}
```

**Use**: Enables REST API queries like `GET /Patient?birthdate=1990-01-01`

### valuesets.json

**Purpose**: Defines allowed code sets for coded fields

```json
{
  "resourceType": "Bundle",
  "entry": [{
    "resource": {
      "resourceType": "ValueSet",
      "id": "marital-status",
      "url": "http://hl7.org/fhir/ValueSet/marital-status",
      "compose": {
        "include": [{
          "system": "http://terminology.hl7.org/CodeSystem/v3-MaritalStatus",
          "concept": [
            {"code": "M", "display": "Married"},
            {"code": "S", "display": "Single"},
            {"code": "D", "display": "Divorced"}
          ]
        }]
      }
    }
  }]
}
```

**Use**: Validation of coded fields, dropdown lists in UIs

### version.info

**Purpose**: Package metadata and versioning information

```json
{
  "package-id": "hl7.fhir.us.core",
  "version": "5.0.1",
  "fhir-version": "4.0.1",
  "title": "US Core Implementation Guide",
  "canonical": "http://hl7.org/fhir/us/core",
  "date": "2023-01-15",
  "publisher": "HL7 International",
  "dependencies": {
    "hl7.fhir.r4.core": "4.0.1",
    "hl7.terminology.r4": "3.1.0"
  }
}
```

**Contains**: Version info, dependencies, build metadata

## How These Files Work Together

### Resource Definition Flow

```txt
Base FHIR Spec → profiles-resources.json → Your Implementation
     ↓                    ↓
profiles-types.json    valuesets.json
                          ↓
                    conceptmaps.json (for translations)
```

### Complete Example: US Core Patient

**profiles-resources.json** defines constraints:

```json
{
  "resourceType": "StructureDefinition",
  "id": "us-core-patient",
  "element": [{
    "id": "Patient.extension:race",
    "type": [{
      "code": "Extension",
      "profile": ["http://hl7.org/fhir/us/core/StructureDefinition/us-core-race"]
    }]
  }]
}
```

**profiles-others.json** defines the race extension:

```json
{
  "resourceType": "StructureDefinition",
  "type": "Extension",
  "id": "us-core-race",
  "context": [{"type": "element", "expression": "Patient"}]
}
```

**valuesets.json** defines allowed race codes:

```json
{
  "resourceType": "ValueSet",
  "id": "omb-race-category",
  "compose": {
    "include": [{
      "system": "urn:oid:2.16.840.1.113883.6.238",
      "concept": [
        {"code": "1002-5", "display": "American Indian or Alaska Native"},
        {"code": "2028-9", "display": "Asian"}
      ]
    }]
  }
}
```

**search-parameters.json** enables race searching:

```json
{
  "resourceType": "SearchParameter",
  "code": "race",
  "base": ["Patient"],
  "type": "token",
  "expression": "Patient.extension('http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension('ombCategory').value"
}
```

### Implementation Guide Structure

These files typically form an Implementation Guide (IG) with this hierarchy:
```
my-ig/
├── package/
│   ├── conceptmaps.json
│   ├── dataelements.json
│   ├── profiles-others.json
│   ├── profiles-resources.json
│   ├── profiles-types.json
│   ├── search-parameters.json
│   ├── valuesets.json
│   └── version.info
├── examples/
│   └── patient-example.json
└── pages/
    └── documentation.html
```

### Practical example

Example generated code usage:

```rust
// Generated from profiles-resources.json
#[derive(Serialize, Deserialize)]
pub struct UsCorePatient {
    #[serde(rename = "resourceType")]
    pub resource_type: String,
    
    // Required by US Core profile
    pub identifier: Vec<Identifier>,  // min: 1
    pub name: Vec<HumanName>,        // min: 1
    
    // Optional with US Core extensions
    #[serde(skip_serializing_if = "Option::is_none")]
    pub race: Option<UsCore Race>,
}

// Validation based on valuesets.json
impl UsCore Race {
    pub fn validate_code(&self) -> Result<(), ValidationError> {
        // Check against omb-race-category value set
    }
}
```

These files work together to create a complete, validated, interoperable FHIR implementation that ensures data consistency and enables seamless healthcare data exchange.

## FHIR Profiles and Resources - Overview

### What is profiles-resources.json?

This is typically a FHIR specification file that contains:

- **Resource Definitions**: The core FHIR resource types (Patient, Observation, Medication, etc.)
- **Profile Definitions**: Constraints and extensions on base resources
= **Metadata**: Information about the FHIR implementation guide

Common FHIR Resources:

- **Patient**: Demographics and other administrative information
- **Observation**: Measurements and simple assertions (lab results, vitals)
- **Condition**: Clinical conditions, problems, diagnoses
- **Medication**: Definition of a medication
- **MedicationRequest**: Prescription or medication order
- **Procedure**: Action taken on or for a patient
- **Encounter**: Interaction between patient and healthcare provider
- **AllergyIntolerance**: Risk of harmful reaction to a substance

### Typical Structure

```json
{
  "resourceType": "Bundle",
  "type": "collection",
  "entry": [
    {
      "resource": {
        "resourceType": "StructureDefinition",
        "id": "Patient",
        "type": "Patient",
        "kind": "resource",
        "abstract": false,
        "baseDefinition": "http://hl7.org/fhir/StructureDefinition/DomainResource",
        "differential": {
          "element": [
            {
              "id": "Patient",
              "path": "Patient",
              "definition": "Demographics and administrative information about a person..."
            },
            {
              "id": "Patient.identifier",
              "path": "Patient.identifier",
              "type": [{
                "code": "Identifier"
              }],
              "min": 0,
              "max": "*"
            }
          ]
        }
      }
    }
  ]
}
```

### Key Components

1. **StructureDefinition** - Defines the structure of a resource or profile
2. **ElementDefinition** - Defines individual fields/elements
3. **Cardinality** - min/max occurrences (0..1, 1..1, 0..*, etc.)
4. **Data Types** - FHIR data types (string, boolean, CodeableConcept, etc.)
5. **Constraints** - Business rules and validation

### Profile Examples

Profiles constrain base resources for specific use cases:

```json
{
  "resourceType": "StructureDefinition",
  "id": "us-core-patient",
  "type": "Patient",
  "baseDefinition": "http://hl7.org/fhir/StructureDefinition/Patient",
  "derivation": "constraint",
  "differential": {
    "element": [
      {
        "id": "Patient.identifier",
        "path": "Patient.identifier",
        "min": 1,  // US Core requires at least one identifier
        "mustSupport": true
      },
      {
        "id": "Patient.name",
        "path": "Patient.name",
        "min": 1,  // Name is required in US Core
        "mustSupport": true
      }
    ]
  }
}
```

## FHIR ConceptMaps - Overview

A FHIR ConceptMap is a resource that defines relationships and mappings between concepts in different code systems or value sets. It's essentially a translation table between different medical coding systems.

### Purpose of ConceptMaps

ConceptMaps solve a critical healthcare interoperability problem: different systems use different codes for the same concepts. For example:
- One system might use SNOMED CT codes
- Another uses ICD-10
- A third uses LOINC
- Legacy systems might use proprietary codes

### Typical Structure of conceptmaps.json

```json
{
  "resourceType": "Bundle",
  "type": "collection",
  "entry": [
    {
      "resource": {
        "resourceType": "ConceptMap",
        "id": "gender-identity-map",
        "url": "http://example.org/fhir/ConceptMap/gender-identity",
        "version": "1.0.0",
        "name": "GenderIdentityMap",
        "title": "Gender Identity Mapping",
        "status": "active",
        "experimental": false,
        "date": "2024-01-01",
        "publisher": "Example Organization",
        "description": "Maps between different gender identity code systems",
        
        "sourceUri": "http://example.org/ValueSet/source-gender",
        "targetUri": "http://hl7.org/fhir/ValueSet/administrative-gender",
        
        "group": [
          {
            "source": "http://example.org/CodeSystem/local-gender",
            "target": "http://hl7.org/fhir/administrative-gender",
            "element": [
              {
                "code": "M",
                "display": "Male",
                "target": [
                  {
                    "code": "male",
                    "display": "Male",
                    "equivalence": "equivalent"
                  }
                ]
              },
              {
                "code": "F",
                "display": "Female",
                "target": [
                  {
                    "code": "female",
                    "display": "Female",
                    "equivalence": "equivalent"
                  }
                ]
              },
              {
                "code": "U",
                "display": "Unknown",
                "target": [
                  {
                    "code": "unknown",
                    "display": "Unknown",
                    "equivalence": "equivalent"
                  }
                ]
              }
            ]
          }
        ]
      }
    }
  ]
}
```

### Key Components

#### Group

Groups mappings by source and target code systems:

```json
"group": [
  {
    "source": "http://snomed.info/sct",
    "target": "http://hl7.org/fhir/sid/icd-10"
  }
]
```

#### Element

Individual concept mappings within a group:

```json
"element": [
  {
    "code": "386661006",
    "display": "Fever",
    "target": [
      {
        "code": "R50.9",
        "display": "Fever, unspecified",
        "equivalence": "equivalent"
      }
    ]
  }
]
```

#### **Equivalence Types**

Describes the relationship quality:

- `equivalent` - Exact match
- `wider` - Target is broader
- `narrower` - Target is more specific
- `inexact` - Close but not exact
- `unmatched` - No reasonable match
- `disjoint` - Concepts are not related

### Common Use Cases

#### Lab Result Mappings

```json
{
  "code": "14682-9",  // LOINC code
  "display": "Creatinine [Moles/volume] in Serum or Plasma",
  "target": [
    {
      "code": "CREA",  // Local lab code
      "display": "Creatinine",
      "equivalence": "equivalent"
    }
  ]
}
```

#### Medication Mappings

```json
{
  "code": "387517004",  // SNOMED code
  "display": "Paracetamol",
  "target": [
    {
      "code": "161",  // NDC code
      "display": "Acetaminophen",
      "equivalence": "equivalent",
      "comment": "Brand name differences"
    }
  ]
}
```

#### Condition/Diagnosis Mappings

```json
{
  "code": "73211009",  // SNOMED
  "display": "Diabetes mellitus",
  "target": [
    {
      "code": "E11",  // ICD-10
      "display": "Type 2 diabetes mellitus",
      "equivalence": "narrower",
      "comment": "ICD-10 is more specific about diabetes type"
    }
  ]
}
```

### Complex Mapping Scenarios

#### One-to-Many Mappings

```json
{
  "code": "LOCAL001",
  "display": "Diabetes with complications",
  "target": [
    {
      "code": "E11.21",
      "display": "Type 2 diabetes mellitus with diabetic nephropathy",
      "equivalence": "narrower"
    },
    {
      "code": "E11.22",
      "display": "Type 2 diabetes mellitus with diabetic chronic kidney disease",
      "equivalence": "narrower"
    }
  ]
}
```

#### Conditional Mappings

```json
{
  "code": "HIGH_BP",
  "display": "High blood pressure",
  "target": [
    {
      "code": "I10",
      "display": "Essential (primary) hypertension",
      "equivalence": "inexact",
      "comment": "Use only if no secondary cause identified"
    }
  ]
}
```

### Practical Implementation

```rust
// Example of how the generated Rust code might look
#[derive(Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct ConceptMap {
    pub resource_type: String,
    pub id: Option<String>,
    pub url: Option<String>,
    pub group: Vec<ConceptMapGroup>,
}

#[derive(Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct ConceptMapGroup {
    pub source: Option<String>,
    pub target: Option<String>,
    pub element: Vec<ConceptMapElement>,
}
```

ConceptMaps are crucial for healthcare interoperability, enabling systems to communicate effectively despite using different coding standards.

## FHIR DataElements - Overview

A FHIR DataElement resource defines a single piece of data with its meaning, allowed values, and usage constraints. Think of it as a reusable data dictionary entry that ensures consistent data collection across different systems.

### Purpose of DataElements

DataElements solve the problem of inconsistent data definitions across healthcare systems. They provide:

- Standardized definitions for data points
- Reusable components for forms and questionnaires
- Consistent validation rules
- Clear semantics for data exchange

### Typical Structure of dataelements.json

```json
{
  "resourceType": "Bundle",
  "type": "collection",
  "entry": [
    {
      "resource": {
        "resourceType": "DataElement",
        "id": "patient-birthdate",
        "url": "http://example.org/fhir/DataElement/patient-birthdate",
        "identifier": [
          {
            "system": "http://example.org/dataelements",
            "value": "DE0001"
          }
        ],
        "version": "1.0.0",
        "status": "active",
        "experimental": false,
        "date": "2024-01-01",
        "publisher": "Example Health Organization",
        "name": "PatientBirthDate",
        "title": "Patient Birth Date",
        "contact": [
          {
            "name": "Clinical Informatics Team",
            "telecom": [
              {
                "system": "email",
                "value": "informatics@example.org"
              }
            ]
          }
        ],
        "useContext": [
          {
            "code": {
              "system": "http://terminology.hl7.org/CodeSystem/usage-context-type",
              "code": "focus"
            },
            "valueCodeableConcept": {
              "coding": [
                {
                  "system": "http://snomed.info/sct",
                  "code": "410525008",
                  "display": "Date of birth"
                }
              ]
            }
          }
        ],
        "jurisdiction": [
          {
            "coding": [
              {
                "system": "urn:iso:std:iso:3166",
                "code": "US"
              }
            ]
          }
        ],
        "mapping": [
          {
            "identity": "v2",
            "uri": "http://hl7.org/v2",
            "name": "HL7 v2 Mapping",
            "comment": "PID-7"
          },
          {
            "identity": "rim",
            "uri": "http://hl7.org/v3",
            "name": "RIM Mapping"
          }
        ],
        "element": [
          {
            "path": "birthDate",
            "label": "Birth Date",
            "code": [
              {
                "system": "http://loinc.org",
                "code": "21112-8",
                "display": "Birth date"
              }
            ],
            "definition": "The date on which the patient was born",
            "comment": "At least an estimated year should be provided as a guess if the exact date is unknown",
            "requirements": "Age of the individual drives many clinical processes",
            "alias": ["DOB", "Date of Birth"],
            "type": [
              {
                "code": "date"
              }
            ],
            "example": [
              {
                "label": "General",
                "valueDate": "1970-03-25"
              }
            ],
            "minValueDate": "1900-01-01",
            "maxValueDate": "2024-12-31",
            "mustSupport": true,
            "isModifier": false,
            "isSummary": true,
            "mapping": [
              {
                "identity": "v2",
                "map": "PID-7"
              },
              {
                "identity": "rim",
                "map": "player[classCode=PSN|ANM and determinerCode=INSTANCE]/birthTime"
              }
            ]
          }
        ]
      }
    }
  ]
}
```

### Key Components

#### Element Definition

The core data definition with constraints:

```json
"element": [
  {
    "path": "bloodPressure.systolic",
    "label": "Systolic Blood Pressure",
    "definition": "Peak arterial pressure during heart contraction",
    "type": [{"code": "integer"}],
    "minValueInteger": 0,
    "maxValueInteger": 300,
    "mustSupport": true
  }
]
```

#### Data Types

Common FHIR data types used in DataElements:

```json
// Simple types
{"code": "boolean"}      // true/false
{"code": "integer"}      // Whole numbers
{"code": "decimal"}      // Decimal numbers
{"code": "string"}       // Text
{"code": "date"}         // Date only
{"code": "dateTime"}     // Date and time

// Complex types
{"code": "CodeableConcept"}  // Coded values with text
{"code": "Quantity"}         // Measured amounts
{"code": "Range"}           // Low and high values
{"code": "Identifier"}      // Business identifiers
```

#### 3. **Constraints and Validation**

```json
{
  "path": "labResult.value",
  "type": [{"code": "Quantity"}],
  "constraint": [
    {
      "key": "lab-1",
      "severity": "error",
      "human": "Lab value must be positive",
      "expression": "value >= 0"
    }
  ]
}
```

### Common DataElement Examples

#### Clinical Measurements**

```json
{
  "resourceType": "DataElement",
  "id": "body-temperature",
  "element": [
    {
      "path": "temperature",
      "label": "Body Temperature",
      "code": [
        {
          "system": "http://loinc.org",
          "code": "8310-5",
          "display": "Body temperature"
        }
      ],
      "type": [{"code": "Quantity"}],
      "minValueQuantity": {
        "value": 25,
        "unit": "Cel",
        "system": "http://unitsofmeasure.org",
        "code": "Cel"
      },
      "maxValueQuantity": {
        "value": 45,
        "unit": "Cel",
        "system": "http://unitsofmeasure.org",
        "code": "Cel"
      }
    }
  ]
}
```

#### Questionnaire Responses

```json
{
  "resourceType": "DataElement",
  "id": "pain-scale",
  "element": [
    {
      "path": "painLevel",
      "label": "Pain Level (0-10 scale)",
      "definition": "Patient's self-reported pain level",
      "type": [{"code": "integer"}],
      "minValueInteger": 0,
      "maxValueInteger": 10,
      "example": [
        {
          "label": "No pain",
          "valueInteger": 0
        },
        {
          "label": "Severe pain",
          "valueInteger": 10
        }
      ]
    }
  ]
}
```

#### Coded Values**

```json
{
  "resourceType": "DataElement",
  "id": "smoking-status",
  "element": [
    {
      "path": "smokingStatus",
      "label": "Smoking Status",
      "type": [{"code": "CodeableConcept"}],
      "binding": {
        "strength": "required",
        "valueSet": "http://hl7.org/fhir/ValueSet/smoking-status",
        "description": "Smoking status codes"
      }
    }
  ]
}
```

### Value Set Bindings

DataElements often reference value sets for coded fields:

```json
"binding": {
  "strength": "required",
  "valueSet": "http://example.org/ValueSet/allergy-type",
  "description": "Types of allergic reactions"
}
```

### Use Cases

#### Form Generation

DataElements can drive dynamic form creation:

```json
{
  "path": "consent.agree",
  "label": "I agree to the terms",
  "type": [{"code": "boolean"}],
  "mustSupport": true,
  "example": [
    {
      "label": "Agreement",
      "valueBoolean": true
    }
  ]
}
```

#### Data Validation

```json
{
  "path": "email",
  "type": [{"code": "string"}],
  "constraint": [
    {
      "key": "email-1",
      "severity": "error",
      "human": "Must be a valid email address",
      "expression": "matches('^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Z|a-z]{2,}$')"
    }
  ]
}
```

#### Research Data Collection**

```json
{
  "path": "studyData.collectionDate",
  "definition": "Date specimen was collected",
  "type": [{"code": "dateTime"}],
  "mustSupport": true,
  "constraint": [
    {
      "key": "study-1",
      "human": "Collection date cannot be in the future",
      "expression": "value <= now()"
    }
  ]
}
```

### Practical example

Example generated Rust structure:

```rust
#[derive(Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct DataElement {
    pub resource_type: String,
    pub id: Option<String>,
    pub element: Vec<ElementDefinition>,
    pub status: String,
    pub binding: Option<ElementBinding>,
}
```

### Benefits of Using DataElements

1. **Consistency**: Same data collected the same way everywhere
2. **Reusability**: Define once, use many times
3. **Validation**: Built-in data quality rules
4. **Interoperability**: Clear semantics for data exchange
5. **Documentation**: Self-documenting data structures
6. **Version Control**: Track changes to data definitions over time

DataElements form the foundation for building standardized forms, questionnaires, and data collection instruments in FHIR-based systems.

## FHIR SearchParameters - Overview

FHIR SearchParameter resources define how to search for resources in a FHIR server. They specify which fields can be queried, how they're indexed, and what search operations are supported.

### Purpose of SearchParameters

SearchParameters enable:

- RESTful searching of FHIR resources
- Custom search capabilities beyond the base specification
- Performance optimization through proper indexing
- Chained and reverse chained searches
- Complex queries across related resources

### Typical Structure of search-parameters.json

```json
{
  "resourceType": "Bundle",
  "type": "collection",
  "entry": [
    {
      "resource": {
        "resourceType": "SearchParameter",
        "id": "patient-birthdate",
        "url": "http://hl7.org/fhir/SearchParameter/Patient-birthdate",
        "version": "4.0.1",
        "name": "birthdate",
        "status": "active",
        "experimental": false,
        "date": "2019-11-01T09:29:23+11:00",
        "publisher": "Health Level Seven International (Patient Administration)",
        "description": "The patient's date of birth",
        "code": "birthdate",
        "base": ["Patient"],
        "type": "date",
        "expression": "Patient.birthDate",
        "xpath": "f:Patient/f:birthDate",
        "xpathUsage": "normal",
        "comparator": ["eq", "ne", "gt", "ge", "lt", "le", "sa", "eb", "ap"]
      }
    }
  ]
}
```

### Key Components

#### Search Parameter Types**

```json
// Number - numeric values
{
  "code": "probability",
  "type": "number",
  "expression": "RiskAssessment.prediction.probability"
}

// Date - date/time values
{
  "code": "date",
  "type": "date",
  "expression": "Observation.effective"
}

// String - text searches
{
  "code": "name",
  "type": "string",
  "expression": "Patient.name.family | Patient.name.given"
}

// Token - exact matches on codes/identifiers
{
  "code": "identifier",
  "type": "token",
  "expression": "Patient.identifier"
}

// Reference - relationships between resources
{
  "code": "patient",
  "type": "reference",
  "expression": "Observation.subject",
  "target": ["Patient"]
}

// Quantity - numerical values with units
{
  "code": "value-quantity",
  "type": "quantity",
  "expression": "Observation.value as Quantity"
}

// URI - uniform resource identifiers
{
  "code": "url",
  "type": "uri",
  "expression": "ValueSet.url"
}

// Composite - combination of parameters
{
  "code": "code-value-quantity",
  "type": "composite",
  "expression": "Observation",
  "component": [
    {
      "definition": "http://hl7.org/fhir/SearchParameter/clinical-code",
      "expression": "code"
    },
    {
      "definition": "http://hl7.org/fhir/SearchParameter/Observation-value-quantity",
      "expression": "value.as(Quantity)"
    }
  ]
}
```

#### Search Modifiers

```json
{
  "code": "name",
  "type": "string",
  "modifier": [
    "exact",     // Exact match (case-sensitive)
    "contains",  // Contains the string
    "missing"    // Search for missing/present values
  ]
}
```

#### Comparators for Different Types**

```json
{
  "comparator": [
    "eq",  // equals (default)
    "ne",  // not equals
    "gt",  // greater than
    "lt",  // less than
    "ge",  // greater or equal
    "le",  // less or equal
    "sa",  // starts after
    "eb",  // ends before
    "ap"   // approximately (within ~10%)
  ]
}
```

### Common SearchParameter Examples

#### Patient Name Search

```json
{
  "resourceType": "SearchParameter",
  "id": "patient-name",
  "code": "name",
  "base": ["Patient"],
  "type": "string",
  "expression": "Patient.name",
  "multipleOr": true,
  "multipleAnd": true,
  "modifier": ["exact", "contains"]
}
```

Usage: `GET /Patient?name=Smith` or `GET /Patient?name:contains=john`

#### Observation by Code and Date

```json
{
  "resourceType": "SearchParameter",
  "id": "observation-code",
  "code": "code",
  "base": ["Observation"],
  "type": "token",
  "expression": "Observation.code"
}
```

Usage: `GET /Observation?code=http://loinc.org|85354-9&date=ge2023-01-01`

#### Reference Search with Chaining

```json
{
  "resourceType": "SearchParameter",
  "id": "observation-patient",
  "code": "patient",
  "base": ["Observation"],
  "type": "reference",
  "expression": "Observation.subject.where(resolve() is Patient)",
  "target": ["Patient"],
  "chain": ["name", "identifier", "birthdate"]
}
```

Usage: `GET /Observation?patient.name=Smith` (chained search)

#### Composite Search

```json
{
  "resourceType": "SearchParameter",
  "id": "observation-code-value",
  "code": "code-value-quantity",
  "base": ["Observation"],
  "type": "composite",
  "expression": "Observation",
  "component": [
    {
      "definition": "http://hl7.org/fhir/SearchParameter/clinical-code",
      "expression": "code"
    },
    {
      "definition": "http://hl7.org/fhir/SearchParameter/Observation-value-quantity",
      "expression": "value as Quantity"
    }
  ]
}
```

Usage: `GET /Observation?code-value-quantity=http://loinc.org|8480-6$gt90`

### Advanced Search Features

#### Include and Reverse Include

```json
{
  "resourceType": "SearchParameter",
  "id": "observation-performer",
  "code": "performer",
  "base": ["Observation"],
  "type": "reference",
  "expression": "Observation.performer",
  "target": ["Practitioner", "Organization", "Patient"]
}
```

Usage: 

- `GET /Observation?_include=Observation:performer` (include referenced resources)
- `GET /Patient?_revinclude=Observation:patient` (include resources that reference this)

#### Custom Search with FHIRPath

```json
{
  "resourceType": "SearchParameter",
  "id": "condition-onset-age",
  "code": "onset-age",
  "base": ["Condition"],
  "type": "number",
  "expression": "Condition.onset.as(Age).value",
  "comparator": ["eq", "gt", "lt", "ge", "le"]
}
```

#### Multi-Resource Search

```json
{
  "resourceType": "SearchParameter",
  "id": "clinical-patient",
  "code": "patient",
  "base": ["Observation", "Condition", "Procedure"],
  "type": "reference",
  "expression": "Observation.subject | Condition.subject | Procedure.subject",
  "target": ["Patient"]
}
```

### Search Result Parameters

Special parameters that control result sets:

```json
// Sorting
_sort=date,-status  // Sort by date ascending, then status descending

// Pagination
_count=20&_offset=40  // 20 results starting from the 40th

// Summary
_summary=true  // Return only summary elements
_summary=count  // Return only the total count

// Elements
_elements=id,name,birthDate  // Return only specified elements
```

### Implementation Considerations

#### Performance Optimization

```json
{
  "resourceType": "SearchParameter",
  "id": "patient-mrn",
  "code": "mrn",
  "base": ["Patient"],
  "type": "token",
  "expression": "Patient.identifier.where(system='http://hospital.org/mrn')",
  "multipleOr": false,  // Improves performance
  "multipleAnd": false
}
```

#### Security Considerations

```json
{
  "resourceType": "SearchParameter",
  "id": "patient-ssn",
  "code": "ssn",
  "base": ["Patient"],
  "type": "token",
  "expression": "Patient.identifier.where(system='http://hl7.org/fhir/sid/us-ssn')",
  "modifier": ["missing", "exact"]  // No partial SSN searches
}
```

### Practical example

Example generated Rust usage:

```rust
#[derive(Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct SearchParameter {
    pub resource_type: String,
    pub id: Option<String>,
    pub code: String,
    pub base: Vec<String>,
    pub type_: SearchParamType,
    pub expression: Option<String>,
}

// Usage in a FHIR client
let search_params = vec![
    ("birthdate", "ge1990-01-01"),
    ("name:contains", "Smith"),
    ("_include", "Patient:organization"),
];
```

### Common Search Patterns

#### Find Recent Lab Results

```
GET /Observation?category=laboratory&date=ge2024-01-01&_sort=-date
```

#### Search Patients by Multiple Criteria

```
GET /Patient?name=John&gender=male&birthdate=1980-1990
```

#### Find All Resources for a Patient

```
GET /Patient/123/$everything
```

#### Complex Chained Search

```
GET /MedicationRequest?patient.organization.name=Mayo%20Clinic
```
