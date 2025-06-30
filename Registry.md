# Registry

A Registry is a collection of System resources. You may add a System to a Registry by setting the System's `registry_id` field.


```
organization
  |-> ** registry ** (optional)
      |-> system

```

- A System may belong to only one Registry.

- All Registries are siblings: You cannot create a hierarchy of Registries.

- Collecting your systems into Registries is optional.


## Object Structure

**fides_key**     *constrained string*

A string token of your own invention that uniquely identifies this Registry. It's your responsibility to ensure that the value is unique across all of your Registry objects. The value may only contain alphanumeric characters, underscores, and hyphens. ([A-Za-z0-9_.-]).

**name**     *string*

A UI-friendly label for the Registry.

**description**     *string*

A human-readable description of the Registry.

**organization_fides_key**     *string*     default: `default_organization`

The fides key of the **Organization** to which this Registry belongs.

## Examples

### Manifest File

```
registry:
  - fides_key: user_systems_registry
    name: User Systems Registry
    description: A Registry for all of the user-related systems.

```

### API Payload

```
{
  "fides_key": "user_systems_registry",
  "name": "User Systems Registry",
  "description": "A Registry for all of the user-related systems."
}

```