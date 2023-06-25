# data-engineering
Updated installation instructions for Data Engineering

OS: __Ubuntu 18__

## Nifi
Set HTTPS Port in "conf/nifi.properties" e.g., 8090
```sh
cd nifi-1.22.0/
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
./bin/nifi.sh start
nifi start
```
https://127.0.0.1:8090/nifi/login

Go to the "conf/login-identity-providers.xml" file

Find __Username__ and __Password__
```
    <property name="Username">random-gibberish</property>
    <property name="Password">random-gibberish</property>
```
Change above to below:
```
    <property name="Username"></property>
    <property name="Password"></property>
```
Check the password in "conf/nifi-app.log" for

```
Generated Username []
Generated Password []
```

## Apache Airflow
```sh
python3.8 -m venv airflow
source ~/airflow/bin/activate
pip install 'apache-airflow[postgres,slack,celery]'
airflow db init
airflow scheduler
airflow webserver
airflow db reset
airflow webserver

airflow users  create --role Admin --username admin --email admin --firstname admin --lastname admin --password admin
```

## Elastic Search
_This was most annoying part and only the installation via Debian seems to work._

Source: https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html
```sh
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
sudo apt-get install apt-transport-https
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.8.1-amd64.deb
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.8.1-amd64.deb.sha512
shasum -a 512 -c elasticsearch-8.8.1-amd64.deb.sha512 
sudo dpkg -i elasticsearch-8.8.1-amd64.deb

sudo /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic

sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
journalctl -u elasticsearch.service
```

## Kibana
Source: https://www.elastic.co/guide/en/kibana/current/deb.html
```sh
wget https://artifacts.elastic.co/downloads/kibana/kibana-8.8.1-amd64.deb
sudo dpkg -i kibana-8.8.1-amd64.deb
sudo /bin/systemctl daemon-reload
sudo /usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana
sudo /bin/systemctl daemon-reload
sudo systemctl start kibana.service
journalctl -u kibana.service
```

## PostgreSQL
Source: https://www.postgresql.org/download/linux/debian/
```sh
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get -y install postgresql
systemctl start postgresql
sudo -u postgres psql

journalctl -u postgresql.service
```

## PG Admin 4
Source: https://www.pgadmin.org/download/pgadmin-4-apt/
