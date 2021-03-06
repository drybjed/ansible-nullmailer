Getting started
===============

.. include:: includes/all.rst

.. contents::
   :local:


User and password configuration is insecure
-------------------------------------------

Before using user authentication in your ``nullmailer`` installation, be aware
that the ``nullmailer`` package in Debian uses the ``debconf`` service which
`can leak the user and password <https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=831813>`_
to unprivileged users on your system. If you need to use authenticated user
accounts to forward mail on hosts where other users can login, you should
either set up an intermediate mail relay on another host you control and use
that to forward the messages, or use different MTA, for example Postfix.


Correct DNS configuration is recommended
----------------------------------------

The ``debops.nullmailer`` role uses the ``ansible_fqdn`` and ``ansible_domain``
variables to create correct values for the ``nullmailer`` service. It's
recommended that the hosts which use ``nullmailer`` have the proper DNS
configuration, which means that they should be resolvable in the DNS by their
Fully Qualified Domain Name (hostname + domain name). The FQDN doesn't need to
be accessible from the Internet when the hosts are on a private network, but
it's recommended to select a subdomain of your main DNS domain and configure
the DNS servers to advertise it on your private subnets.


No local mail by default
------------------------

The ``nullmailer`` service does not provide support for local mail - all mail
is forwarded to the configured SMTP servers for further processing. If you need
more advanced SMTP configuration, you should check out the debops.postfix_
role which can configure the Postfix MTA. This also means that in a new
environment, you should perpare at least 1 host as the central mail hub for
your network, or use an already existing SMTP server for relaying mail
messages.

The ``debops.nullmailer`` role is designed to allow automatic switch to
a different SMTP server - if it detects a ``postfix`` package installed on
a host, it will automatically disable configuration of the ``nullmailer``
service to not interfere with existing Postfix configuration.


Default SMTP relay
------------------

The default upstream SMTP relay is configured in the :envvar:`nullmailer__relayhost`
default variable. If not configured otherwise, all mail will be forwarded to
``smtp.{{ ansible_domain }}``. However, this might not be the correct
destination in your environment. The ``nullmailer`` SMTP server does not
resolve the MX records for a domain (as far as I can tell), so you need to
specify the address to your SMTP server manually.

To set the desired value for all hosts in your environment, set in the
inventory:

.. code-block:: yaml

   # ansible/inventory/group_vars/all/nullmailer.yml

   nullmailer__relayhost: '<FQDN address of mail server>'


Example inventory
-----------------

The ``debops.nullmailer`` role is included by default in the ``common`` DebOps
playbook and you don't need to add a host to a custom inventory group to
activate it.


Example playbook
----------------

If you are using this role without DebOps, here's an example Ansible playbook
that uses the ``debops.nullmailer`` role:

.. literalinclude:: playbooks/nullmailer.yml
   :language: yaml
