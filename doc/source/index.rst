===============================
Nova role for OpenStack-Ansible
===============================

.. toctree::
   :maxdepth: 2

   configure-nova.rst

:tags: openstack, nova, cloud, ansible
:category: \*nix

This role will install the following Systemd services:
    * nova-server
    * nova-compute

To clone or view the source code for this repository, visit the role repository
for `os_nova <https://github.com/openstack/openstack-ansible-os_nova>`_.

Default variables
~~~~~~~~~~~~~~~~~

.. literalinclude:: ../../defaults/main.yml
   :language: yaml
   :start-after: under the License.

Dependencies
~~~~~~~~~~~~

This role needs pip >= 7.1 installed on the target host.

Example playbook
~~~~~~~~~~~~~~~~

.. literalinclude:: ../../examples/playbook.yml
   :language: yaml

External Restart Hooks
~~~~~~~~~~~~~~~~~~~~~~

When the role performs a restart of the service, it will notify an Ansible
handler named ``Manage LB``, which is a noop within this role. In the
playbook, other roles may be loaded before and after this role which will
implement Ansible handler listeners for ``Manage LB``, allowing external roles
to manage the load balancer endpoints responsible for sending traffic to the
servers being restarted by marking them in maintenance or active mode,
draining sessions, etc. For an example implementation, please reference the
`ansible-haproxy-endpoints role <https://github.com/Logan2211/ansible-haproxy-endpoints>`_
used by the openstack-ansible project.

Tags
~~~~

This role supports two tags: ``nova-install`` and ``nova-config``

The ``nova-install`` tag can be used to install and upgrade.

The ``nova-config`` tag can be used to manage configuration.

CPU platform compatibility
~~~~~~~~~~~~~~~~~~~~~~~~~~

This role supports multiple CPU architecture types.  At least one repo_build
node must exist for each CPU type that is in use in the deployment.

Currently supported CPU architectures:
 - x86_64 / amd64
 - ppc64le

At this time, ppc64le is only supported for the Compute node type. It can not
be used to manage the OpenStack-Ansible management nodes.


Compute driver compatibility
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This role supports multiple nova compute driver types. The following
compute drivers are supported:

- libvirt (default)
- ironic
- lxd (via nova-lxd)

The driver type is automatically detected by the OpenStack Ansible Nova role
for the following compute driver types:

- libvirt (kvm / qemu)

Any mix and match of compute node types can be used for those platforms,
except for ironic.

If using the lxd driver, the compute type must be specified using the
``nova_virt_type`` variable.

The ``nova_virt_type`` may be set in
``/etc/openstack_deploy/user_variables.yml``, for example:

.. code-block:: shell-session

   nova_virt_type: lxd

You can set ``nova_virt_type`` per host by using ``host_vars`` in
``/etc/openstack_deploy/openstack_user_config.yml``. For example:

 .. code-block:: shell-session

   compute_hosts:
    aio1:
      ip: 172.29.236.100
      host_vars:
        nova_virt_type: lxd

If ``nova_virt_type`` is set in ``/etc/openstack_deploy/user_variables.yml``,
all nodes in the deployment are set to that hypervisor type.  Setting
``nova_virt_type`` in both ``/etc/openstack_deploy/user_variables.yml`` and
``/etc/openstack_deploy/openstack_user_config.yml`` will always result in the
value specified in ``/etc/openstack_deploy/user_variables.yml`` being set on
all hosts.
