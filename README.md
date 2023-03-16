# Install OCI8 on Ubuntu 20.04 with PHP 8.1

## Step 1

### Download **Oracle Instant Client** and **SDK** from Oracle:

> https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html
> Download files:

> Basic Package (ZIP): instantclient-basic-linux.x64-21.9.0.0.0.zip
> SDK Package (ZIP): instantclient-sdk-linux.x64-21.9.0.0.0.zip
> Download links

> wget https://download.oracle.com/otn_software/linux/instantclient/219000/instantclient-basic-linux.x64-21.9.0.0.0dbru.zip
> wget https://download.oracle.com/otn_software/linux/instantclient/219000/instantclient-sdk-linux.x64-21.9.0.0.0dbru.zip
	
	
## Step 2

Create new folders for Oracle Instant Client on server

```
    sudo mkdir /usr/lib/oracle
    sudo mkdir /usr/lib/oracle/21.9
    sudo mkdir /usr/lib/oracle/21.9/client64
```

## Step 3

Extract files

```
sudo cp instantclient-basic-linux.x64-21.9.0.0.0dbru.zip /usr/lib/oracle/21.9/client64
sudo cp instantclient-sdk-linux.x64-21.9.0.0.0dbru.zip /usr/lib/oracle/21.9/client64

    cd /usr/lib/oracle/21.1/client64

	sudo unzip instantclient-basic-linux.x64-21.9.0.0.0dbru.zip
	sudo unzip instantclient-sdk-linux.x64-21.9.0.0.0dbru.zip
    	sudo mv instantclient_21_1 lib
```

## Step 4

Create symbolic link to the new Instant Client files:

```
    cd /usr/lib/oracle/21.1/client64/lib/    
    sudo ln -s libclntsh.so.21.9 libclntsh.so (It may already exist, continue)
    sudo ln -s libocci.so.21.9 libocci.so (It may already exist, continue)
```

## Step 5

Edit/Create this file with the path to the lib (for LDCONFIG):

```
	sudo echo /usr/lib/oracle/21.9/client64/lib > /etc/ld.so.conf.d/oracle.conf
```

## Step 6

Update Dynamic Linker

```
	ldconfig
```

## Step 7

Install php-dev php-pear build-essential and libaio1

```	
	sudo apt-get install build-essential libaio1
	apt install php8.1-dev
	apt install php8.1-pear
```

## Step 8

Update PECL to install OCI8 from it

```
	sudo pecl channel-update pecl.php.net
```

Install OCI8 from PCEL (desired version, default seems not to find it for me, i used php7.)

```
    sudo pecl install oci8 (php 8)
    sudo pecl install oci8-2.2.0  (php 7.)
```

During install it will require the path to Instant Client, write this:

```
	instantclient,/usr/lib/oracle/21.9/client64/lib
```

At the end something like this should appear:

```
Build process completed successfully
Installing '/usr/lib/php/20190902/oci8.so'
install ok: channel://pecl.php.net/oci8-2.2.0
configuration option "php_ini" is not set to php.ini location
You should add "extension=oci8.so" to php.ini
```

## Step 9

Load OCI8 into PHP

```
	sudo echo "extension=oci8.so" >> /etc/php/8.1/cli/php.ini
```

Restart Apache

```
	sudo systemctl restart apache2
```

## Step 10

Add to **mods-available**

```
	cd /etc/php/8.1/mods-available/
	sudo nano oci.ini
```

Add this to the file:

```
extension = oci8.so
```

## Step 11

Create sym link to the created ini file

```
	cd /etc/php/8.1/apache2/conf.d
	sudo ln -s /etc/php/8.1/mods-available/oci.ini 20-oci.ini
```

Restart Apache

```
	sudo systemctl restart apache2
```

## Step 12 (Testing)

Check if OCI is loaded in PHP

```
	php -i | grep oci
```

Should output something like this:

```
oci8
oci8.connection_class => no value => no value
oci8.default_prefetch => 100 => 100
oci8.events => Off => Off
oci8.max_persistent => -1 => -1
oci8.old_oci_close_semantics => Off => Off
oci8.persistent_timeout => -1 => -1
oci8.ping_interval => 60 => 60
oci8.privileged_connect => Off => Off
oci8.statement_cache_size => 20 => 20
```


