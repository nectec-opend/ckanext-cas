.. You should enable this project on travis-ci.org and coveralls.io to make
   these badges work. The necessary Travis and Coverage config files have been
   generated for you.

.. .. image:: https://travis-ci.org/polarp/ckanext-cas.svg?branch=master
..     :target: https://travis-ci.org/polarp/ckanext-cas

.. .. image:: https://coveralls.io/repos/polarp/ckanext-cas/badge.svg
..   :target: https://coveralls.io/r/polarp/ckanext-cas


=============
ckanext-cas
=============

.. Put a description of your extension here:
   What does it do? What features does it have?
   Consider including some screenshots or embedding a video!
เป็น CKAN plugin สำหรับทำ Single Sign On (SSO) สำหรับหน่วยงานหรือองค์กรทำใช้ระบบ Central Authentication Service (CAS) ในการยืนยันตัวตน
Plugin นี้ได้ทำการ Fork มาจาก `keitaroinc <https://github.com/keitaroinc/ckanext-cas>` แล้วนำมาปรับปรุงแก้ไขโค้ดให้สามารถใช้งานร่วมกับ CKNA Open-D ได้

------------
Special Thanks
------------
Special thanks to `Neogeo Technologies <http://www.neogeo-online.net>`_ for their contribution in making of this plugin.

------------
Requirements
------------

คู่มือการติดตั้งสำหรับ CKAN 2.9


------------
Installation
------------

.. Add any additional install steps to the list below.
   For example installing any non-Python dependencies or adding any required
   config settings.

To install ckanext-cas:

1. Activate your CKAN virtual environment, for example::

     . /usr/lib/ckan/default/bin/activate

2. Install the ckanext-cas Python package into your virtual environment::

     pip install -e 'git+https://github.com/nectec-opend/ckanext-cas.git#egg=ckanext-cas'
     pip install -r ckanext-cas/requirements.txt

3. Add ``cas`` to the ``ckan.plugins`` setting in your CKAN
   config file (by default the config file is located at
   ``/etc/ckan/default/ckan.ini``).

4. Restart CKAN. For example if you've deployed CKAN with Supervisor on Ubuntu::

     sudo supervisorctl reload


---------------
Config Settings
---------------

In order to configure CKAN to use CAS you must setup the following configuration options::

    # User attributes mapping (required)
    # ``email`` and ``user`` mappings are required
    ckanext.cas.user_mapping = email~email user~username fullname~full_name sysadmin~is_superuser

    # If you need to combine attributes (note fullname)
    ckanext.cas.user_mapping = email~email user~username fullname~first_name+last_name sysadmin~is_superuser

    # CAS login URL (required)
    ckanext.cas.login_url = http://mamacas.django.com/login

    # CAS logout URL (required)
    ckanext.cas.logout_url = http://mamacas.django.com/logout

    # CKAN application URL (required)
    # The URL through which users are interacting with the application
    ckanext.cas.application_url = https://ckan-demo.com

    # CAS single sign out (optional)
    ckanext.cas.single_sign_out = true

    # CAS service validation URL (conditional)
    # Either ``ckanext.cas.service_validation_url`` or ``ckanext.cas.saml_validation_url`` must be configured.
    ckanext.cas.service_validation_url = http://mamacas.django.com/serviceValidate

    # CAS SAML validation URL (conditional)
    # Either ``ckanext.cas.service_validation_url`` or ``ckanext.cas.saml_validation_url`` must be configured.
    ckanext.cas.saml_validation_url = http://mamacas.django.com/samlValidate


Make sure you have configured ``django-mama-cas`` properly i.e. ::

    MAMA_CAS_SERVICES = [
        {
            'SERVICE': '^https://ckan-demo.com',
            'CALLBACKS': [
                'mama_cas.callbacks.user_name_attributes',
                'mama_cas.callbacks.user_model_attributes'
            ],
            'LOGOUT_ALLOW': True,
            'LOGOUT_URL': 'https://ckan-demo.com/cas/logout'
        },
    ]

**NOTE:** If you use SAML as validation method for CAS have in mind that CKAN and django must be accessed over SSL.

The current version of ``django-mama-cas`` has a bug when you use SAML as validation method since it is unable
to serialize user attribute types that are different from string.

`Pull request <https://github.com/jbittel/django-mama-cas/pull/44>`_ has been submitted
but until it has been approved and merged you can use the following `fork <https://github.com/keitaroinc/django-mama-cas/tree/saml-response-errors>`_ of ``django-mama-cas``.
