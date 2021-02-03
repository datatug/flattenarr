# [datatug/flattenarr](https://github.com/datatug/flattenarr)

Flattens hierarchical object into a flat array of maps`interface{} => []map[string]interface{}` (think CSV/grid generation).

# How it works?

Imagine we have next object/JSON:
```
{
  countries: {
    "us": {
      "name": "United States"
      "languages": ["English"],
      "currencies": ["USD"],
      "populatin": 987654321,
    },
    "ca": {
      "name": "Canada"
      "languages": ["English", "French"]
      "currencies": ["CAD"]
      "populatin": 123456789,
    }
  }
}
```

And we need to generate a CSV file with fields: `COUNTRY_ID,COUNTRY_NAME,LANGUAGE,CURRENCY`.

This can be done by defining flattening mapping like:
```
{
  countries: {
    "$key": "COUNTRY_ID",
    "$value": {
      "name": "COUNTRY_NAME"
      "languages.#": LANGUAGE,
      "currencies.#": CURRENCY
    }
  }
}
```

And the output would be:

```
[
  {COUNTRY_ID: "us", COUNTRY_NAME: "United State, "LANGUAGE": "English", "CURRENCY": "USD"},
  {COUNTRY_ID: "ca", COUNTRY_NAME: "Canada,       "LANGUAGE": "English", "CURRENCY": "CAD"},
  {COUNTRY_ID: "ca", COUNTRY_NAME: "Canada,       "LANGUAGE": "French",  "CURRENCY": "CAD"},
]
```
