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

Then we need to run our server with the `autoupdate` feature in it, this will
create the table and the constraint. Obviously we need the depency table first
if not the server will throw an error.

```js
module.exports = function(app) {
  const { mysqlIDs: ds } = app.dataSources;

  // in this step the table will be created
  // and also the foreign key constraint
  ds.autoupdate(['book'], err => {
    if (err) {
      throw err;
    }
    console.log('tables synced');
  });
};
```

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
