=========================
napalm-interfaces-formula
=========================

Salt formula to manage the interfaces configuration on network devices, managed via
`NAPALM <https://napalm-automation.net>`_,
either running under a `proxy minion <https://docs.saltstack.com/en/develop/ref/proxy/all/salt.proxy.napalm.html>`_,
or installing the ``salt-minion`` directly on the network device (if the operating system permits).

Check the `Salt Formulas instructions <https://docs.saltstack.com/en/latest/topics/development/conventions/formulas.html>`_ to understand how to install and use formulas.

Available states
================

.. contents::
    :local:

``netconfig``
-------------

Generate the configuration using Jinja templates and load the rendered configuration on the network device. The
templates are pre-written for several operating systems:

- Junos
- Cisco IOS-XR
- Arista EOS
- Cisco IOS
- Cisco NX-OS

If you have a different operating system not covered yet, please submit a PR to add it.

Pillar
======

The pillar has the same structure in both cases, following the hierarchy of the
`openconfig-interfaces YANG model <http://ops.openconfig.net/branches/master/openconfig-interfaces.html>`_, e.g.:

.. code-block:: yaml

    openconfig-interfaces:
      interfaces:
        interface:
          xe-0/0/0:
            config:
              mtu: 9000
              description: Interface1
            subinterfaces:
              subinterface:
                0:
                  config:
                    description: Subinterface1
                  ipv4:
                    addresses:
                      address:
                        1.2.3.4:
                          config:
                            ip: 1.2.3.4
                            prefix_length: 24
                1:
                  config:
                    description: Subinterface2
                  ipv4:
                    addresses:
                      address:
                        1.2.3.4:
                          config:
                            ip: 1.2.3.4
                            prefix_length: 24
                  ipv6:
                    addresses:
                      address:
                        '2001:db8::1':
                          config:
                            ip: '2001:db8::1'
                            prefix_length: 64
          xe-1/1/1:
            config:
              description: Interface2
            subinterfaces:
              subinterface:
                0:
                  config:
                    description: Reserved
                  ipv4:
                    addresses:
                      address:
                        5.6.7.8:
                          config:
                            ip: 5.6.7.8
                            prefix_length: 24

Usage
=====

After configuring the pillar data (and refresh it to the minions, i.e. ``$ sudo salt '*' saltutil.refresh_pillar``),
you can run this formula:

.. code-block:: bash

    $ sudo salt '*' state.sls interfaces.netconfig

Output Example:

.. code-block:: bash

    $ sudo salt vmx1 state.sls interfaces.netconfig


``test_netconfig``
------------------

To avoid testing the state directly on the network device, you can use this
state to save the contents in a temporary file, and display the rendered content
on the command line:

.. code-block:: bash

    $ sudo salt '*' state.sls interfaces.test_netconfig

Output example:

.. code-block:: bash

    $ sudo salt vmx state.sls interfaces.test_netconfig

``netyang``
-----------
