# Ansible Role: `supervisor`

[![CI](https://github.com/shaneholloman/ansible-role-supervisor/actions/workflows/ci.yml/badge.svg)](https://github.com/shaneholloman/ansible-role-supervisor/actions/workflows/ci.yml)

An Ansible Role that installs [Supervisor](http://supervisord.org/) on Linux.

## Requirements

Python `pip` should be installed. If it is not already installed, you can use the `shaneholloman.pip` Ansible role to install Pip prior to running this role.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

```yml
supervisor_version: ''
```

Install a specific version of Supervisor by setting it here. See [available Supervisor versions](https://pypi.python.org/pypi/supervisor) on Pypi. If no version is set, it will install the latest stable version of Supervisor when the role is run.

```yml
supervisor_started: true
supervisor_enabled: true
```

Choose whether to use an init script or systemd unit configuration to start Supervisor when it's installed and/or after a system boot.

```yml
supervisor_config_path: /etc/supervisor
```

The path where Supervisor configuration should be stored.

```yml
supervisor_programs:
  - name: 'foo'
    command: /bin/cat
    state: present

  - name: 'apache'
    command: apache2ctl -DFOREGROUND
    state: present
    configuration: |
      autostart=true
      autorestart=true
      startretries=1
      startsecs=1
      redirect_stderr=true
      stderr_logfile=/var/log/apache-err.log
      stdout_logfile=/var/log/apache-out.log
      user=root
      killasgroup=true
      stopasgroup=true
```

`supervisor_programs` is an empty list by default; you can define a list of `program`s to be managed by Supervisor. If you set `state` to `present`, then a configuration file for the program (named `[program-name-here].conf`) will be added to the `conf.d` path included by the global Supervisor configuration. You can also manage program-level configuration on your own, outside this role, if you need more flexibility.

```yml
supervisor_nodaemon: false
```

Set to `true` if you need to run Supervisor in the foreground.

```yml
supervisor_log_dir: /var/log/supervisor
```

The location where Supervisor logs will be stored.

```yml
supervisor_user: root
supervisor_password: 'my_secret_password'
```

The user under which `supervisord` will be run, and the password to be used when connecting to Supervisor's HTTP server (either for `supervisorctl` access, or when viewing the administrative UI).

```yml
supervisor_unix_http_server_password_protect: true
supervisor_inet_http_server_password_protect: true
```

Password protection can be turned off for Unix HTTP and Inet HTTP by setting these variables to `false`, This would disable password protection for `supervisorctl` as well.

```yml
supervisor_unix_http_server_enable: true
supervisor_unix_http_server_socket_path: /var/run/supervisor.sock
```

Whether to enable the UNIX socket-based HTTP server, and the socket file to use if enabled.

> **Note**: By default, this role enables an HTTP server over a UNIX socket that can be accessed locally using the `_user` and `_password` defined earlier. Make sure you set a secure `supervisor_password` to prevent unauthorized access! (Or, if you don't need to HTTP server nor need to use `supervisorctl`, you should disable the UNIX http server by setting this variable to `false`).

```yml
supervisor_inet_http_server_enable: false
supervisor_inet_http_server_port: '*:9001'
```

Whether to enable the TCP-based HTTP server, and the interface and port on which the server should listen if enabled.

## Dependencies

None.

## Example Playbook

```yml
- hosts: all
  roles:
    - shaneholloman.pip
    - shaneholloman.supervisor
```

If you need to use `supervisorctl`, you can either use [Ansible's built-in `supervisorctl` module](https://docs.ansible.com/ansible/latest/collections/community/general/supervisorctl_module.html) for management, or run it like so (accounting for the variable path to the configuration directory):

```sh
supervisorctl -c /etc/supervisor/supervisord.conf -u root -p [password] status all
```

## License

Unlicense

## Author Information

This role was created in 2023
