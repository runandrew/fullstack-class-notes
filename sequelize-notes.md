# Sequelize Notes

# Contents
+ Relationships
  * Source and Target
  * 1:1 relationships
  * One-to-many Relationships
  * Many-to-many Relationships
+ Model creation
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
+ Establishing a database
+ Synchronizing the models


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
+ Association methods: `player.getTeam/setTeam/removeTeam`

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

This will add the attribute of `projectId` to User.
+ Association methods: `project.getWorkers/setWorkers/addWorker/addWorkers`

If the source is `hasMany`, for the `Target`, we get: `getTargets`, `setTargets`, `addTarget`, `addTargets`, `createTarget`, and `removeTarget`.

## Many-to-many

```js
User.belongsToMany(Project, {through: 'user_project_assignments'});
Project.belongsToMany(User, {through: 'user_project_assignments'});
```

This sets up a many-to-many relationship between User and Project.

So what happens?
1. Sets up a "foreign key table" / "join table". The join table would have two columns: `userId` and `projectId`.
2. Association methods get set up for us.
  * `getTargets`, `setTargets`, `addTarget`, `addTargets`, `createTarget`, and `removeTarget`.

---

# Model creation

```js
const Page = db.define('page', {
    title: {
        type: Sequelize.STRING,
        allowNull: false
    },
    urlTitle: {
        type: Sequelize.STRING,
        allowNull: false
    },
    content: {
        type: Sequelize.TEXT,
        allowNull: false
    },
    status: {
        type: Sequelize.ENUM('open', 'closed')
    },
    tags: {
        type: Sequelize.ARRAY(Sequelize.STRING),
        defaultValue: [],
        set: function (tags) {

            tags = tags || [];

            if (typeof tags === 'string') {
                tags = tags.split(',').map(function (str) {
                    return str.trim();
                });
            }

            this.setDataValue('tags', tags);

        }
    }
});
```

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

If there is an aliased association, use the following:
```js
Secret.findById(req.params.secretId, {
    include: [{model: User, as: 'director'}]
})
```

Eager load all relationships
```js
{ include: [ {all: true } ]}
```

# Establishing a database

```js
const Sequelize = require('sequelize');
const db = new Sequelize('postgres://localhost:5432/wikistack', {
    logging: false
});
```

# Synchronizing the models

```js
Promise.all([
    models.User.sync({}),
    models.Page.sync({})
])
.then(function () {
    server.listen(3001, function () {
        console.log('Server is listening on port 3001!');
    });
})
.catch(console.error);
```
