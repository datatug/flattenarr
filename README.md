# [datatug/flattenarr](https://github.com/datatug/flattenarr)

Flattens hierarchical object into a flat array of maps`interface{} => []map[string]interface{}` (think CSV/grid generation).

# How it works?

Imagine we have next object/JSON (_pseudo-code_):
```javascript
var dataToFlatten = {
  countries: {
    us: {
      name: "United States"
      languages: ["English"],
      currencies: ["USD"],
      population: 987654321,
    },
    ca: {
      name: "Canada"
      languages: ["English", "French"]
      currencies: ["CAD", "USD"]
      population: 123456789,
    }
  }
}
```

And we need to generate a CSV file with fields: `COUNTRY_ID,COUNTRY_NAME,LANGUAGE,CURRENCY`.

This can be done by defining flattening mapping like (_pseudo-code_):
```javascript
var flatteningMapping = {
  countries: {
    $key: "COUNTRY_ID",            // here $key refers to a key of item in 'countries' element
    $value: {                      // here $value refers to a value of item in 'countries' element
      name: "COUNTRY_NAME",        // `name` is source attribute, `COUNTRY_NAME` is output column
      "languages.#": "LANGUAGE",
      "currencies.#": "CURRENCY",
      population: "POPULATION",
    }
  }
}

var outputColumnsOrder = ['COUNTRY_ID', 'COUNTRY_NAME', 'LANGUAGE', 'CURRENCY', 'POPULATION']
```

## To flatten to an array of key-values:

```go
var err error
var flattened []map[string]interface{}

flattened, err = flattenarr.FlattenToKeyValues(dataToFlatten, flatteningMapping, outputColumnsOrder)

// Output:
[
  {"COUNTRY_ID": "us", "COUNTRY_NAME": "United State", "LANGUAGE": "English", "CURRENCY": "USD", "POPULATION": 987654321},
  {"COUNTRY_ID": "ca", "COUNTRY_NAME": "Canada",       "LANGUAGE": "English", "CURRENCY": "CAD", "POPULATION": 123456789},
  {"COUNTRY_ID": "ca", "COUNTRY_NAME": "Canada",       "LANGUAGE": "French",  "CURRENCY": "CAD", "POPULATION": 123456789},
  {"COUNTRY_ID": "ca", "COUNTRY_NAME": "Canada",       "LANGUAGE": "English", "CURRENCY": "USD", "POPULATION": 123456789},
  {"COUNTRY_ID": "ca", "COUNTRY_NAME": "Canada",       "LANGUAGE": "French",  "CURRENCY": "USD", "POPULATION": 123456789},
]

```

## To flatten to a slice of values only:

```go
var err error
var flattened [][]nterface{}

flattened, err = flattenarr.FlattenToValuesOnly(dataToFlatten, flatteningMapping, outputColumnsOrder)

// Output:
[
  ["us", "United State", "English", "USD", 987654321],
  ["ca", "Canada",       "English", "CAD", 123456789],
  ["ca", "Canada",       "French",  "CAD", 123456789],
  ["ca", "Canada",       "English", "USD", 123456789],
  ["ca", "Canada",       "French",  "USD", 123456789],
]
```
