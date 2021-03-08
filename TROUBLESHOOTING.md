# Troubleshooting

## Drush command not defined or inability to install migrate_tools

This module works with Drupal 8 and 9. All the examples in this demo module assume Drush **10.3.x** is used. If you are using another version, the commands and their aliases might be different. Execute `./vendor/bin/drush list --filter=migrate` to verify the available commands for your version of Drush.

**Important:** Drush 10.4+ is not compatible with `migrate_tools` <= 5. Until a 6.x branch is release for `migrate_plus`, Drush needs to be pinned to `^10.3.0` via Composer.

If you are using Drush 10, make sure the [Migrate tools](https://www.drupal.org/project/migrate_tools) is **enabled** on the site. This module provides the commands for executing migrations from the command line.

## CSV files location

The `path` configuration for the CSV source plugin accepts either an absolute path or relative path from the Drupal's root folder. The examples use a relative path and that:

1. You place this demo module in a `modules/custom` folder.
1. The module's folder itself is name `ud_staff`.

Therefore, the CSV files should be located at `modules/custom/ud_staff/sources/`. **Whether you clone this repository or download a ZIP file with its content, you need to rename the folder appropriately.**

This would look similar to:

```
.
|-- autoload.php
|-- core
|-- index.php
|-- modules
|   |-- contrib
|   |   |-- address
|   |   |-- entity_reference_revisions
|   |   |-- migrate_plus
|   |   |-- migrate_source_csv
|   |   |-- migrate_tools
|   |   `-- paragraphs
|   `-- custom
|       `-- ud_staff
|           |-- COMMANDS.md
|           |-- composer.json
|           |-- config
|           |-- DEVELOPMENT.md
|           |-- migrations
|           |-- README.md
|           |-- sources
|           |-- TROUBLESHOOTING.md
|           |-- ud_staff.info.yml
|           `-- ud_staff_setup
|-- profiles
|-- robots.txt
|-- sites
|-- themes
|-- update.php
`-- web.config
```

Not having the source CSV files in the proper location would trigger and error similar to:

```
[error]  Migration failed with source plugin exception: File path (modules/custom/ud_staff/sources/ud_staff.csv) does not exist.
```

If you want to place the files in a different location, you have to update the `source:path` setting in the corresponding migration files.

## Disappearing paragraphs

Paragraphs migrations are affected by a particular behavior of revisioned entities. If the host entity is deleted, and the paragraphs do not have translations, they are scheduled to be deleted in the next cron run. That means that deleting a node will eventually remove its referenced paragraphs. Because of this, if the node migration is rolled back and cron is executed, the paragraphs will be removed without the migrate API being aware of it. In this example, execute the following commands to trigger this behavior:

```
# Roll back the CSV node migration.
./vendor/bin/drush migrate:rollback ud_staff_csv_node

# Execute cron.
./vendor/bin/drush cron

# Check the status of the CSV paragraph migrations. It reports all items have been imported while the paragraphs were deleted during cron.
./vendor/bin/drush migrate:status ud_staff_csv_paragraph

# Import the CSV node migration again.
./vendor/bin/drush migrate:import ud_staff_csv_node

# Visit the /ud-staff page and notice that the nodes were imported, but the book paragraphs are missing.
```

The purge of orphaned paragraphs is implemented at a [field](https://git.drupalcode.org/project/entity_reference_revisions/-/blob/8.x-1.8/src/Plugin/Field/FieldType/EntityReferenceRevisionsItem.php#L407) and [entity level](https://git.drupalcode.org/project/entity_reference_revisions/-/blob/8.x-1.8/entity_reference_revisions.module#L310) via a [queue worker](https://git.drupalcode.org/project/entity_reference_revisions/-/blob/8.x-1.8/src/Plugin/QueueWorker/OrphanPurger.php). In the past, the deletion was instantaneous. Today, the deletion is deferred to a later moment making this less likely to be noticed. Thanks to Damien McKenna for helping me understand this behavior.

In any migration project, it is common that you do roll back operations to test new field mappings or fix errors. Beware of it if you do paragraphs migrations. So, what do you do to recover the deleted paragraphs? You have to roll back both migrations: node and paragraph. And then, you have to import the two again. The following snippet shows how to do it for the CSV migrations:

* Roll back both migrations:
```
./vendor/bin/drush migrate:rollback ud_staff_csv_node
./vendor/bin/drush migrate:rollback ud_staff_csv_paragraph
```

* Import both migrations again:
```
./vendor/bin/drush migrate:import ud_staff_csv_paragraph
./vendor/bin/drush migrate:import ud_staff_csv_node
```
