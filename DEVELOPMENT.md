# Development

This module can be used in any development environment: DrupalVM, Lando, DDEV, Docksal, LAMP, XAMP, MAMP, etc. Another option is to use the quick-start command that comes with Drupal core. Follow the instructions below to use this approach.

## Using Drupal core development server

Drupal core comes with a [quick-start command](https://www.drupal.org/docs/installing-drupal/drupal-8-quick-start-command) that can be used for testing locally. It will install Drupal using a SQLite database and start PHP's built-in server.

These instructions also use the [recommended-project composer template](https://www.drupal.org/docs/develop/using-composer/starting-a-site-using-drupal-composer-project-templates) for installing Drupal 9. This setup assumes you have [Composer](https://getcomposer.org/) installed. To run Drupal 9 with this set you need to meet the following [requirements](https://www.drupal.org/docs/understanding-drupal/how-drupal-9-is-made-and-what-is-included/environment-requirements-of):

* PHP 7.3. Check with this command: `php --version`
* SQLite 3.26. Check with this command: `sqlite3 --version`
* Drush 10 for running the migrations. Check with this command: `./vendor/bin/drush --version`

```
# Get Drupal 9 via composer
composer create-project drupal/recommended-project:^9.0.0 migrations_basic

cd migrations_basic

# Add Olivero
composer require 'drupal/olivero:^1.0'

# Add Drush
composer require drush/drush

# Add contrib modules
composer require 'drupal/migrate_plus:^5.1' 'drupal/migrate_tools:^5.0' 'drupal/migrate_source_csv:^3.4' 'drupal/entity_reference_revisions:^1.8' 'drupal/paragraphs:^1.12' 'drupal/address:^1.8'

# At the time of publication, a patch is needed for migrate_tools module.
# The following 3 commands are only needed until this issue is resolved:
# https://www.drupal.org/node/3117485

# Add ability to patch modules
composer require cweagans/composer-patches

# Edit composer.json file per instructions in patching migrate_tools module
# section.
vim composer.json

# Apply the patch
composer install

# Create modules/custom folder
mkdir -p web/modules/custom

# Get demo module with GIT
cd web/modules/custom && git clone git@github.com:dinarcon/drupal_migrations_basic.git && mv drupal_migrations_basic ud_staff && rm -rf ud_staff/.git && cd ../../..

# Or using CURL
cd web/modules/custom && curl -LO https://github.com/dinarcon/drupal_migrations_basic/archive/main.zip && unzip main.zip && rm main.zip && mv drupal_migrations_basic-main ud_staff && cd ../../..

# Or using WGET
cd web/modules/custom && wget https://github.com/dinarcon/drupal_migrations_basic/archive/main.zip && unzip main.zip && rm main.zip && mv drupal_migrations_basic-main ud_staff && cd ../../..

# Install Drupal. The built-in server might stop working from time to time. If
# that is the case, press Ctrl-C to quit the Drupal development server. Then run
# the same command again to restart the development server.
php web/core/scripts/drupal quick-start standard --site-name "UnderstandDrupal.com/migrations" --suppress-login

# Enable the modules
./vendor/bin/drush pm-enable -y migrate_plus migrate_tools migrate_source_csv entity_reference_revisions paragraphs address ud_staff

# Import content
./vendor/bin/drush migrate:import --tag='UD Migrations Basic Example'

# Check the imported content at /staff
# See TROUBLESHOOTING.md if there are any issues.

# Rollback content
./vendor/bin/drush migrate:rollback --tag='UD Migrations Basic Example'

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
