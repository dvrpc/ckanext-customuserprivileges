# ckanext-customuserprivileges

ckanext-customuserprivileges is a [CKAN](https://github.com/ckan/ckan) extension for adding additional restrictions for dataset management. It adds an extra autocomplete field to the dataset to specify users who can administer it. This works for both unowned datasets that by default can be managed by any authenticated user and datasets within company which requires the member to have sufficient permissions within the company and additionally be a manager.

## Installing

This plugin was original developed in CKAN v2.4. After a very minor tweak to the setup.py file, it also appears to work on CKAN v2.9.

```sh
source /usr/lib/ckan/default/bin/activate
cd /usr/lib/ckan/default/src
git clone https://github.com/dvrpc/ckanext-customuserprivileges
cd ckanext-customuserprivileges
python setup.py develop
```

Then add to `customuserprivileges` to the list of plugins in the configuration file.

## Usage

Extra field for specifying dataset administrators is visible when creating or editing the dataset. Users will be specified by their username.![Extra field](https://i.imgur.com/HVG2ofP.png)

By default when creating a dataset and not specifying any administrators, only the user who created it can manage it. For other users following applies:

Unowned dataset + dataset administrator = **can edit**

Company dataset + admin or editor within company + dataset administrator = **can edit**

### With ckanext-scheming

To use with [ckanext-scheming](https://github.com/ckan/ckanext-scheming/), add the following field to your dataset schema:

```
{
  "field_name": "managing_users",
  "label": "Dataset administrators",
  "preset": "dataset_admin"
}
```

Create a presets.json file if it doesn't already exist, or add to it:

```
{
  "dvrpc_presets_version": 1,
  "about": "a preset for the customeruserprivileges plugin",
  "about_url": "",
  "presets": [
    {
      "preset_name": "dataset_admin",
      "values": {
        "validators": "ignore_missing",
        "classes": ["control-full"],
        "form_attrs": {
          "data-module": "autocomplete",
          "data-module-tags": "",
          "data-module-source": "/api/2/util/user/autocomplete?ignore_self=true&q=?"
        }
      }
    }
  ]
}
```

Finally, include the preset in your configuration file:

```
scheming.presets = ckanext.dvrpc_theme:presets.json ckanext.scheming:presets.json
```

where `ckanext.dvrpc_theme` is your theme and `presets.json` is the name of the presets file.

See [DVRPC's custom theme](https://github.com/dvrpc/ckanext-dvrpc_theme) for a full example.

## Running tests

```sh
# activate virtualenv
source /usr/lib/ckan/default/bin/activate

# install test dependencies
pip install -r /usr/lib/ckan/default/src/ckan/dev-requirements.txt

# create test database (if not created)
sudo -u postgres createdb -O ckan_default ckan_test -E utf-8

# edit test database connection string
nano /usr/lib/ckan/default/src/ckan/test-core.ini

# enter extension directory
cd /usr/lib/ckan/default/src/ckanext-customuserprivileges

# run test suite
nosetests --ckan --with-pylons=test.ini ckanext/customuserprivileges/tests --nocapture
```
