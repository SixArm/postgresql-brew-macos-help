# PostgreSQL brew macOS help

This page helps with:

* PostgreSQL database
 
* brew package manager

* macOS Ventura

This page is a work in progress. Constructive feedback is appreciated.


## What's installed?

To see what's already installed:


```sh
% brew list | grep postgresql
```

If there's more than one PostgreSQL version installed via brew, then that's OK. 


## Uninstall an old version

If you do have an old PostgreSQL version installed via brew, then you may prefer to uninstall it.

Example to remove PostgreSQL version 14:

```sh
% brew uninstall postgresql@14
% brew cleanup postgresql@14
```

Example to remove PostgreSQL without a brew version:

```sh
% brew uninstall postgresql
% brew cleanup postgresql
```


## Install PostgreSQL 15

Run:

```sh
brew install postgresql@15
```

Output should be like:

```sh
This formula has created a default database cluster with:
  initdb --locale=C -E UTF-8 /opt/homebrew/var/postgresql@15

For more details, read:
  https://www.postgresql.org/docs/15/app-initdb.html

postgresql@15 is keg-only, which means it was not symlinked into /opt/homebrew,
because this is an alternate version of another formula.

If you need to have postgresql@15 first in your PATH, run:
  echo 'export PATH="/opt/homebrew/opt/postgresql@15/bin:$PATH"' >> ~/.zshrc

For compilers to find postgresql@15 you may need to set:
  export LDFLAGS="-L/opt/homebrew/opt/postgresql@15/lib"
  export CPPFLAGS="-I/opt/homebrew/opt/postgresql@15/include"

To restart postgresql@15 after an upgrade:
  brew services restart postgresql@15
  
Or, if you don't want/need a background service you can just run:
  /opt/homebrew/opt/postgresql@15/bin/postgres -D /opt/homebrew/var/postgresql@15
```

## Link

If you have more than one PostgreSQL version installed via brew, then you may want to adjust which version is linked.

Run:

```sh
% brew link postgresql@15                              
Linking /opt/homebrew/Cellar/postgresql@15/15.1... 383 symlinks created.
```


## Verify brew formula

Set:

```sh
formula=postgresql@15
```

What version is installed?

```sh
% brew list --version $formula
postgresql@15 15.1
```

Where is it installed?

```sh
% brew --cellar $formula
/opt/homebrew/Cellar/postgresql@15
```

Does the cellar directory contain the corret version?

```sh
% ls $(brew --cellar $formula)
15.1
```


## Get PostgreSQL 15 path

Save the path so we can use it for our next steps:

```sh
% cellar=$(brew --cellar $formula)
% version=$(brew list --version $formula | head -1 | awk '{print $2}')
% path="$cellar/$version"
% echo $path
/opt/homebrew/Cellar/postgresql@15/15.1
```


## Connect with psql

To connect with the command `psql`:

```sh
psql
```

To connect to the default database named "postgres" with the default user which is your macOS username:

```sh
psql postgres
```

To connect to the default database named "postgres" with a specific username such as "alice":

```sh
psql postgres -U alice
```

To connect to the default database named "postgres" with a specific username such as "brew", for example because you've configured your macOS system with a "brew" user:

```sh
psql postgres -U brew
```





### Verify postgres directory

Verify the postgres directory exists and is correctly owned by admin:

    $ ls -ladg /usr/local/var/postgres
    drwxr--r--  2 admin  68 Dec  5 03:20 /usr/local/var/postgres

Verify there is a plist file:

    $ ls /usr/local/opt/postgresql/*.plist
    /usr/local/opt/postgresql/homebrew.mxcl.postgresql.plist

Do you have a conflict with Postgres.app?

   $ ls /Applications/Postgres.app
   $ ls ~/Applications/Postgres.app

Verify Postgres is running:

    $ ps auxwww | grep post

Verify you can connect:

    $ psql -U postgres

Verify you can start:

    $ postgres -D /usr/local/var/postgres


### Verify postgres user

Verify the postgres user exists:

```sh
% $path/bin/createuser -s postgres
createuser: creation of new role failed: ERROR:  role "postgres" already exists
```


## Launch automatically

Optional setup to launch automatically:

```
% mkdir -p ~/Library/LaunchAgents
% ln -sfv /usr/local/opt/postgresql/*.plist ~/Library/LaunchAgents
% launchctl load ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist
```


## Nuclear option 

Totally delete all the existing databases, then recreate a fresh one:

    $ rm -rf /usr/local/var/postgres
    $ initdb /usr/local/var/postgres -E utf8
    $ /usr/local/Cellar/postgresql/9.6.1/bin/createuser -s postgres
    $ pg_ctl -D /usr/local/var/postgres -l logfile start
    $ psql postgres -c 'CREATE EXTENSION "adminpack";'
    $ psql -U postgres


