# Agile Model

**agile-model** was created to remove the pains of setting up models, migrations and database files for your awesome app in development. It also gives a nice clean highly maintainable project structure that you'll just love. It is also designed to be independent of whatever server-side framework you are using (express, koa, etc...)

# Installation

npm install -g agile-model

# Why

Because the time I spend creating the same structure always in my projects, I could use in coming up with more awesome features :)

# Usage

This is a global package and should be run at the root of your project.

## Initialization

To start with, run:

     agile-model init

This creates a file in your project root called **agility.js**. This is the configuration file agile-model will use to create the various entities in your app. We'll talk about this in the section [Using the **agility.js** file for initial configuration](#using-the-agility.js-file-for-initial-configuration)

## Setting Up

When you are ready to setup your project, you run:

    agile-model setup --database=<database-client>

This command installs **[knex](https://knexjs.org/)** sql builder and **[objection](https://vincit.github.io/objection.js/)** object relational mapper. You can specify a database client plugin using the `--database` or `-d` switch. If not specified the database client defaults to "**[pg](https://www.npmjs.com/package/pg)**".

Afterwards, agile-model bootstraps your project with the necessary project structure to get started. This structure is shown below:

    my-app
    +-- config/
    |   +-- objection.js
    +-- models/
    +-- migrations/
    +-- services/
    |   +-- db/
    |   |   +-- index.js
    +-- knexfile.js
    +-- package.json

### Setting up with or without routes and views folder

In your `models` array in the **agility.js** file, if you used a _path version_ of your model name (e.g. "entity/post"), agile-model will create a **views** and **routes** folder at the root of your project

    my-app
    +-- config/
    |   +-- objection.js
    +-- models/
    +-- migrations/
    +-- routes/
    +-- services/
    |   +-- db/
    |   |   +-- index.js
    +-- views/
    +-- knexfile.js
    +-- package.json

We talk about this in the section [Using the agility.js to setup routes and views structure](#using-the-agility.js-file-to-setup-routes-and-views-structure)

## Generating New Models

After you have started working on your project, you may need more models you hadn't set up at the beginning. Don't worry, agile-model has got you covered. Inspired by [Active Record](http://guides.rubyonrails.org/active_record_basics.html) in Ruby on Rails, agile-model allows you to scaffold new models. (I.e. create the model with it's associated database and migration files).

To generate a new model, run:

    agile-model generate [model-name]

This will create the model file in `models/` folder and add the corresponding database files in the `services/db/` folder. By convention, **agile-model** will use a singular noun version of the name you used with the generate command and a plural form for the table name.

> Running the command `agile-model generate person` will create a
> model file called `person.js` within which a class of **Person** will
> be created and the table name will be **people**

# Using the **agility.js** file for initial configuration

When beginning a new app, you might already have the structure for the database. If you do, you can specify it withing the **agility.js** file which is created by running `agile-model init`. The file looks like this:

```
/**
* Modify this file with the models and relations you want in your project
* Please refer to the agile model documentation to find out more
* https://github.com/kwameopareasiedu/agile-model#using-the-agility.js-file-for-initial-configuration
*/


module.exports = {
    models: [],
    relations: ""
};
```

In the **models** array is pretty straightforward. You put your model names within it.

The **relations** string is where the magic happens. You specify the relations between your models and agile-model will use that to create a model-graph, from which it will construct the files and file relations.

To illustrate this, let's take a simple scenario. Let's say we want to build a todo-list app where **users** can create **posts** and can also **comment** on posts. We can identify the following relations:

1.  A user has one or many posts

2.  A user has one or many comments

3.  A post has one or many comments

From this, the **agility.js** configuration for this project will be...

```
module.exports = {
    models: ["user", "post", "comment"],
    relations: "user HAS_MANY [post comment], post HAS_MANY comment"
}
```

...and that's it. Agile model will use this to build a dependency graph and create your entire structure. Simple, ain't it?

You might be wondering, _"I only specified a relation from **user** to **post**, what about from **post** to **user**?_.

No worries bruh. Agile model takes care of the reverse relations for you. From the model graph it can deduce that a post should belong to a user.

As of **v1.2.0**, the supported relations are "HAS_MANY", and "BELONGS_TO_ONE". In future releases, more relations will be supported.

## Using the agility.js file to setup routes and views structure

In the above section we looked at how to use the agility.js file in the most common case. You'd notice that after running `agile-model setup`, there're no **routes** or **view** folder. Perhaps, in addition you'd want a _routes_ folder (with files for each of the [CRUD operations](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)) such that it's structure would be:

    my-app
    ...
    +-- routes
    |   +-- entity
    |   |   +-- post
    |   |   |   +-- show-all.js
    |   |   |   +-- show-create.js
    |   |   |   +-- show-edit.js
    |   |   |   +-- show-one.js
    |   |   |   +-- create.js
    |   |   |   +-- edit.js
    |   |   |   +-- delete.js
    |   |   |   +-- index.js
    ...

In that case the way you would define your `models` array would be

    models = ["/entity/post"]

... instead of the previous

    models = ["post"]

This creates a route folder group with files for each Create, Read, Update, Delete operation. A similar structure is also made in **views** folder. However, you can add `--no-routes` or `--no-views` switch to the `agile-model setup` and `agile-model generate` commands to suppress routes or views folder creation respectively.

# Testing

agile-model uses [mocha](https://mochajs.org/) and [chai](http://www.chaijs.com/) assetion library to run unit tests. Integration tests will be available with future releases.

To run the tests, simply run:

npm test

# Contribution

If you love agile-model and want to help out, please feel free to clone, make the changes and submit a pull-request. If it checks out (with tests as well), I'll merge and add you to my contributors.

Visit the Github repository using [this link](https://github.com/kwameopareasiedu/agile-model)

If you'd also like to support me with any donations (monetary or otherwise), please send a mail and let's talk. I'll soon have a patreon account btw :)

If you discover any errors or malfunctions, please don't hesitate to open a issue on the repo and I'll look into it as soon as possible

# Contributors

-   [Kwame Opare Asiedu](https://github.com/kwameopareasiedu/)

# Changelog

### v1.2.0

-   Added **routes** and **views** folder structure generation to model scaffolding.
-   Fixed foreign key name resolution in `/migrations` folder
-   Added new `--no-routes` and `--no-views` switches to the `agile-model setup` and `agile-model generate` commands
-   Updated Readme

### v1.1.0

-   Added knex migration file support

-   Fixed issue where database client is ignored from `agile-model setup --database=<database-client>`

-   Updated Readme

### v1.0.1

-   Updated Readme

### v1.0.0

-   Removed options from commands

-   `agile-model init` now generates the agility.js config file

-   Added model graphing

-   Added new command to cmd interface `agile-model setup --database`

### v0.1.1

-   Fixed file path issue which caused [init error](https://github.com/kwameopareasiedu/agile-model/issues/1)
