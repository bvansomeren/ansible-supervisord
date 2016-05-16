bvansomeren.supervisord
=========

Role installs supervisord on CentOS (from package) configures it and sets up applications based on configuration.

Requirements
------------

None

Role Variables
--------------

All variables in use, including defaults. See also Example Playbook

	supervisord_packages:
  	  - supervisor

In case you need to override the package name

	supervisord_configuration_file: "/etc/supervisord.conf"

And

	supervisord_configuration_folder: "/etc/supervisord.d/"

This controls the location of the config file / supervisord.d folder. Override if you want to use a non-privileged account for example.

	supervisord_config:
	  - { section: unix_http_server, option: username, value: "YourUser" }
	  - { section: unix_http_server, option: password, value: "TopSecretPassword" }

Allows overriding of specific configuration parameters from the defaults or current values.
On to subprocesses; These are optional. If you do not specify this, `supervisord` will only be installed and started without subprocesses.

	supervisord_subprocess_name:

Defines the unit name; IE if you name the application httpd, you'll supervise it with `supervisorctl start httpd`

	supervisord_subprocess_type: "program"

Defines the type of subprocess. Any of the types supported by Supervisor are acceptable

	supervisord_subprocess_options: []

A list of options to put into your subprocess configuration file. See example and documentation for Supervisor.


Dependencies
------------

None

Example Playbook
----------------

Just some example; Be sure to download Payara Micro for the Java app
		
	- hosts: all
  	  become: yes
   	  become_user: root
  	  roles:
  	  - geerlingguy.java
  	  - { role: bvansomeren.supervisord,
    	      supervisord_subprocess_name: "httpd",
    	      supervisord_subprocess_options: [
      		{ option: "command", value: "java -jar /opt/payara-micro-4.1.1.162.jar" },
      		{ option: "autostart", value: "true" },
      		{ option: "autorestart" ,value: "true" },
      		{ option: "user", value: "payara" },
      		{ option: "stderr_logfile", value: "/var/log/payara.err.log" },
      		{ option: "stdout_logfile", value: "/var/log/payara.out.log" } ] }
  	  - { role: bvansomeren.supervisord,
    	      supervisord_subprocess_name: "long",
    	      supervisord_subprocess_options: [
      		{ option: "command", value: "/opt/long.sh" },
      		{ option: "autostart", value: "true" },
      		{ option: "autorestart" ,value: "true" },
      		{ option: "stderr_logfile", value: "/var/log/long.err.log" },
      		{ option: "stdout_logfile", value: "/var/log/long.out.log" } ] }
  	 pre_tasks:
  	 - user: name=payara state=present
  	 - copy: src=long.sh dest=/opt/long.sh mode=0700
  	 - copy: src=payara-micro-4.1.1.162.jar dest=/opt/payara-micro-4.1.1.162.jar	

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
