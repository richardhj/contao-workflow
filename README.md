Contao Workflow Boileplate
==========================

This repository holds a concept of a Contao development and deployment
workflow. This workflow is something I figured out for me personally but is
open for feedback for sure.

This workflow is splitted in two parts:

1. Frontend theme development
2. Project deployment


Frontend theme development
--------------------------

As many others, I tried a lot of frontend theme development mehtods. Starting
from the internal CSS genenrator, I then tried external CSS files and SASS with
Compass.

For now I use a very satisfying frontend workflow. Since then, working with old
projects, not using this workflow, feels awful.

The workflow comes with the [euf_nutshell][10]. The nutshell itself has a SCSS
boilerplate and is built in a modular manner. Meaning: Each component has its
own SCSS file, like `_events.scss`, `_navs.scss` or `_layout.scss`.

The documentation can befound at http://nutshell.erdmann-freunde.de/. The
nutshell uses BEM syntax, has a mobile-first approach and really fastens your
work. Big props to [Dennis Erdmann](http://erdmann-freunde.de/).

I usually start with the [euf_nutshell_kit][11]. You can start by creating a
composer project upon the Contao managed edition:

```
composer create-project erdmannfreunde/euf_nutshell_kit [path]
```

Make sure to replace `[path]` with the name of the folder you want to install
your Contao installation in.

Once installed, you should find a `gulpfile.js` and a `package.json` in the
root of your Contao installation and a theme folder within `/files`. You
can develop your theme within this theme folder, while `gulp` is running
constantly in the background.

Please see [this guide][9] to get to know, how to install the gulp components.

The euf_nutshell_kit also comes with a customized `.gitignore` and a
boilerplate `.htaccess`.


Project deployment
------------------

The whole Contao project should be checked in via version control, preferably
git.

To start, use the following commands. We will push the project repository to
GitHub, but you can consider GitLab (which has free private repos) or other
providers as well.

```bash
git init
git add .
git commit -m "Initial files"
git remote add origin https://github.com/USER/example.org.git
git push origin master
```

When you're ready to deploy on the production or staging, continue:

### Clone project on production

1. Make sure, you have a SSH Key on your production server: `ls -al ~/.ssh`. If true, skip the next step.
2. Create SSH-Key on server: `ssh-keygen -t rsa -b 4096`
3. Show public SSH-Key `cat ~/.ssh/id_rsa.pub`, copy to clipboard.
4. Add public SSH-Key as Deploy Key to GitLab Repository settings.
5. Finally clone the project repository and install composer dependencies.

```bash
git clone https://github.com/USER/example.org.git
cd example.org
composer install --no-dev -o --prefer-dist
```
The path `example.org/web` then should become your website root.

Now you import your database on the live system and you're ready to go live.

The following commands need to be executed, every time you want to update the
project. (Hint: you need to push the files from the dev repo beforehand)

```bash
git pull origin
composer install --no-dev -o --prefer-dist
```

That's it!

### GitLab CI

With GitLab CI it is possible to test your application on every commit and automatically deploy the project on staging or production.

Basically, I don't need to login on the server and git pull anymore. It saves some time ¯\_(ツ)_/¯

We proceed as follows:

1. Create a new key-value pair (`ssh-keygen -t rsa -b 4096`). Do not overwrite your default one, save in extra folder!
2. Add the public key to the `authorized_keys` on the production server.
3. Save the private key as protected variable `SSH_PRIVATE_KEY` in the GitLab CI / CD config of the project.
4. Alter the [`.github-ci.yml`](.github-ci.yml), then add it to your project and push to GitLab.

Within the `build_app` I run [`phpcq`](https://phpcq.github.io/) tasks, e.g. author-validation, composer-validation and runningn php tests (if available). The necessary files are within this repostiory.

### Git versionized database

It is possible to check in the project's database in version control.

Major benefits: Easy rollback of database plus regular backup due of an entry
in the crontab.

These are the files I create on demand, either on the live system and/or on the
dev system:

* `.git/hooks/pre-commit` (Creates the database dump with each commit)
* `.git/hooks/post-merge` (Imports the database given in the repository. Data
loss predictable.)

You can find the hook files in the repository.

After copying the file, run `chmod +x .git/hooks/pre-commit` to make the hook
executable.

#### `.my.cnf`

Evaluate if you need to add a `.my.cnf`.

```bash
echo "[client]
user=example
password=supersecret" > ~/.my.cnf
```

Run `chmod 0600 .my.conf` for hardening to read the credentials.

### Crontab. Regular backup

I added a crontab job that commits every changed file and the current databse
dump to the remote repository (e.g. GitHub, GitLab).

`crontab -e`, then add the following line(s):

```
MAILTO="me@example.org"
@daily (cd /var/www/www.example.org/htdocs/contao && git add . && git commit -m "The daily" && git push)
```

### app_dev.php

In order to enable the `app_dev.php` on the non-localhost environments, use:

```php vendor/bin/contao-console contao:install-web-dir --user=USER --password=PASSWORD```

Alter the username and password in the snipped above.

Commit the file `.env`.


I also add a `README.md` to my project which aims to be comprehensible for
people being unfamiliar with the project. The README is the following:

-------------


# example.org

This project holds the following websites bundled within one Contao installation:

* https://www.example.org
* https://www.example.com
* https://intern.example.org

## Contao 4 managed edition

Contao is an Open Source PHP Content Management System for people who want a
professional website that is easy to maintain. Visit the [project website][1]
for more information.


### System requirements

 * Web server
 * PHP 7.1.0+ with GDlib, DOM and Phar
 * MySQL 5.5.7+
 * InnoDB with `innodb_large_prefix` enabled


#### InnoDB large prefix

MySQL versions prior to 5.7.7 do not have the `innodb_large_prefix` option
enabled by default. To enable it in one of these versions, add the following
to your `my.cnf` file:

```
innodb_large_prefix = 1
innodb_file_format = Barracuda
innodb_file_per_table = 1
```

If the option cannot be enabled on your server, please configure a different
database engine and character set in your `app/config/config.yml` file:

```yml
doctrine:
    dbal:
        connections:
            default:
                default_table_options:
                    charset: utf8
                    collate: utf8_unicode_ci
                    engine: MyISAM
```


### Installation

See the [installation chapter][2] of the user's manual.


### Documentation

 * [User's manual][3]
 * [Developers's manual][4]
 * [Cookbook][5]
 * [API reference][6]


### License

Contao is licensed under the terms of the LGPLv3.


### Getting support

Visit the [support page][7] to learn about the available support options.


## Nutshell Framework

The Frontend Themes are based on [Nutshell][8]. Nutshell is a basis framework for Contao website layouts and comes with
a specific workflow. This workflow prescribes how to work with the website layout.

### Installation

Please ensure that your working dir has the following structure.

```
|-- CONTAO
  |-- .editorconfig (Einstellungen für Code-Editor, wenn unterstützt)
  |-- files
    |-- my-project (enthält die SCSS-Dateien)
  |-- gulpfiles.js
  |-- package.json
  |-- templates
    |-- my-project (angepasste Templates)
  |-- README.md
```

Please follow [this guide][9] in order to get to run your development environment.

You usually run `gulp` and access the website at `localhost:3000`.

With `gulp deploy` you prepare the theme for production. Commit the changed files afterwards.

-----------------


[1]: https://contao.org
[2]: https://docs.contao.org/books/manual/current/en/01-installation/installing-contao.html
[3]: https://docs.contao.org/books/manual/current/
[4]: https://docs.contao.org/books/extending-contao4/
[5]: https://docs.contao.org/books/cookbook/
[6]: https://docs.contao.org/books/api/
[7]: https://contao.org/en/support.html
[8]: http://nutshell.erdmann-freunde.de/
[9]: http://nutshell.erdmann-freunde.de/installation-einrichtung/einrichtung.html
[10]: https://github.com/ErdmannFreunde/euf_nutshell
[11]: https://github.com/ErdmannFreunde/euf_nutshell_kit

