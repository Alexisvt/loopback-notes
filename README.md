# Simple HOW-TO list using LoopBack

## How to enable UNSIGNED attribute to a particular property JSON model

We need to add the `unsigned` key to the particular property that we want to
check as `UNSIGNED`.

```json
{
  "name": "book",
  "base": "PersistedModel",
  "idInjection": true,
  "options": {
    "validateUpsert": true
  },
  "properties": {
    "pages": {
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
  "name": "book",
  "base": "PersistedModel",
  "idInjection": true,
  "options": {
    "validateUpsert": true
  },
  "foreignKeys": {
    "fk_author_id": {
      "name": "fk_author_id",
      "foreignKey": "fk_author_id",
      "entityKey": "id",
      "entity": "order"
    }
  },
  "properties": {
    "pages": {
      "type": "number",
      "required": true,
      "unsigned": true
    },
    "name": {
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
  ds.automigrate(['book'], err => {
    if (err) {
      throw err;
    }
    console.log('tables synced');
  });

  // step two, autoupdate
  // in this step the foreign key constraint will be created
  // ds.autoupdate(['book'], err => {
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

**Note:** We need to make sure that we have the latest version of the
[loopback-connector-mysql](https://www.npmjs.com/package/loopback-connector-mysql)
otherwise the creation of the constraint will fail.

## How to set the type of a property to other than the default one

In SQL we have the option to define a number as `TINYINT`, `INT`, `DECIMAL` and
son on, we can tell to **LoopBack** about it, if we are using **MYSQL** we can
do that adding the key _mysql_ to the desire property we want to do the change.

```json
{
  "name": "book",
  "base": "PersistedModel",
  "idInjection": true,
  "options": {
    "validateUpsert": true
  },
  "properties": {
    "price": {
      "type": "number",
      "required": true,
      "unsigned": true,
      "mysql": {
        "dataType": "DECIMAL",
        "dataPrecision": 15,
        "dataScale": 2
      }
    },
    "name": {
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

In this case we are changing the **price** property to use `DECIMAL` instead of
default value which is `INT`.

**Note:** more about types:

* [LoopBack types](https://loopback.io/doc/en/lb3/LoopBack-types.html)
* [MySQL connector](https://loopback.io/doc/en/lb3/MySQL-connector.html#loopback-to-mysql-types)
