# Dataset

A Dataset takes a database schema (tables and columns) and adds Fides privacy categorizations. This is a database-agnostic way to annotate privacy declarations.

```
organization
  |-> registry (optional)
      |-> system
          |-> ** dataset **
              |-> collections
                  |-> fields
```

- The schema is represented as a set of "collections" (tables) that contain "fields" (columns). These can also be arbitrarily nested to handle document-type databases (e.g., NoSQL or S3).

- At each level -- Dataset, collection, and field, you can assign one or more Data Categories and Data Qualifiers. The Categories and Qualifiers declared at each child level is additive.

You use your Datasets by adding them to Systems. A System can contain any number of Datasets, and a Dataset can be added to any number of Systems. When a dataset is referenced by a system, all applicable data categories set on the dataset are treated as part of the system. If a Dataset is not referenced by a System, a warning is surfaced denoting an orphan dataset exists.

Datasets cannot contain other Datasets.

## Object Structure

**fides_key**     *constrained string*

A string token of your own invention that uniquely identifies this Dataset. It's your responsibility to ensure that the value is unique across all of your Dataset objects. The value may only contain alphanumeric characters, underscores, and hyphens. (`[A-Za-z0-9_.-]`).

**name**     *string*

A UI-friendly label for the Dataset.

**description**     *string*

A human-readable description of the Dataset.

**organization_fides_key**     *string*     default: `default_organization`

The fides key of the **Organization** to which this Dataset belongs.

**meta**     *object*

An optional object that provides additional information about the Dataset. You can structure the object however you like. It can be a simple set of `key: value` properties or a deeply nested hierarchy of objects. How you use the object is up to you: Fides ignores it.

**third_country_transfers**     *constrained string*

An optional array to identify any third countries where data is transited to. For consistency purposes, these fields are required to follow the Alpha-3 code set in **ISO 3166-1**

**joint_controller**  [array]

An optional array of contact information if a Joint Controller exists. This information can also be stored at the **system** level (`name`, `address`, `email`, `phone`).

**retention**  *string*

An optional string to describe the retention policy for a dataset. This field can also be applied more granularly at either the Collection or field level of a Dataset

**data_categories**     [string]
**data_qualifiers**     [string]

Arrays of Data Category and Data Qualifier resources, identified by `fides_key`, that apply to all collections in the Dataset.

**collections**     [object]

An array of objects that describe the Dataset's collections.

**collections.name**     string

A UI-friendly label for the collection.

**collections.description**     *string*

A human-readable description of the collection.

**collections.data_categories**     *[string]*
**collections.data_qualifiers**     *[string]*

Arrays of Data Category and Data Qualifier resources, identified by `fides_key`, that apply to all fields in the collection.

**collections.retention**  *string*

An optional string to describe the retention policy for a Dataset collection. This field can also be applied more granularly at the field level of a Dataset.

**collections.fields**     [object]

An array of objects that describe the collection's fields.

**collections.fields.name**     *string*

A UI-friendly label for the field.

**collections.fields.description**     *string*

A human-readable description of the field.

**collections.fields.data_categories**     *[string]*

Arrays of Data Categories, identified by `fides_key`, that applies to this field.

**collections.fields.data_qualifier**     *string*

A Data Qualifier that applies to this field. Note that this field holds a single value, therefore, the property name is singular.

**collections.fields.retention**  *string*

An optional string to describe the retention policy for a field within a Dataset collection.

**collections.fields.fields**     *[object]*

An optional array of objects that describe hierarchical/nested fields (typically found in NoSQL databases)


## Examples

### Manifest File


```
dataset:
  - fides_key: demo_users_dataset
    name: Demo Users Dataset
    description: Data collected about users for our analytics system.
    third_country_transfers:
    - USA
    - CAN
    joint_controller:
      name: Dave L. Epper
      address: 1 Acme Pl. New York, NY
      email: controller@acmeinc.com
      phone: +1 555 555 5555
    retention: 1 year post account deletion
    collections:
      - name: users
        description: User information
        data_categories:
          - user
        retention: 30 days post account deletion
        fields:
          - name: first_name
            description: User's first name
            data_categories:
              - user.name
          - name: email
            description: User's Email
            data_categories:
              - user.contact.email
          - name: phone
            description: User's phone numbers
            data_categories:
              - user.contact.phone_number
            retention: end of user relationship
            fields:
              - name: mobile
                description: User's mobile phone number
                data_categories:
                  - user.contact.phone_number
              - name: home
                description: User's home phone number
                data_categories:
                  - user.contact.phone_number
```

## API Payload

``
  {
    "fides_key": "demo_users_dataset",
    "name": "Demo Users Dataset",
    "description": "Data collected about users for our analytics system.",
    "third_country_transfers": ["USA", "CAN"],
    "joint_controller": {
      "name": "Dave L. Epper",
      "address": "1 Acme Pl. New York, NY",
      "email": "controller@acmeinc.com",
      "phone": "+1 555 555 5555"
    },
    "retention": "1 year post account deletion",
    "collections": [
      {
        "name": "users",
        "description": "User information",
        "retention": "30 days post account deletion",
        "fields": [
          {
            "name": "first_name",
            "description": "User's first name",
            "data_categories": [
              "user.name"
            ]
          },
          {
            "name": "email",
            "description": "User's Email",
            "data_categories": [
              "user.contact.email"
            ]
          },
          {
            "name": "phone",
            "description": "User's phone numbers",
            "data_categories": [
              "user.contact.phone_number"
            ],
            "retention": "end of user relationship",
            "fields": [
              {
                "name": "mobile",
                "description": "User's mobile phone number",
                "data_categories": [
                  "user.contact.phone_number"
                ],
              },
              {
                "name": "home",
                "description": "User's home phone number",
                "data_categories": [
                  "user.contact.phone_number"
                ]
              }
            ]
          }
        ]
      }
    ]
  }
```
