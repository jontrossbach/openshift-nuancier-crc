# openshift-nuancier-crc
Setup for deploying nuancier in OpenShift development environment


Description
-----------
Do you want to set up an instance of Fedora's [nuancier](https://github.com/fedora-infra/nuancier) in OpenShift for development purposes? Then you've come to the right repo. Whether you are setting up OpenShift locally using [crc](https://developers.redhat.com/products/codeready-containers/overview) or have an instance of OpenShift running somewhere, this repo contains all the yaml templates and example config files that you will need.


Usage
---------------------
The `buildconfig.yml` in the templates directory currently points to my personal fork/branch of nuancier which, as of this writing, is two pull requests ahead ([#124](https://github.com/fedora-infra/nuancier/pull/124) and [#127](https://github.com/fedora-infra/nuancier/pull/127)) of the origin repo. To set up a development environment of your own it might be advisable to make a fork of the origin repo yourself instead of forking the repo that is already in the buildcofig.yml of this repo. If you do that, the following steps are the quickest way to get your application up and running in OpenShift with the Source to Image (s2i) tool:

1.  Create an [app.py](https://github.com/jontrossbach/nuancier/blob/nuancier_crc_openshift/app.py) so that s2i has something to recognize as an entrypoint
1.  Create an [.s2i/environment](https://github.com/jontrossbach/nuancier/blob/nuancier_crc_openshift/.s2i/environment) and set all the required s2i [enviroment variables](https://github.com/sclorg/s2i-python-container/blob/master/3.6/README.md#environment-variables) that you may need.
1.  Now point the `buildconfig.yml` from this repo to your personal fork of nuancier and you should be ready for deploying to OpenShift


Getting Started Deploying to OpenShift
--------------------
Given that you have your openshift environment set up, it is time to start deploying the nuancier application.

Nuancier uses a PostgreSQL database. To have OpenShift spin up one of these for you, do the following command with the parameters adjusted responsibly (e.g. change the password):
```
oc new-app postgresql-persistent --name database --param DATABASE_SERVICE-NAME=database --param POSTGRESQL_DATABASE=sampledb --param POSTGRESQL_USER=username --param POSTGRESQL_PASSWORD=password
```
Once the database is ready, adjust the `alembic.ini` and the `nuancier.cfg` to reflect database's new URL. (You may have to expose the database with `oc expose`.) From there you can start creating all the templates from the templates and files directory with `oc create`.

Once all the requisite files have been created, you will be ready to start your first build which can be done through the console or with the oc tool. When the build finishes nauncier should be accessable after you `oc expose` it.
