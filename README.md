# PgRebase

PgRebase is a tool that allows you to easily handle your postgres codebase for
functions, triggers and views.

> Note: this is a in progress project, see the TODO section at the end 
> of this page to see what is actually implemented


## Why

If you started outsourcing data manipulation to your database through
postgresql cool features, you probably realized this is painful. This is not
your usual codebase, it lives in postgres, and you often have to drop your
function/trigger/view if you want to edit it. Migrating servers is not easy
either.

The classic tool for this is to have migration files. This is great for
handling tables, not so great to make frequent changes to your functions. Can
we do better?


## What

PgRebase allows you to manage your functions/triggers/views as plain files in
filesystem. You put them in a `sql/` directory, one file per
function/trigger/view.

```
$ tree sql/
sql/
├── functions/
│   └── assign_user_to_team.sql
├── triggers/
│   └── user_updated_at.sql
└── views/
    └── user_json.sql
```

No need to add drop statement in those files, PgRebase will take care of it.


## Install

Just clone the directory and run inside it:

```
go build -o bin/pgrebase .
```

If you don't want to install golang, a prebuilt binary for linux/amd64 can be
found in `bin/` (built with golang-1.6.2).

Copy the downloaded or built binary in your favorite place to execute it.

> Note: If you're used to golang, you can install pgrebase just the usual way
>
> `go get github.com/oelmekki/pgrebase`


## Usage

```
$ export DATABASE_URL=postgres://user:pass@host/db

$ ./pgrebase sql/
Loaded 10 functions
Loaded 25 views
Loaded 5 triggers - 1 trigger with error
  error while loading sql/triggers/user_updated_at.sql
  column users.updated_at does not exist

$ ./pgrebase -w sql/
Loaded 10 functions
Loaded 25 views
Loaded 6 triggers
Watching filesystem for changes...
sql/functions/create_user.sql changed. Building.
```

When working in development environment, you'll probably want to use watch mode
(`-w`) to have your changes automatically loaded.

For deployment, add `pgrebase` to your repos and call it after your usual
migrations step:

```
DATABASE_URL=your_config ./pgrebase ./sql
```


## TODO

* [ ] handle functions
* [ ] handle views
* [ ] handle triggers
* [ ] add watch mode