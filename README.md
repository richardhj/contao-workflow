Contao Workflow Boileplate
==========================

This repository holds a concept of a Contao development and deployment
workflow. This workflow is something I figured out for me personally but is
open for feedback for sure.


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

The documentation can befound at http://nutshell.erdmann-freunde.de/. However,
I switched the gulp part to encore.

I ususally start with an empty Contao project by running the following:

```
git clone git@github.com:terminal42/contao-standard.git && cd contao-standard && composer require --dev erdmannfreunde/euf_nutshell
```

I use the terminal42 edition, as it comes with the required files for the
encore workflow.

Then, I copy the [SCSS files][9] of the nutshell kit into the `/layout` folder.
The resulting directory structure looks like this:

```
|-- CONTAO
  |-- files
  |-- package.json
  |-- layout
    |-- images
    |-- js
    |-- styles
      |-- base
      |-- components
      |-- mixinx
      |-- trumps
      |-- _variables.scss
      |-- app.scss
  |-- README.md
```

After setting up your Contao (site structure, theme etc.), you can start building your
theme with `npm run dev` keep running.

Add the style sheet to your layout with the following lines:

```
<link rel="stylesheet" href="{{asset::layout/css/app.css}}">
<script src="{{asset::layout/js/app.js}}"></script>
```


Project deployment
------------------

I deploy using Deployer. Please make sure to follow the latest instructions from
https://github.com/terminal42/deployer-recipes.

### trakked.io

I use https://trakked.io to track my projects for uptime and overview of used versions.
With an automatated GitLab CI task I will make sure, that the project is always using the
latest Contao version.


### GitLab CI

The `.gitlab.ci.yml` hold two tasks:

The `save_database_dump` task will download a database dump. This task is scheduled
(via the GitLab interface) to run once every week.

The `composer_update` task will update the Contao project via `composer update` and
will re-deploy project once every week within the GitLab Runner.

Now, the versions are up-to-date and the interface of trakked.io will show you so :)

-----------------


[1]: https://contao.org
[2]: https://docs.contao.org/books/manual/current/en/01-installation/installing-contao.html
[3]: https://docs.contao.org/books/manual/current/
[4]: https://docs.contao.org/books/extending-contao4/
[5]: https://docs.contao.org/books/cookbook/
[6]: https://docs.contao.org/books/api/
[7]: https://contao.org/en/support.html
[8]: http://nutshell.erdmann-freunde.de/
[9]: https://github.com/ErdmannFreunde/euf_nutshell_kit/tree/develop/files/starterkit/src
[10]: https://github.com/ErdmannFreunde/euf_nutshell

