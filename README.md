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

## How to create foreign key constraint in MYSQL

We need to add the `foreignKeys` property to one of the endpoint of the
relation.

```json
{
  "name": "order_route",
  "base": "PersistedModel",
  "idInjection": true,
  "options": {
    "validateUpsert": true
  },
  "foreignKeys": {
    "fk_order_id": {
      "name": "fk_order_id",
      "foreignKey": "fk_order_id",
      "entityKey": "id",
      "entity": "order"
    }
  },
  "properties": {
    "qty": {
      "type": "number",
      "required": true,
      "unsigned": true
    },
    "description": {
      "type": "string",
      "required": true
    }
  },
  "validations": [],
  "relations": {},
  "acls": [],
  "methods": {}
}
```

Then we need to run the `automigrate` option first then over the same model run
the `autoupdate`. This second operation will do the trick and make the foreign
key constraint

```js
module.exports = function(app) {
  const { mysqlIDs: ds } = app.dataSources;

  // step one, automigrate
  // in this steps the columns will be created without the
  // corresponding constraint
  ds.automigrate(['order_route'], err => {
    if (err) {
      throw err;
    }
    console.log('tables synced');
  });

  // step two, autoupdate
  // in this step the foreign key constraint will be created
  // ds.autoupdate(['order_route'], err => {
  //   if (err) {
  //     throw err;
  //   }
  //   console.log('tables synced');
  // });
};
```

First we need to run `automigrate` and we need to stop the server, uncomment the
`autoupdate` portion and commment `automigrate` portion instead then run again
the server.
