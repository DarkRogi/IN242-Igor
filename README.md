# IN242-Igor

// criar a instância na AWS e assossiar a ela um IP elástico - 18.228.200.47

//criar as seguintes regras de acesso 
TCP	8888	0.0.0.0/0
SSH	22	0.0.0.0/0
TCP	27017	0.0.0.0/0
TCP	1883	0.0.0.0/0

// acessar a instância na AWS

ssh -i "MyKey.pem" ubuntu@ec2-18-228-200-47.sa-east-1.compute.amazonaws.com

// clonar o repositorio no git hub

git clone https://github.com/lucheol/in242

// instalar o python - https://python.org.br/instalacao-linux/

sudo apt-get install python3.6
sudo apt-get install python-pip

//instalar, criar e acessar um virtualenv

sudo pip3 install virtualenv 
virtualenv -p python3.6 env
source env/bin/activate

//instalar o docker - https://www.digitalocean.com/community/tutorials/como-instalar-e-usar-o-docker-no-ubuntu-18-04-pt

sudo apt update
sudo apt install docker-ce

//intalar o docker-compose - https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-ubuntu-18-04

sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

//subir os containers

sudo docker-compose up -d

//ajustar o horario - https://linuxize.com/post/how-to-set-or-change-timezone-on-ubuntu-18-04/

sudo ln -s /usr/share/zoneinfo/America/Sao_Paulo /etc/localtime

//instalar o editor vim e realizar os seguintes ajustes: - https://www.ubuntudicas.com.br/2012/08/vim-editor-de-textos/

sudo apt-get install vim -y

*producer ==> (IP, amplitude e frequência de amostragem)
import random
import time
import paho.mqtt.client as mqtt
import json

# conectar ao broker

print('Conectando ao mqtt broker...')
mqtt_client = mqtt.Client()
mqtt_client.connect('localhost'==>18.228.200.47, 1883, 60)

while True:
    temperatura = random.uniform(17==>15,30)
    print(temperatura)
    msg = {
        'temperatura': temperatura
    }
    mqtt_client.publish('in242', json.dumps(msg), qos=0)
    time.sleep(5==>1)

*consumer ==> (IP)
import paho.mqtt.client as mqtt
from pymongo import MongoClient
import datetime
import json

mongo_client = MongoClient('localhost'==>18.228.200.47, 27017)
mongo_db = mongo_client['inatel']
mongo_collection = mongo_db['in242']


def on_message(mqtt_client, obj, msg):
    print('recebendo mensagem...')
    msg_formatada = json.loads(msg.payload)
    msg_formatada['data_coleta'] = datetime.datetime.now()
    mongo_collection.insert_one(msg_formatada)
    print('mensagem inserida')


print('configurando...')
mqtt_client = mqtt.Client()
mqtt_client.connect('localhost'==>18.228.200.47, 1883, 60)
mqtt_client.on_message = on_message
mqtt_client.subscribe("in242/#", 0)
mqtt_client.loop_forever()

//criar 2 sub telas sobrepostas - https://www.digitalocean.com/community/tutorials/how-to-install-and-use-screen-on-an-ubuntu-cloud-server

sudo apt-get install screen
screen -S IN242

//na tela 1 acessar novamente o v.env e ativar o producer

python producer/producer.py
 
//na tela 2 acessar novamente o v.env e ativar o consumer

python consumer/consumer.py
