pwm
=========

PWM is an open source password self service application for LDAP directories. PWM is an ideal candidate for organizations that wish to “roll their own” password self service solution, but do not wish to start from scratch.

This ansible role eases the implementation of the pwm software. Installation based off of this documentation: https://github.com/pwm-project/pwm/wiki/Installation-on-Ubuntu-Server-16.04-LTS

Requirements
------------

Ubuntu and debian OS supported
python-ldap package required: install is handled in the role if you don't already have it

Role Variables
--------------

* tomcat specific
  * `tomcat_directory`: PWM is a java app deployed to tomcat. This is the directory that your tomcat instance folders are held in. Default upon tomcat installation is: `/var/lib`
  * `tomcat_instance`: Folder name for the instance you want pwm to be deployed to. default is `tomcat8`
  * `pwm_HTTP_server`: Right now this role only supports nginx as the reverse proxy
  * `pwm_site_config`: Where your server config is located. Default: `/etc/{{ pwm_HTTP_server }}/sites-available/pwm.conf`
  * `pwm_HTTP_configured` : At this point this role doesn't configure a reverse proxy from scratch, it assumed you already have one. Default: `true`
* LDAP specific
  * `pwm_ldap_type` : This role only supports openldap at this time
  * `pwm_ldap_domain` : Your ldap should already be setup, if not you can use [tiedtoastar.openldap-debian](https://github.com/tiedtoastar/ansible-role-openldap-debian) to do so. Newly registered users will reside in this subtree also. Default= `"dc=example,dc=com"`
  * `pwm_ldap_subtree` : Specify which subtree contains your users for PWM. Default `ou=people,{{ pwm_ldap_domain }}` 
  * `pwm_ldap_admin_group`: Specify the group of users that you want to administer PWM configs via the web. Default `cn=pwmwebadmins,ou=groups,{{ pwm_ldap_domain }}`
  * `pwm_ldap_test_user_sn` & `pwm_ldap_test_user`: A test user is used by PWM to monitor functionality such as the ability for the software to change passwords, etc. The test user will be created for you by this role
  * `pwm_ldap_bind_user` & `pwm_ldap_bind_pwd` : The account that is used to allow PWM to communicate with your ldap. Default: `cn=pwm-ldap-service,ou=machineaccounts,{{ pwm_ldap_domain }}`
  * `pwm_ldap_db`: the type of database your ldap is using. Default: `mdb`
  * `pwm_ldap_urls`: list of urls for PWM to connect to. Note that PWM does support LDAP clustering. The testuser is used to determine which ldap servers are available to be included in the cluster. Default: `ldap://127.0.0.1:389`
* DB specific
  * `pwm_db` : PWM stores user information in a database. `LOCALDB` is the only one that has been test. But a remote RDBMS should work
  * Other db parameters referenced in the PWM config are included in the defaults, but I have not tested them.
* PWM specific
  * `pwm_release` : The release that you would like to download and configure. Default: 1.9.1
  * `pwm_new_account_registration` : Whether you want to allow new users to register. Default: `true`
    * New users can register here: https://www.example.com/pwm/public/newuser 
    * The default profile is used for creating new accounts. 
    * The default config only requires new users to specify a username AND captcha is not required
  * `pwm_site_url` : Default: https://www.example.com/pwm
  * `pwm_securitykey` : You have to provide the key when changing the configs within PWM. Default: `ChangeMe`
  * `pwm_newuser_redirecturl` : Where you want your users to be redirected to after they have registered. Default: `https://www.example.com`
  
  
Dependencies
------------

None

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: all
    become: true
      roles:
         - role: tiedtoastar.ansible-role-pwm
         pwm_ldap_domain: dc=example,dc=com
         pwm_site_config: /etc/nginx/sites-available/nginx.conf
         pwm_securitykey: Blah1234

License
-------

BSD

Author Information
------------------

TiedToAStar

Want to help?
------------------

If you find this project useful, please consider a donation to the following Monero address: 47q3TVnd79QcMLqFE2HJC5HTWDadUXtMDVavERPfeT3xFiBeqQQX6knBNALTz4aciC6pSbnLoMCHXXsQDCPV1BT7TqoqZxW

Haven't heard of Monero? Then do me the favor by checking out: https://www.getmonero.org/