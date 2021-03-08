# Development

This module can be used in any development environment: DrupalVM, Lando, DDEV, Docksal, LAMP, XAMP, MAMP, etc. Another option is to use the quick-start command that comes with Drupal core. Follow the instructions below to use this approach.

## Using Drupal core development server

Drupal core comes with a [quick-start command](https://www.drupal.org/docs/installing-drupal/drupal-8-quick-start-command) that can be used for testing locally. It will install Drupal using a SQLite database and start PHP's built-in server.

These instructions also use the [recommended-project composer template](https://www.drupal.org/docs/develop/using-composer/starting-a-site-using-drupal-composer-project-templates) for installing Drupal 9. This setup assumes you have [Composer](https://getcomposer.org/) installed. To run Drupal 9 with this set you need to meet the following [requirements](https://www.drupal.org/docs/understanding-drupal/how-drupal-9-is-made-and-what-is-included/environment-requirements-of):

* PHP 7.3. Check with this command: `php --version`
* SQLite 3.26. Check with this command: `sqlite3 --version`
* Drush **10.3.x** for running the migrations. Check with this command: `./vendor/bin/drush --version`

Drush `10.4` and later is not compatible with `migrate_tools <= 5`. Until a `6.x` branch is released for `migrate_tools`, Drush needs to be pinned to `^10.3.0` via Composer.

```
# Get Drupal 9 via composer.
composer create-project drupal/recommended-project:^9.1 migrations-basic

# If you get memory limit errors when running composer, prepend the command with
# COMPOSER_MEMORY_LIMIT=-1
# More information at https://getcomposer.org/doc/articles/troubleshooting.md#memory-limit-errors
COMPOSER_MEMORY_LIMIT=-1 composer create-project drupal/recommended-project:^9.1 migrations-basic

cd migrations-basic

# Add Drush
composer require 'drush/drush:^10.3.0'

# Add contrib modules
composer require 'drupal/migrate_plus:^5.1' 'drupal/migrate_tools:^5.0' 'drupal/migrate_source_csv:^3.4' 'drupal/entity_reference_revisions:^1.9' 'drupal/paragraphs:^1.12' 'drupal/address:^1.9'

# At the time of publication, a patch is needed for migrate_tools module.
# The following 4 commands are only needed until this issue is resolved:
# https://www.drupal.org/node/3117485

# Add ability to patch modules
composer require 'cweagans/composer-patches:^1.7'

# Edit composer.json file per instructions in patching migrate_tools module
# section.
vim composer.json

# Validate composer.json file.
composer validate

# Apply the patch
composer install

# Create modules/custom folder
mkdir -p web/modules/custom

# Get demo module with GIT over HTTPS.
cd web/modules/custom && git clone https://github.com/dinarcon/drupal-migrations-basic.git && mv drupal-migrations-basic ud_course && rm -rf ud_course/.git && cd ../../..

# Get demo module with GIT over SSH.
cd web/modules/custom && git clone git@github.com:dinarcon/drupal-migrations-basic.git && mv drupal-migrations-basic ud_staff && rm -rf ud_staff/.git && cd ../../..

# Or using CURL
cd web/modules/custom && curl -LO https://github.com/dinarcon/drupal-migrations-basic/archive/main.zip && unzip main.zip && rm main.zip && mv drupal-migrations-basic-main ud_staff && cd ../../..

# Or using WGET
cd web/modules/custom && wget https://github.com/dinarcon/drupal-migrations-basic/archive/main.zip && unzip main.zip && rm main.zip && mv drupal-migrations-basic-main ud_staff && cd ../../..

# Install Drupal. The built-in server might stop working from time to time. If
# that is the case, press Ctrl-C to quit the Drupal development server. Then run
# the same command again to restart the development server.
php web/core/scripts/drupal quick-start standard --site-name "UnderstandDrupal.com/migrations" --suppress-login

# Set Claro as the admin theme.
./vendor/bin/drush theme:enable claro && ./vendor/bin/drush --yes config:set system.theme admin claro

# Set Olivero as the default (frontend) theme. If using Drupal 8.9, install it via Composer: composer require 'drupal/olivero:^1.0@beta'
./vendor/bin/drush theme:enable olivero && ./vendor/bin/drush --yes config:set system.theme default olivero

# Enable the modules
./vendor/bin/drush pm:enable --yes ud_staff

# Import content
./vendor/bin/drush migrate:import --tag='UD Migrations Basic Example'

# Check the imported content at /ud-staff
# See TROUBLESHOOTING.md if there are any issues.

# Rollback content
./vendor/bin/drush migrate:rollback --tag='UD Migrations Basic Example'

# Uninstall example module. This removes the included configuration: content type, fields, paragraph type, etc.
./vendor/bin/drush pm:uninstall --yes ud_staff ud_staff_setup
```

If you are using a different development environment, make sure to meet Drupal's [system requirements](https://www.drupal.org/docs/system-requirements).


## Patching migrate_tools module

Add the following snippet as a child of the `extra` section in `composer.json`:

```
"patches": {
  "drupal/migrate_tools": {
    "Migrate import --execute-dependencies fix": "https://www.drupal.org/files/issues/2020-03-06/fetch-migration-requirements-3117485-2.patch"
  }
}
```
