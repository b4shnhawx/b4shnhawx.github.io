---
layout: post
title: Suricata IDS con OpenIA
---

Las inteligencias artificiales (IA) están transformando el mundo de manera significativa, y su aplicación en el ámbito de la ciberseguridad está siendo cada vez más relevante. En el contexto actual, la ciberseguridad se ha vuelto una preocupación importante para empresas y organizaciones, ya que las amenazas cibernéticas se han vuelto cada vez más sofisticadas y frecuentes. Los sistemas de detección de intrusiones (IDS, por sus siglas en inglés) son herramientas esenciales para detectar y responder a posibles amenazas a la seguridad de la red.

Las IA se están utilizando cada vez más en los IDS, lo que permite una detección más rápida y precisa de posibles amenazas. Las IA pueden aprender patrones de comportamiento de los usuarios y detectar anomalías en tiempo real, lo que permite una respuesta más rápida y eficaz ante posibles ataques. Además, las IA también pueden identificar amenazas emergentes y desconocidas que podrían haber pasado desapercibidas para los IDS tradicionales.

En resumen, la aplicación de las IA en el ámbito de la ciberseguridad puede proporcionar una capa adicional de seguridad y protección, lo que resulta crucial en un mundo cada vez más conectado y dependiente de la tecnología.

En este proyecto he querido configurar un Suricata para detectar anomalias en la red, lo cual generaria una alerta y se notificaria por medio de correo electronico junto con una recomendacion de mitigación generada por una IA (en este caso chatGPT).


![_config.yml]({{ site.baseurl }}/images/lan.png)



<pre>
<code><xmp>x="hello world"
print (x)

aaaa






bbbbbbb


</xmp></code>
</pre>

```
#!/bin/bash

sudo suricata-update update-sources
latest_version=`sudo suricata-update check-versions | grep "is outdated" | egrep -o "[0-9]{1,5}\.[0-9]{1,5}\.[0-9]{1,5}" | tail -n1`
actual_version=`sudo suricata-update check-versions | grep "Found Suricata version" | egrep -o "[0-9]{1,5}\.[0-9]{1,5}\.[0-9]{1,5}"`

cd /home/pi

now=$(date)
echo "$now    Inicializando script." >> /home/pi/Scripts/log/update_suricata.log

if [[ -z $latest_version ]];
then
	now=$(date)
	echo "$now       No requiere actualizacion." >> /home/pi/Scripts/log/update_suricata.log

	exit 0
else
	now=$(date)
	echo "$now       Requiere actualizacion." >> /home/pi/Scripts/log/update_suricata.log
	echo "$now       Descargando actualizacion." >> /home/pi/Scripts/log/update_suricata.log
	wget https://www.openinfosecfoundation.org/download/suricata-$latest_version.tar.gz

	now=$(date)
	echo "$now       Descomprimiendo archivos." >> /home/pi/Scripts/log/update_suricata.log
	tar -xvf suricata-$latest_version.tar.gz

	now=$(date)
	echo "$now       Configurando archivos." >> /home/pi/Scripts/log/update_suricata.log
	cd /home/pi/suricata-$latest_version/
	./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var --enable-nfqueue --enable-lua

	now=$(date)
	echo "$now       Compilando todos los archivos necesarios." >> /home/pi/Scripts/log/update_suricata.log
	make

	now=$(date)
	echo "$now       Instalando." >> /home/pi/Scripts/log/update_suricata.log
	sudo make install

	now=$(date)
	echo "$now       Comprobando instalacion." >> /home/pi/Scripts/log/update_suricata.log
	sudo suricata-update update-sources
	actual_version=`sudo suricata-update check-versions | grep "Found Suricata version" | egrep -o "[0-9]{1,5}\.[0-9]{1,5}\.[0-9]{1,5}"`

	cd /home/pi

	if [[ $actual_version == $latest_version ]];
	then
		now=$(date)
		echo "$now       Instalado correctamente." >> /home/pi/Scripts/log/update_suricata.log
	else
		now=$(date)
                echo "$now       La instalacion ha fallado." >> /home/pi/Scripts/log/update_suricata.log
	fi

	now=$(date)
	echo "$now       Eliminando archivos." >> /home/pi/Scripts/log/update_suricata.log

	rm /home/pi/Scripts/suricata*
fi

now=$(date)
echo "$now    Script finalizado." >> /home/pi/Scripts/log/update_suricata.log

exit 0

```
