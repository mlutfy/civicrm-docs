# CiviCRM documentation infrastructure

A summary of CiviCRM's documentation infrastructure.

Note: If you just want to read our documentation, please see https://civicrm.org/documentation for an up to date list of documentation for users, administrators and developers.

## Contributing to documentation

Our documentation is currently split between a readthedocs at https://docs.civicrm.org, and a wiki at https://wiki.civicrm.org. Our aim is to move all documentation to readthedocs. To aid with the migration, please avoid making substantial improvements to the wiki and focus your efforts on improving documentation in readthedocs.

Once we have moved the majority of our documentation from the wiki to readthedocs, we will switch the documentation home page from https://civicrm.org/documentation to https://docs.civicrm.org/ and retire the wiki for documentation.

The rest of this document focuses on readthedocs based documentation.

If you have any questions about how stuff works, or how to start contributing, please ask in the documentation chat room (https://chat.civicrm.org/civicrm/channels/documentation).

### How documentation is organised

CiviCRM documentation is organised into various *books*.  The source text for these books is stored in various git repositories, for example https://github.com/civicrm/civicrm-user-guide. Books can be translated into different languages. A seperate repository is used for each langauge.  Different versions of a book can be made if required by creating branches in a repository. See *Defining books* below for more information.

All documentation is written in Markdown, and is structured following [mkdocs](http://www.mkdocs.org) conventions. 

## Publishing updates to documentation

Books are automatically published when the corresponding branch is updated their repository. This is typically acheived by making edits and submitting a pull request. Any emails listed in the commits that are submitted as part of the pull request will receive an email with a summary of the update process.

If required, a book can be manually updated by calling a URL in the following format: https://docs.civicrm.org/admin/publish/{book}/{en}/{branch}.

* {book} the name of the book - as per configuration file in the conf/books directory.
* {lang} the language that you want to publish - as defined in the configuration file.
* {branch} the name of the branch that you want to publish - needs to be a branch in the repository for that language.

### Setting up automatic publishing of documentation

Auto updates are configured via github webhooks.

1. Go to https://github.com/civicrm/[repo-name]/settings/hooks/new
2. Set the **Payload URL** to 'http://docs.civicrm.org/admin/listen'
3. Set the **Content type** to 'application/json'
3. Set the **Secret** to match the secret as defined in app/config/parameters.yml
4. Set **Which events would you like to trigger this webhook?** to 'Let me select individual events' and select 'Pull request' and 'Push' (since these are the only events that should trigger an update)

## Defining books

Books are defined with a Yaml configuration file. To define a new book, create a yaml file and add it to the app/config/books/ directory of this repository.

The config file defines the name of the book, the repository that contains the source code, and the **languages** that the book is available in, with a repository for each language. For each language, the configuration file defines:

* which **edition** of the book should be considered **stable**
* where to find the **latest** edits to the book
* a history of book **editions** of the book (these will be publicly listed at https://docs.civicrm.org/).

If you would like to be notified by email whenever an update to a book is published, you can add your email to the notify list.

**Example book definition:**
```yml
name: User guide
description: Aimed at day to day users of CiviCRM.
langs:
    en:
        repo: 'https://github.com/michaelmcandrew/civicrm-user-guide'
        latest: master
        stable: 4.7
        history:
            - 4.6
            - 4.5
            - 4.4
        notify:
            michael@civicrm.org # will be notified when documentation is published (as well as any emails mentioned in commits)
    ca:
        repo: 'https://github.com/babu-cat/civicrm-user-guide-ca'
        latest: master
        # stable: master (will not be published)
```



# Installation

**Note**: the following steps are only useful and necessary for people looking after CiviCRM's documentation infrastructure.. If you are want to contribute to CiviCRM's documentation, there see [Contributing to documentation](#contributing-to-documentation) above.

1) Ensure that that you have [pip](https://packaging.python.org/en/latest/install_requirements_linux/#installing-pip-setuptools-wheel-with-linux-package-managers) (for python) and [composer](https://getcomposer.org/) (for php) installed..

2) Install mkdocs (`$ sudo pip install mkdocs`). ***Note:*** *Ensure that mkdocs is installed as root so that it can be accessed from the src/publish.php script (typically invoked as https://docs.civicrm.org/publish.php)*

3) clone this repository to somewhere like /var/www/civicrm-docs and run `composer install`

```
$ git clone /var/www/civicrm-docs
$ cd /var/www/civicrm-docs
$ composer install
```

4) Set appropriate permissions on web/static

5) Configure an nginx virtual host

```
$ cd /etc/nginx/sites-enabled
$ ln -s /var/www/civicrm-docs/app/config/nginx.conf civicrm-docs
```

6) Reload your nginx config and you should be up and running.

# To do

* Move this todo list to another issue tracker (either this one or infra)
* create a seperate repo for app/config/books where we can define books independently of this repo
* create pdf and ePub versions of the document when publishing (maybe using pandoc)
* find a nice userfriendly UI for people to edit the documentation (the github UI is OK but we can do better)
* should doc infra interact with extension info.xml files?
* A CiviCRM theme for documentation which includes the civicrm version
* Book validation as part of publishing ( missing images (`ack '\!\[.*\]\((.*?)( ".*)?\)' -h --nobreak --output='$1'` will give all images)
* Future proof documentation structure
    * / - documentation home
    * /user/ - user documentation for core civicrm
    * /extensions/[extenstion_name] - documentation for extensions (probably mostly user focused, though with possible developer and system administrator sections)
    * /admin/ - system administrator documentation (installation, upgrades, email server configuration)
    * /dev/ - developer documentation
* Last but not least: migrate lots of documentation (e.g. our developer and sys administrator documentation)
