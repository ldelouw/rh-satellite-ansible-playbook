# This is a playbook to set up a Satellite 6 server

## Author, Copyright and License
This playbook is created by Luc de Louw <luc@delouw.ch>, <ldelouw@redhat.com>. It is free software licensed under the terms of the GNU General Public License GPL v3 or later. A copy of the license can be obtained here: http://www.gnu.org/licenses/gpl-3.0.html

## Motivation
I've wrote this Playbook because I do a lot of experiments with the Red Hat Satellite, trying new stuff and 
sometimes it breaks (i.e. when upgrade to a new beta relase). Manual installation takes a lot of time and it is boring as hell. Work smarter, not harder!

## Scope
This playbook is not meant for daily tasks such as creating and provision new hosts and the like,
it's just for the initial configuration

## Things covered in this playbook
At the moment, the Playbook covers the most important topics for getting a Satellite up and running

## Compatibility
This version of the playbook was tested to be working with Satellite Version 6.13. Older versions of Statellite need some minor changes in the vars.yml as well as the software requirements

## Prerequisites
* Subscribe the system, and ensure to meet the requirements like sizing and firewall rules as described here: https://access.redhat.com/documentation/en-us/red_hat_satellite/6.10/html-single/installing_satellite_server_from_a_connected_network/index
* Shortcut for lab systems:
	* subscription-manager list --all --available --matches 'Red Hat Satellite Infrastructure Subscription'
	* subscription-manager attach --pool=pool_id
	* subscription-manager repos --disable "*"
    * subscription-manager repos --enable=rhel-8-for-x86_64-baseos-rpms \
--enable=rhel-8-for-x86_64-appstream-rpms \
--enable=satellite-6.13-for-rhel-8-x86_64-rpms \
--enable=satellite-maintenance-6.13-for-rhel-8-x86_64-rpms
    * dnf module enable satellite:el8
	* yum -y update && reboot
* Install the EPEL repository (Note: Not suppoerted by Red Hat)
    *dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm*
* Install the Satellite,python3.11-jmespath.noarch and the Ansible collection for Red hat Satellite: *dnf -y install satellite ansible-collection-redhat-satellite ansible-collection-ansible-posix.noarch ansible-collection-community-general.noarch python3.11-jmespath.noarch git*
* **IMPORTANT!** Disable EPEL on Red Hat Satellite Systems as there are conflicting packages!
    *dnf config-manager --set-disabled epel*

* Satellite Installer was run with the enabledment of the TFTP feature:
    *satellite-installer --scenario satellite --foreman-initial-organization=lab --foreman-proxy-tftp=true*
* If you want to use OpenSCAP for compiance checking, run *foreman-rake foreman_openscap:bulk_upload:default*
* Clone this repo: *git clone https://github.com/ldelouw/rh-satellite-ansible-playbook.git*

## Things *NOT* set up:
For security reasons (I dont want to accidentially leak some real life credentials)
* Register and subscribe the Satellite to Redhat CDN
* Setting up an IPA Realm, you need to join the Satellite manually
* Setting up a libvirt compute resource, however, an example is provided but commented out

For technical reasons
* Running the Satellite installer (Just takes too long to execute and brings no benefit)
* Anything that has to do with Puppet, as Puppet ist depricated in Satellite 6

For the sake of lazyness
* Using more than one Organisation. If you are using more than one Org, please use
  multiple vars.yml files per Org.

To be done
* More sophisticated activation key configuration such as subs and repo overrides
* Creating a Letsencrypt cerfiicate using certbot
* More general settings
* IPA/Kerberos authentication
* Remove "Default Location"
* Configuring Capsule servers

## How to use
First of all, copy the vars.yml.dist to vars.yml and edit the file, replace everything with example.com 
with your real life environment. Please also read the comments for better understanding

Alter that, copy the passwd.yml.dist to passwd.yml and change the vault password with *ansible-vault rekey passwd.yml*, the password is *changeme*. Edit the passwd.yml file with *ansible-vault edit passwd.yml* and use your real Satellite password and if needed uncomment the default_http_proxy_passwd and change to the real world Proxy password

Then, simply run it with *ansible-playbook sat.yml --ask-vault*

Note: It's recommended to have some basic Ansible knowledge to operate this playbook.

## Contributions
Contributions are very welcome :-) Just send me a pull-request

