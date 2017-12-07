# Simple HOW-TO list using LoopBack

## How to enable UNSIGNED attribute to a particular property JSON model

We need to add the `unsigned` key to the particular property that we want to
check as `UNSIGNED`.

```json
{
  "name": "order_route",
  "base": "PersistedModel",
  "idInjection": true,
  "options": {
    "validateUpsert": true
  },
  "properties": {
    "qty": {
      "type": "number",
      "required": true,
      "unsigned": true
    }
  },
  "validations": [],
  "relations": {},
  "acls": [],
  "methods": {}
}
```
