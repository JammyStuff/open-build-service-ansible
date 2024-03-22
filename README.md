open-build-service
==================

Installs and configures the [Open Build Service (OBS)](https://openbuildservice.org/).

Requirements
------------

This role is written for OpenSUSE, and is currently tested on OpenSUSE Leap 15.5. It makes use of the `zypper` and `zypper_repository` modules, so you need to install the `python-xml` package. Due to the use of the firewalld module, you must use Python 3 for your `ansible_python_interpreter`.

Role Variables
--------------

### obs_version

The version of OBS to install. Only 2.10 is tested. The default is 2.10.

### obs_opensuse_version

The version of OpenSUSE OBS is being installed on. The default is `ansible_distribution_version`.

### obs_fqdn

The fully qualified domain name for this OBS instance. The default is "obs.internal".

### obs_cert_domains

The domains to use when generating a self-signed certificate. The default is ["{{ obs_fqdn }}", "obs", "localhost"].

### obs_xdelta1_compat

Enable compatibility with xdelta1. openSUSE now packages xdelta3 in a way where it pretends to be xdelta1, but not very well. This causes an issue for pristine-tar, which is sometimes used in Debian packaging, when xdelta1 was used. Setting this to true will replace the xdelta symlink from the xdelta3 package with an actual xdelta1 binary. The default is false.

### obs_xdelta_rpm_filename

The filename of the xdelta rpm if using xdelta1 compatibility. The default is "xdelta-1.1.4-193.d_t.3.x86_64.rpm".

### obs_sign_server

Address of the signing server to use. By default this is 127.0.0.1, meaning that the local signing server set up as part of the OBS server will be used. Change this if you are using a detached signing server.

### obs_sign_user

The user to use for signing. Note that this corresponds to the email address of the GPG key that should be used. By default this is defaultkey@localobs, and this is the address that will be used for the key generated for the local signer created by this role. If you are using a detached signing server, it is recommended that you use a different email address for the GPG key on that machine and set this variable to it.

### obs_configuration_title

The title that appears on the web user interface landing page.

### obs_configuration_description

The description that appears on the web user interface landing page.

### obs_configuration_name

The instance name which gets generated into each package. Short string only, no white spaces recommended.

### obs_configuration_anonymous_on_off

Sets policy on whether anonymous access is permitted. The values 'on' or 'off' are valid.

### obs_configuration_registration_allow_disallow

Sets policy on whether user self registration is permitted. The values 'allow' and 'disallow' are valid.

### obs_configuration_default_access_disabled_on_off

Sets policy on whether default access is disabled. The values 'on' or 'off' are valid.

### obs_configuration_permit_user_home_on_off

Sets policy on whether a user is permitted to have a home project. The values 'on' or 'off' are valid.

### obs_configuration_group_creation_on_off

Sets policy on whether group creation is permitted. The values 'on' or 'off' are valid.

### obs_configuration_change_password_on_off

Sets policy on whether a user can change their password. The values 'on' or 'off' are valid.

### obs_configuration_hide_private_options_on_off

Sets policy on whether private options are hidden. The values 'on' or 'off' are valid.

### obs_configuration_gravatar_on_off

Sets policy on whether a gravatar should be used in the web user interface. The values 'on' or 'off' are valid.

### obs_configuration_enforce_project_keys_on_off

Sets policy on whether to enforce projects keys. The values 'on' or 'off' are valid.

### obs_configuration_download_on_demand_on_off

Sets policy on whether download on demand is permitted. The values 'on' or 'off' are valid.

### obs_configuration_download_url

Sets download url made available in the web user interface within the built package. Either unset, or a URI is valid.

### obs_configration_main_url

Sets the main url made available in the web user interface. A valid URI should be set.

### obs_configuration_admin_email

Sets the admin email. Should be a valid email address.

### obs_configuration_cleanup_empty_projects_on_off

Sets policy on whether OBS should perform a cleanup of empty projects. The values 'on' or 'off' are valid

### obs_configuration_cleanup_after_days

Sets the number of days before project cleanup. An integer value is valid.

### obs_configuration_disable_publish_for_branches_on_off

Sets policy on whether to disable publishing for branches. The values 'on' or 'off' are valid

### obs_configuration_publish_hooks

This is a string that allows for arbitrary addition statements to inserted into the configuration file. It is intended for adding things like custom `publishedhook =`.
See: https://openbuildservice.org/help/manuals/obs-admin-guide/obs.cha.administration.html#_publisher_hooks

### obs_server_architectures

Provides a list of architectures supported by the OBS instance, provided as a UML list.
e.g.
obs_service_architectures:
  - x86_64

### obs_cert_path

Path to a certificate for the web server to use. Disables self-signed certificate generation when set.

### obs_key_path

Path to a key for the web server to use.

### obs_src_address

Address of the source server. Defaults to localhost.

### obs_repo_address

Address of the repository server. Defaults to localhost.

### obs_service_address

Address of the service server. Defaults to localhost.

### obs_cloudupload_address

Address of the cloudupload server. Defaults to localhost.

### obs_workersrc_address

Address of the worker source server. Defaults to localhost.

### obs_workerrepo_address

Address of the worker repository server. Defaults to localhost.

Dependencies
------------

This role uses the `weareinteractive.hosts` role to ensure that an entry for 127.0.1.1 is present in /etc/hosts. This is required for remote workers to work properly. The `hosts_entries` variable is not set by this role to avoid clashes where you are using the `hosts` role for other purposes. Make sure that you have the variable set appropriately to create an entry for the FQDN of the node to point to 127.0.1.1. This could be done by adding the following to your host_vars:

    hosts_entries:
    - ip: 127.0.1.1
      domain: "{{ ansible_fqdn }} {{ ansible_hostname }}"

Example Playbook
----------------

    - hosts: servers
      roles:
         - jammystuff.open_build_service

Development
-----------

The easiest way to test your changes is using [Vagrant](https://www.vagrantup.com/). Once you have installed Vagrant, you can create and provision a VM like this:

    $ cd open-build-service
    $ vagrant up

After making changes, you can run Ansible again like this:

    $ vagrant provision

Once you're finished, you can destroy the VM like this:

    $ vagrant destroy

License
-------

BSD 3-Clause
