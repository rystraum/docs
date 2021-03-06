# Apache SAML


## Configuring Apache Shibboleth SP in CentOS

### System preparation: 

1. Add Shibboleth repo for CentOS6

* Contents of "shib.repo":

    	[security_shibboleth]
    	name=Shibboleth (CentOS_CentOS-6)
    	type=rpm-md
    	baseurl=http://download.opensuse.org/repositories/security:/shibboleth/CentOS_CentOS-6/
    	gpgcheck=1
    	gpgkey=http://download.opensuse.org/repositories/security:/shibboleth/CentOS_CentOS-6/repodata/repomd.xml.key
    	enabled=1

* http://download.opensuse.org/repositories/security:/shibboleth/CentOS_CentOS-6/security:shibboleth.repo

### Shibboleth SP installation

* yum install shibboleth
* service shibd start
* chkconfig shibd on

### Install and Configure httpd


### Installation: 

* yum install httpd
* service httpd start
* Stop firewall

### Configuration: 

* Change ServerName directive to the server name of SP
* UseCanonicalName = On
* Restart httpd

### Httpd testing:

* Create a "index.html" inside /var/www/html
* Restart httpd
* Check from your browser

### SP key cert

* Create private key and certificate and place those in /etc/shibboleth
* Change permission

### Shibboleth SP configuration: 

This section is mirroring how to configure "shibboleth2.xml" file. 

* Provide "entityID" of SP in: 
    * <ApplicationDefaults entityID="http://sp.example.org/Shibboleth" section
* Provide the "entityID of IDP" in: 
    * <SSO entityID="https://idp.gluu.org/idp/shibboleth" section 
* Point the metadata provider, in most cases it's Gluu IDP metadata link: 
    * <MetadataProvider type="XML" uri="https://idp.gluu.org/idp/shibboleth" section
* Provide the key and cert of SP in: 
    * <CredentialResolver type="File" key="spkey.key" certificate="spcrt.crt" section

## Configuring Apache Shibboleth SP in Ubuntu

### System preparation:

1.apt-get install curl

2.Grab Shibboleth repository from SWITCH:

* curl -k -O http://pkg.switch.ch/switchaai/SWITCHaai-swdistrib.asc

* gpg --with-fingerprint  SWITCHaai-swdistrib.asc

* apt-key add SWITCHaai-swdistrib.asc

* echo 'deb http://pkg.switch.ch/switchaai/ubuntu precise main' | sudo tee /etc/apt/sources.list.d/SWITCHaai-swdistrib.list > /dev/null

* apt-get update

### Shibboleth SP installation:

* apt-get install shibboleth
* Quick test:
` shibd -t  [ Important is the last line: overall configuration is loadable, check console for non-fatal problems ]`

### Apache testing:

`apache2ctl configtest`

### Test Shibboleth:

* https://hostname_of_sp/Shibboleth.sso/Session

`It will say: "A valid session was not found."`

### Shibboleth Manual configuration ( one Physical SP ):

* Create a directory named "secure" under /var/www/
* Change permission for directory "secure" to www-data:www-data
* httpd.conf:
    * ServerName `<hostname_of_server>`
    * Set Location:

                <Location /secure>
                    AuthType shibboleth
                    ShibRequestSetting requireSession 1
                    ShibUseHeaders on
                    Require valid-user
                </Location>


* shibboleth2.xml configuration:
    * EntityID of SP: `ApplicationDefaults entityID="http://hostname/secure"`
    * Provide EntityID of IDP: `SSO entityID="https://idp.gluu.org/idp/shibboleth"`
    * Metadata Provider, IDP: `MetadataProvider type="XML" uri="https://idp.gluu.org/idp/shibboleth"`


* Restart shibd and apache2
* Create Trust Relationship for this SP in your desired IDP. 


## Configuring Apache Shibboleth SP in Windows

### Shibboleth SP Installation

* Download the MSI of Shibboleth-SP from: http://www.shibboleth.net/downloads/service-provider/latest/
* Start the installation

* Destination folder (by default it is: C:\opt\shibboleth-sp\). You can select any directory, but it is best to follow the tree \opt\shibboleth-sp\
* Select Shibboleth Daemon port: default is 1600, you can keep it for local testing
* Now, there are two options here. You have to follow either one (but not both) according to your target.
    * Option 1: If you are installing Shibboleth for Apache Web server
    * Option 2: If you are installing Shibboleth for Microsoft IIS Web server
    * For Apache Web Server, UNCHECK “Install ISAPI filter and configure IIS”.
    * UAC of Windows 7 may block this program, so allow it

### Apache configuration

* Download the Apache HTTP server MSI Installer with OpenSSL: http://httpd.apache.org/download.cgi#apache22
* Select Destination. You can keep the default destination for your local testing. But, make sure that there is no other “Apache Software Foundation” directory in your current “C:\Program Files\” location
* Provide Server Information. For local testing, you can use “localdomain/localhost”
* Test whether Apache is installed or not. Open your web browser and use “localhost”. If you see "It Works!" page, you are done.


### Shibboleth and Apache further configuration

* Change the permission of the Apache Installation Directory, provide “write”  access 
* httpd.conf configuration
    * Change: “ServerName localhost:80” ( for your local testing )
    * Copy apache22.conf from the Shibboleth directory to ~/apache/conf/extra/
* Shibboleth2.xml configuration
    * Change: Host name=“localhost” ( for local testging )
    * Change: entityID=“https://localhost/shibboleth” ( for local testing )
    * Change: ApplicationOverride id=“admin” entityID=“https://localhost/shibboleth/”
* Reboot your Windows box

### Test SP installation with Windows and Apache

* Open Web browser and provide the address: localhost/Shibboleth.sso/Status
* If you can see some XML page, you are done with your SP installation in Windows through Apache2

### Create Trust Relationship from Gluu IDP

How to create trust relationship with
[Generate](http://www.gluu.org/docs/admin-guide/saml/outbound-saml/#saml-trust-relationship)
method. 


