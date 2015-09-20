# uWSGI Run Directories role

This is an Ansible role to create directories for each of your [uWSGI][]
vassals as managed by `uwsgi-emperor`. (If you use the Debian `uwsgi`
package, it creates directories like these by default, but this is not the
case under `uwsgi-emperor`. I don't know why; I only seek to compensate
for it.) This directory can be referenced in your uWSGI vassal configs to
tell it where to put sockets and pidfiles.

Role Variables
--------------

    uwsgi_rundir: /run/uwsgi

The base path under which subdirectories for each vassal will be created.
This role will make sure that the base path exists.

    uwsgi_owner: www-data
    uwsgi_group: www-data

Owner and group for the directories created for this group. Defaults to
the ones that work best for Debian.

    uwsgi_vassals: []

This variable comes from `cchurch.uwsgi` (see Dependencies below) and is
a list of hashes that define which vassals get configured. A subdirectory
under `uwsgi_rundir` is created for each vassal, named after that vassal's
`name` member. You can then add `socket` and `pidfiles` members to the
vassal's configuration hash to define file paths, like so:

    uwsgi_vassals:
        - name: trac
          plugin: python
          chown-socket: "www-data:www-data"
          uid: trac
          gid: trac
          env: "TRAC_ENV=/var/lib/trac"
          module: "trac.web.main:dispatch_request"
          socket: "{{uwsgi_rundir}}/trac/trac.sock"
          pidfile: "{{uwsgi_rundir}}/track/trac.pid"

Dependencies
------------

This role depends on the [`cchurch.uwsgi` role][cchurch] and its
`uwsgi_vassals` variable, as described above.

[cchurch]: https://github.com/cchurch/ansible-role-uwsgi

Example Playbook
----------------

    - hosts: servers
      roles:
         - slaarti.uwsgi-rundir
         - cchurch.uwsgi

License
-------

Unlicense; see the LICENSE file

Author Information
------------------

Chris Pinard <slaarti@gmail.com>
