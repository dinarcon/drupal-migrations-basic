# Commands

This is a list of some commands use throughout the course.

```
# Create modules/custom folder.
mkdir -p web/modules/custom

# Get example module source code.
cd web/modules/custom && git clone git@github.com:dinarcon/drupal_migrations_basic.git && mv drupal_migrations_basic ud_staff && rm -rf ud_staff/.git && cd ../../..

# Enable example.
./vendor/bin/drush pm-enable -y ud_staff

# Import all migrations.
./vendor/bin/drush migrate:import --tag='UD Migrations Basic Example'

# Roll back all migrations.
./vendor/bin/drush migrate:rollback --tag='UD Migrations Basic Example'

# Uninstall example module. This removes the included configuration: content type, fields, paragraph type, etc.
./vendor/bin/drush pm:uninstall -y ud_staff ud_staff_setup
```

```
./vendor/bin/drush migrate:status --fields=id,status,total,imported,unprocessed,last_imported
./vendor/bin/drush migrate:import ud_staff_csv_node_init
./vendor/bin/drush migrate:rollback ud_staff_csv_node_init
# Make the system detect changes to files under /migrations.
./vendor/bin/drush cache:rebuild
./vendor/bin/drush migrate:import ud_staff_csv_node_init
```

```
./vendor/bin/drush migrate:import ud_staff_csv_node_init
./vendor/bin/drush migrate:status ud_staff_csv_node_init
./vendor/bin/drush migrate:stop ud_staff_csv_node_init
./vendor/bin/drush migrate:reset-status ud_staff_csv_node_init
./vendor/bin/drush migrate:rollback ud_staff_csv_node_init
./vendor/bin/drush cache:rebuild
./vendor/bin/drush migrate:import ud_staff_csv_node_init
```

```
./vendor/bin/drush migrate:import --tag='UndestandDrupal.com'
./vendor/bin/drush migrate:import --tag='UD CSV Source'
./vendor/bin/drush migrate:import --group='ud_staff_json_examples'
./vendor/bin/drush migrate:import --tag='UD Migrations Basic Example'
./vendor/bin/drush migrate:import --all

./vendor/bin/drush migrate:rollback --tag='UD CSV Source'
./vendor/bin/drush migrate:rollback --group='ud_staff_json_examples'
./vendor/bin/drush migrate:rollback --tag='UD Migrations Basic Example'
./vendor/bin/drush migrate:rollback --tag='UndestandDrupal.com'
./vendor/bin/drush migrate:rollback --all
```

```
# Connect to the database.
./vendor/bin/drush sql:cli

# At the SQLite prompt.
.help
.tables
.tables paragraph%

select * from paragraph__field_ud_book_title;

.headers ON
.mode column

select * from paragraph__field_ud_book_title;
select bundle, entity_id, revision_id, delta, field_ud_book_title_value from paragraph__field_ud_book_title;

select bundle, entity_id, revision_id, delta, field_ud_book_author_value from paragraph__field_ud_book_author;

.schema paragraph__field_ud_book_title
.exit
```

```
# Apply the configuration changes to the migrations configuration entities under config/install.
./vendor/bin/drush --yes config-import --partial --source=modules/custom/ud_staff/config/install

# Generate a UUID.
./vendor/bin/drush php:eval "echo \Drupal::service('uuid')->generate(). PHP_EOL;"
```
