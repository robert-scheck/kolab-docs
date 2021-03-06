.. _installation-guide-debian-8-enterprise-14:

========================================================
Installation of Kolab Enterprise 14 on Debian 8 (jessie)
========================================================

1.  Install the **apt-transport-https** package:

    .. parsed-literal::

        # :command:`aptitude -y install apt-transport-https`

2.  Add the following two lines to ``/etc/apt/sources.list.d/kolab.list``:

    .. parsed-literal::

        deb https://mirror.kolabenterprise.com/debian/kolab-14/ jessie release updates
        deb-src https://mirror.kolabenterprise.com/debian/kolab-14/ jessie release updates

3.  To ensure the Kolab packages have priority over the Debian packages, such as
    must be the case for PHP as well as Cyrus IMAP, please make sure the APT
    preferences pin the mirror.kolabsys.com origin as a preferred source.

    Put the following in ``/etc/apt/preferences.d/kolab``:

    .. parsed-literal::

        Package: *
        Pin: origin mirror.kolabenterprise.com
        Pin-Priority: 501

4.  Install the client certificate and certificate authority files:

    A.  Remove the passphrase from the SSL certificate key:

        .. parsed-literal::

            # :command:`openssl rsa -in /path/to/private.key \\
                -out /path/to/private.key.nopass`

    B.  Concatenate the certificate file and the new key file without
        passphrase:

        .. parsed-literal::

            # :command:`cat /path/to/public.crt /path/to/private.key.nopass \\
                > /path/to/mirror.kolabsys.com.client.pem`

    C.  Place the file :file:`mirror.kolabsys.client.pem` in
        :file:`/etc/apt/certs/`.

    D. Download the kolabenterprice mirror certificate:

        .. parsed-literal::

            # :command:`cd /etc/apt/certs/`
            # :command:`wget https://ssl.kolabsys.com/mirror.kolabenterprise.com.ca.cert`

    E. Correct the permissions on the private key:

        .. parsed-literal::

            # :command:`chown root:root /etc/apt/certs/mirror.kolabsys.com.client.pem`
            # :command:`chmod 640 /etc/apt/certs/mirror.kolabsys.com.client.pem`

5.  Configure **APT** to use the certificates installed in step 4 by
    creating a file ``/etc/apt/apt.conf.d/81kolab`` with the following
    contents:

    .. parsed-literal::

        Acquire {
            https {
                mirror.kolabenterprise.com {
                    Verify-Peer "true";
                    Verify-Host "true";
                    CaInfo "/etc/apt/certs/mirror.kolabenterprise.com.ca.cert";

                    SslCert "/etc/apt/certs/mirror.kolabsys.com.client.pem";
                    SslKey "/etc/apt/certs/mirror.kolabsys.com.client.pem";
                };
            };
        };

6.  Update the repository metadata:

    .. parsed-literal::

        # :command:`apt-get update`

7.  Start the installation of the base package as follows:

    .. parsed-literal::

        # :command:`aptitude install kolab`

8.  When asked to confirm you want to install the package and its dependencies, press Enter.

Continue to :ref:`installation-guide-setup-kolab`.
