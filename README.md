# Understand Drupal Migrations Course - Basic package

A demo module created by [Mauricio Dinarte](https://www.drupal.org/u/dinarcon) ([@dinarcon](https://twitter.com/dinarcon)) to explain migrations concepts in Drupal.

This module is part of the [Understand Drupal Migrations Course](https://understanddrupal.com/migrations) which can be purchased at https://udrupal.com/get-migrations-course

## Dependencies

The following projects are required to run this demo. The numbers indicate which version of the project was last used for testing.

* [Drupal](https://www.drupal.org/project/drupal) 9.1.5
* [Address](https://www.drupal.org/project/address) 8.x-1.9
* [Entity reference revisions](https://www.drupal.org/project/entity_reference_revisions) 8.x-1.9
* [Migrate plus](https://www.drupal.org/project/migrate_plus) 8.x-5.1
* [Migrate source csv](https://www.drupal.org/project/migrate_source_csv) 8.x-3.4
* [Migrate tools](https://www.drupal.org/project/migrate_tools) 8.x-5.0
* [Paragraphs](https://www.drupal.org/project/paragraphs) 8.x-1.12
* [Drush](https://github.com/drush-ops/drush) 10.3.6

### Specific Drush version required

Drush `10.4` and later is not compatible with `migrate_tools <= 5`. Until a `6.x` branch is released for `migrate_tools`, Drush needs to be pinned to `^10.3.0` via Composer.

## Examples

This demo includes six migrations. Three of them import content from CSV files and the other three from a JSON file.

* `ud_staff_csv_paragraph` for importing data into paragraphs entities from a CSV file. There are no dependencies on other migrations.
* `ud_staff_csv_image` for importing data into file entities from a CSV file. There are no dependencies on other migrations.
* `ud_staff_csv_node` for importing data into node entities from a CSV file. This migration depends on the other two: `ud_staff_csv_paragraph` and `ud_staff_csv_image`. These need to be executed in advance for the node migration to work.

* `ud_staff_json_paragraph`, `ud_staff_json_image`, `ud_staff_json_node` are very similar, but read from a JSON file instead. Another difference is that these migrations are defined as configuration entities.

## Instructions

* Install module dependencies via Composer: `composer require 'drupal/migrate_plus:^5.1' 'drupal/migrate_tools:^5.0' 'drupal/migrate_source_csv:^3.4' 'drupal/entity_reference_revisions:^1.9' 'drupal/paragraphs:^1.12' 'drupal/address:^1.9'`
* Install the **Drush 10.3.x** via Composer: `composer require 'drush/drush:^10.3.0'`. After this step, you may call it via `./vendor/bin/drush`.
* Make sure that your Drupal installation has a `/modules/custom` folder. The `modules` folder should exist, but the `custom` sub-folder might not. Create it if needed.
* Download the demo module contained in this repository into the `/modules/custom` folder. You can do this by cloning this repository or [downloading a ZIP file](https://github.com/dinarcon/drupal-migrations-basic/archive/main.zip). **Important:** In either case, you need to rename the folder to `ud_staff`. Otherwise, the CSV migrations might not work.
* Verify that the CSV files are in the proper location. See TROUBLESHOOTING.md file for details.
* Enable the UD Staff Example Migration (`ud_staff`) module.
* Run migrations using Drush or from the user interface. See instructions below.
* Check out the DEVELOPMENT.md file for an option to configure a local development environment.
* Check out the COMMANDS.md file for some of the commands that were used throughout the course.

### Importing the migrations

Run any of the following Drush commands to get the content imported:

* To migrate only from CSV files execute: `./vendor/bin/drush migrate:import --tag='UD CSV Source'`
* To migrate only from JSON files execute: `./vendor/bin/drush migrate:import --group='ud_staff_json_examples'`
* To migrate from both CSV and JSON files execute: `./vendor/bin/drush migrate:import --tag='UD Migrations Basic Example'`

Alternatively, JSON migrations can also be executed from the administration interface for the UD Staff JSON Examples migration group.

After the migrations are run successfully, visit /ud-staff to see the content that was imported.


If the import operation fails, make sure that path to the CSV and JSON files are properly configured. Refer to the TROUBLESHOOTING.md file for details.

### Rolling back the migrations


Depending on which migrations were imported, you can roll them back using one these Drush commands:

* To roll back only from CSV files execute: `./vendor/bin/drush migrate:rollback --tag='UD CSV Source'`
* To roll back only from JSON files execute: `./vendor/bin/drush migrate:rollback --group='ud_staff_json_examples'`
* To roll back from both CSV and JSON files execute: `./vendor/bin/drush migrate:rollback --tag='UD Migrations Basic Example'`

Alternatively, JSON migrations can also be rolled back from the administration interface for the UD Staff JSON Examples migration group.
