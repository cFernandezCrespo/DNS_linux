# DNS_linux
Comenzamos la práctica instalando un ubuntu en una maquina virtual 

En cuanto estemos con la maquina preparada para continuar, hacemos un *sudo apt update && apt upgrade* y de seguido la siguiente linea de codigo
~~~
sudo apt install bind9
~~~

Ahora tendriamos que ir a las carpetas de bind (en /etc/bind) y hacer el documento named.conf
~~~
include "/etc/bind/named.conf.options";
include "/etc/bind/named.conf.local";
~~~

El named.conf.default-zones

~~~
// prime the server with knowledge of the root servers
zone "." {
	type hint;
	file "/usr/share/dns/root.hints";
};

// be authoritative for the localhost forward and reverse zones, and for
// broadcast zones as per RFC 1912

zone "localhost" {
	type master;
	file "/etc/bind/db.local";
};

zone "127.in-addr.arpa" {
	type master;
	file "/etc/bind/db.127";
};

zone "0.in-addr.arpa" {
	type master;
	file "/etc/bind/db.0";
};

zone "255.in-addr.arpa" {
	type master;
	file "/etc/bind/db.255";
};

~~~

ahora named.conf.local

~~~
zone "asircastelao.int" {
	type master;
	file "/var/lib/bind/db.asircastelao.int";
	allow-query {
		any;
		};
	};
~~~

ahora named.conf.options

~~~

options {
	directory "/var/cache/bind";

	forwarders {
		208.67.222.222;
		208.67.220.220;
	 	8.8.8.8;
		1.1.1.1;
	 };
	 forward only;

	listen-on { any; };
	listen-on-v6 { any; };

	allow-query {
		any;
	};
};

~~~

El habitual db.asircastelao.int donde se guarda toda la configuracion de nuestro dns (este documento tiene que ir en la carpeta /var/lib/bind)

~~~

$TTL 38400	; 10 hours 40 minutes
@		IN SOA	ns.asircastelao.int. some.email.address. (
				10003   ; serial
				10800      ; refresh (3 hours)
				3600       ; retry (1 hour)
				604800     ; expire (1 week)
				38400      ; minimum (10 hours 40 minutes)
				)
@		IN NS	ns.asircastelao.int.
ns		IN A		172.0.8.152
pag1	IN A		172.28.5.4
pag2	IN A 		172.28.5.7
www		IN CNAME	test
global	IN TXT		mensaje
gabriel	IN A 		10.0.9.10

~~~

Después de configurar todos los documentos tendrás que reinciar la máquina y ya deberia funcionar, utilizando el siguiente comando debería funcionar todo perfecto.
~~~
dig @10.0.2.15 pag1.asircastelao.int
~~~