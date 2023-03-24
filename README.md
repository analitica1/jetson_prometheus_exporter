INSTALACIÓN:

Requisitos: Tener pip3 instalado (`sudo apt install python3-pip`)

Estando en la carpeta `jetson_prometheus_exporter`:

1. sudo -H pip3 install -r requirements.txt

2. python3 setup.py install 

3. sudo python3 -m jetson_prometheus_exporter

See `python3 -m jetson_prometheus_exporter --help` for command line arguments.

///////////////////////////////////////////////////////////////////////////////////////////

			`Jetson Prometheus Exporter como servicio`

Para configurar el jetson_prometheus_exporter como servicio de Ubuntu:

1. Crear un archivo .sh, por ejemplo `jetson_exp.sh`, y en este poner el siguiente contenido:

	#!/bin/bash
	cd /home/analitica/jetson_prometheus_exporter
	sudo python3 -m jetson_prometheus_exporter

2. Hacemos ejecutable el script con `sudo chmod u+x jetson_exp.sh`

3. Movemos el script a /usr/local/bin/ con `sudo mv /usr/local/bin/ jetson_exp.sh`

4. Creamos el archivo que manejará el servicio encargado de ejecutar el script que creamos.
Para esto:

	1. Corremos la linea `sudo nano /etc/systemd/system/jetson_exp.service`
	2. Copiamos el siguiente contenido dentro del archivo:

		[Unit]
		Description=Prometheus exporter for NVIDIA Jetson Nano Stats

		[Service]
		ExecStart=/usr/bin/jetson_exp.sh
		TimeoutSec=2s

		[Install]
		WantedBy=multi-user.target
	3. Damos Ctrl+O y Enter para guardar el archivo, y Ctrl+X para salir del editor nano

5. Ahora configuramos el demonio encargado de manejar los servicios en Ubuntu, para esto usamos `systemctl`:

	1. sudo systemctl daemon-reload
	2. sudo systemctl status jetson_exp.service
	
	Deberia aparecer algo como esto, que nos dice que el servicio está cargado correctamente, pero
	está inhabilitado:

		Loaded: loaded (/etc/systemd/system/firstscript.service; disabled; vendor preset:
		enabled)
		Active: inactive (dead)

	3. sudo systemctl enable jetson_exp.service
	4. sudo systemctl start jetson_exp.service
	5. Volvemos a correr la linea del punto 2. para verificar el estado del servicio, 
	deberia aparecer que esta activo y corriendo.

6. Si todo salió bien, ya tenemos nuestro jetson_prometheus_exporter funcionando correctamente, y podemos mirar desde el navegador las estadisticas que se exportan entrando al `localhost:8000`, ya que este exporter utiliza el puerto 8000 para entregar todas las métricas asociadas al equipo Jetson. Este conjunto de metricas se importará al Prometheus del host que se quiera utilizar para monitorear el equipo Jetson, y con Grafana desplegaremos dichas métricas.



