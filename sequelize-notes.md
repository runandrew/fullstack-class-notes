# Sequelize Notes

# Contents
+ Relationships
  * Source and Target
  * 1:1 relationships
  * One-to-many Relationships
  * Many-to-many Relationships
+ Model methods
  * getter
  * setter
  * class
  * instance
  * hooks
+ Querying
  * Types
  * Complex queries
  * Eager loading


# Relationships
## Source and target

```js
var Player = db.define('player', {});
var Team = db.define('team', {});

Player.belongsTo(Team);
```

`Player` is the **source**, `Team` is the **target**.

## 1:1 relationships

One-to-one associations are associations between exactly two models connected by a single foreign key.

```js
Player.belongsTo(Team);
Player.hasOne(Team);
```

`belongsTo()` inserts the association key in the **source** model.
`hasOne()` inserts the association key in the **target** model.

## One-to-many

One-to-many associations are connecting one source with multiple targets. The targets however are again connected to exactly one specific source.

```js
var User = sequelize.define('user', {/* ... */})
var Project = sequelize.define('project', {/* ... */})

// OK. Now things get more complicated (not really visible to the user :)).
// First let's define a hasMany association
Project.hasMany(User, {as: 'Workers'})
```

Each user has one project, but a project can have many users.

This will add the attribute of `projectId` to User. Instances of Project will get the accessors `getWorkers` and `setWorkers`.

If the source is `hasMany`, for the `Target`, we get: `getTargets`, `setTargets`, `addTarget`, `createTarget`, and `removeTarget`.

## Many-to-many

```js
User.belongsToMany(Project, {through: 'user_project_assignments'});
Project.belongsToMany(User, {through: 'user_project_assignments'});
```

This sets up a many-to-many relationship between User and Project.

So what happens?
1) Sets up a "foreign key table" / "join table". The join table would have two columns: `userId` and `projectId`.
2) Association methods get set up for us.

If the source is `belongsToMany`, for the `Target`, we get: `getTargets`, `setTargets`, `addTarget`, `createTarget`, and `removeTarget`.

---


# Model methods

## getterMethods
Methods that are run when you retrieve an item from the database.

`this` = the instance

```js
getterMethods: {
    route: function() {
        return "/wiki/" + this.urlTitle;
    }
},
```

## setterMethods
Methods that can replace a value before being stored in the database.

`this` = the instance

```js
setterMethods: {
    tags: function(tags) {
        tags = tags || [];
        if (typeof tags === 'string') {
            tags = tags.split(',').map(function (str) {
                return str.trim();
            });
        }

        this.setDataValue("tags", tags);
    }
},
```

## classMethods
Methods that are run at the Model or Class level. They are invoked.

`this` = the class

```js
classMethods: {
    findByTag: function(tag) {
        return Page.findAll({
            where: {
                tags: {
                    $contains: [tag]
                }
            }
        });
    }
},
```

## instanceMethods
Methods that act on each instance.

```js
instanceMethods: {
    findSimilar: function() {
        return Page.findAll({
            where: {
                id: {
                    $ne: this.id
                },
                tags: {
                    $overlap: this.tags
                }
            }
        });
    }
}
```

## hooks
These can be run before of after certain events.

`this` = class

```js
hooks: {
    beforeValidate: (page) => {
        page.urlTitle = generateUrlTitle(page.title);
    }
},
```



# Querying

## Types

```js
Model.findAll()
Model.findById()
Model.findOne()
```

## Complex queries
Find more information in the docs for [Querying > Where](http://docs.sequelizejs.com/en/latest/docs/querying/#where).

```js
getSiblings: function() {
    return Task.findAll({
        where: {
            parentId: this.parentId, // Find all with same parent id
            id: {
                $ne: this.id // Exclude itself from search
            }
        }
    });
}
```

## Eager loading
Joining model tables to give you extra information .

`Comment` is the Model name.
```js
Secret.findById(req.params.secretId, {
    include: [Comment]
})
```

Eager load all relationships
```js
{ include: [ {all: true } ]}
```
