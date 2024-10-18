<h1>Práctica 4 Network e Compose - Anxo Vázquez</h1>
<h2>Docker Network</h2>
<h3>1. Crea unha rede en docker</h3>
<p>Para crear unha rede en docker executo "docker network create -d tipo nome", sendo tipo o tipo de rede (bridge, ipvlan, macvlan, overlay) e nome un nome indicativo.</p>
<br>
<p>docker network create -d bridge bridgep4</p>
<br>
<h3>2. Crear dous contenedores unidos a esa rede</h3>
<p>Para lanzar un contenedor nesa rede executo "docker run -itd --network=nome imaxe" onde nome sería lanp4 no meu caso.</p>
<p>Lanzo o comando 2 veces para crear dous contenedores.</p>
<br>
<p>docker run -itd --network=bridgep4 ubuntu</p>
<br>
<h3>3. Comprobar que os dous contenedores están na mesma rede.</h3>
<p>Para ver si os contenedores estan na mesma rede co comando "docker inspect nomecontenedor" ou dende visual studio code, no apartado de network debería aparecer o nome da rede e a súa ip.</p>
<br>
<p>docker inspect mystifying_archimedes</p>
<p>docker inspect beautiful_villani</p>
<br>
<p>Resultado:</p>
<p>"Networks": {
                "bridgep4": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "MacAddress": "02:42:ac:13:00:02",
                    "DriverOpts": null,
                    "NetworkID": "da2ff48ac35b676e1d6a85869f0aa83291bbc7c4048415df7a2ccb4a299febd0",
                    "EndpointID": "402ddabea8529ef28ce516a83faf83d159955dc89d55ab428a45a776dab0c01a",
                    "Gateway": "172.19.0.1",
                    "IPAddress": "172.19.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "DNSNames": [
                        "beautiful_villani",
                        "80279d181273"
</p>
<br>
<h3>4. Comprobar que os contenedores poden verse entre eles</h3>
<p>Primeiro conectome ao contenedor cunha terminal bash</p>
<p>Logo executo "apt update", despois instalo o paquete de net-tools e o paquete de iputils con "apt install netutils-ping"<</p>
<p>Despois probo a facer o ping entre os contenedores indicando a ip do outro contenedor</p>
<br>
<p>docker exec -it beautiful_villani /bin/bash</p>
<p>apt update</p>
<p>apt install net-tools</p>
<p>apt install netutils-ping</p>
<p>ping 172.19.0.3</p>


<h3>5. listar os contenedores conectados á rede</h3>
<p>Facendo "docker network inspect nomerede" onde o nome da rede e "bridgep4" mostra os contenedores que  ten nesa rede.</p>
<br>
<p>docker network inspect bridgep4 </p>
<p>Resultado:
"Containers": {
            "80279d181273806d00de21ae01555b8031e89449249f74781d4d45a03b175713": {
                "Name": "beautiful_villani",
                "EndpointID": "402ddabea8529ef28ce516a83faf83d159955dc89d55ab428a45a776dab0c01a",
                "MacAddress": "02:42:ac:13:00:02",
                "IPv4Address": "172.19.0.2/16",
                "IPv6Address": ""
            },
            "d9ff7f260ea79cedc0eb4b9c3b1a928e995dd3f605e41d602dbb536373e0ebd5": {
                "Name": "mystifying_archimedes",
                "EndpointID": "0f2b8b32ed85c54fbe49932f2cc3b08770f4d91f6e10182ac73207b0f5711211",
                "MacAddress": "02:42:ac:13:00:03",
                "IPv4Address": "172.19.0.3/16",
                "IPv6Address": ""
            }
</p>
<br>
<h3>Listar as propiedades da rede</h3>
<p>Usando "docker network ls" mostra todas as redes, despois con "docker network inspect nomerede" mostra todas as propiedades de unha rede.</p>
<br>
<p>docker network ls</p>
<p>docker network inspect bridgep4</p>
<br>
<h3>Crea outra rede</h3>
<p>Para crear outra rede uso "docker network create ****".
<br>
<p>docker network create -d bridge bridgep42</p>
<br>
<h3>Lanza dous contenedores novos conectados a esa nova rede</h3>
<p>Para lanzalos uso docker run -itd --network=rede imaxe, neste caso como son dous contenedores lanzo o comando duas veces.</p>
<br>
<p>docker run -itd --network=bridgep42 ubuntu</p>
<br>
<h3>Comproba as posibles conexións entre os 4 contenedores</h3>
<p>Permite facer ping entre os contenedores dunha rede, pero nos de contenedores de diferentes redes.</p>
<br>
<h2>Docker compose</h2>
<h3>1. Segue os pasos da guía de iniciación de docker-compose, e explica coas túas palabras os pasos que segues e qué fan</h3>
<h4>Guía: https://docs.docker.com/compose/gettingstarted/ </h4>
<p>1 - creo un directorio llamado "composeprueba".</p>
<p>2 - creo un archivo llamado "app.py" en el que copio lo indicado en la guía.</p>
<p>3 - creo otro archivo llamado "requirements.txt" con las palabras "flask y redis".</p>
<p>4 - Creo un archivo de docker con lo especificado en la guía.</p>
<p>5 - Creo el archivo compose.yaml</p>
<p>6 - docker compose up</p>
<p>7 - Accedo a localhost:8000, esto muestra un mensaje con un contador que sube cada vez que se accede a la página</p>
<br>
<h3>2. Crea un arquivo (ou varios arquivos) de configuración que ó ser lanzados cun docker-compose up, resulten nunha rede docker á que estean conectados 3 contenedores, explica os parámetros do .yaml usado</h3>
<p>1 - Modifico el archivo "compose.yaml" y introduzco lo siguiente:</p>
<p>services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    networks:
      - bridgep42

  app:
    build:
      context: ./app
    networks:
      - bridgep42

  mongo:
    image: mongo:latest
    networks:
      - bridgep42

networks:
  bridgep42:
    driver: bridge
</p>
<br>
<h3>Busca 4 parámetros e configuracións diferentes que podes incluir no arquivo compose</h3>
<p>network -> Para indicar unha rede</p>
<p>volume -> Para indicar un volumen</p>
<p>image -> Indicar unha imaxen</p>
<p>ports -> Especificar portos </p>


