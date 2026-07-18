==============================
OpenStack-Ansible Skyline role
==============================

.. toctree::
   :maxdepth: 2

Default variables
~~~~~~~~~~~~~~~~~

.. literalinclude:: ../../defaults/main.yml
   :language: yaml
   :start-after: under the License.

Configure and add service to your OpenStack-Ansible deployment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To add a new service to your OpenStack-Ansible (OSA) deployment:

* Define ``skyline_dashboard_hosts`` in your ``conf.d`` or
  ``openstack_user_config.yml``. For example:

  .. code-block:: yaml

      skyline_dashboard_hosts:
        infra1:
          ip: 172.20.236.111
        infra2:
          ip: 172.20.236.112
        infra3:
          ip: 172.20.236.113

* Create respective LXC containers (skip this step for metal deployments):

  .. code-block:: console

     openstack-ansible openstack.osa.containers_lxc_create --limit skyline_all,skyline_dashboard_hosts

* Run service deployment playbook:

  .. code-block:: console

     openstack-ansible openstack.osa.skyline

For more information, please refer to the `OpenStack-Ansible project documentation <https://docs.openstack.org/project-deploy-guide/openstack-ansible/latest/>`_.

Configuring Skyline SSO
~~~~~~~~~~~~~~~~~~~~~~~

Skyline can expose Keystone WebSSO login via:

.. code-block:: yaml

   skyline_sso_protocols:
     - openid

This sets ``sso_enabled``, ``sso_protocols``, and ``sso_region`` (from
``skyline_service_region``) in ``skyline.yaml``. It does **not** configure
Keystone federation. Operators still need an IdP, Keystone protocol/mapping,
and a browser-reachable Keystone WebSSO endpoint. See:
https://docs.openstack.org/openstack-ansible-os_keystone/latest/configure-federation-sp.html

When SSO is enabled, ensure:

* Skyline's ``keystone_url`` is reachable from end-user browsers (the default
  internal Keystone URL may not be).
* The Skyline callback URL is trusted by Keystone WebSSO::

    {skyline_public}/api/openstack/skyline/api/v1/websso

``sso_region`` is only the initial region after SSO login (shared Keystone
deployments do not switch identity services). Skyline apiserver also provides
``POST /api/v1/switch_region/{region}`` for later region switching.

Always verify that the integration is successful and that the service behaves
correctly before using it in a production environment.

Example playbook
~~~~~~~~~~~~~~~~

.. literalinclude:: ../../examples/playbook.yml
   :language: yaml
