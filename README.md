# Twitter Dashboard, Apache NiFi, Solr, Banana

Twitter dashboard using Apache NiFi, Solr and Banana

![Apache NiFi/Twitter Dashboard](https://github.com/UNGlobalPlatform/twitter-dash/blob/master/docs/twitter-dashboard.png?raw=true)

## Getting Started

Creating an interactive dashboard for your data

### Prerequisites

For this part we are going to need Solr, Banana and NiFi and we are going to create an interactive dashboard of the tweets.

## Update System

```
sudo apt-get update && apt-get upgrade -y
```

## Solr

First install Java.

### Install Java

```
sudo apt-get update
sudo tar apt-get install -y default-jre || sudo yum install -y java-headless
java -version
```
Veryify that Java is installed.

### Install Solr Standard
```
sudo wget http://apache.mirror.anlx.net/lucene/solr/7.0.0/solr-7.0.0.tgz
sudo tar xzf solr-7.0.0.tgz
sudo ./solr-7.0.0/bin/install_solr_service.sh solr-7.0.0.tgz
```
Create the Solr Collection for Tweets
```
sudo -i
cd /var/solr/data
mkdir tweets
cp -r _default/ /var/solr/data
mv _default/ tweets
chown -r solr:solr *
exit

sudo -u solr ./solr-7.0.0/bin/solr create_core -c tweets -d _default -s 1 -rf 1
```
Check the core has been created

http://hostname:8983/solr/#/~cores/tweets

Edit solrconfig.xml by adding <str>EEE MMM d HH:mm:ss Z yyyy</str> under ParseDateFieldUpdateProcessorFactory so it looks like below. This is done to allow Solr to recognise the timestamp format of tweets.
```
sudo nano /opt/lucidworks-hdpsearch/solr/server/solr/configsets/data_driven_schema_configs/conf/solrconfig.xml
  <processor>
    <arr name="format">
      <str>EEE MMM d HH:mm:ss Z yyyy</str>
```

![Apache NiFi/Twitter Dashboard](https://github.com/UNGlobalPlatform/twitter-dash/blob/master/docs/solr-date-config.png?raw=true)

## Banana

Banana is a tool to create dashboards to visualize data you have stored in Solr. Commonly used with Logstash for log data. Its a fork of kibana.

Run Solr at least once to create the webapp directory

Download Banana and install it.
```
 cd /opt/solr/server/solr-webapp/webapp
 sudo git clone https://github.com/lucidworks/banana
```
Replace the default dashboard with the Twitter dashboard
```
cd /opt/solr/server/solr-webapp/webapp/banana/src/app/dashboards
sudo mv default.json default.json.orig
wget https://raw.githubusercontent.com/abajwa-hw/ambari-nifi-service/master/demofiles/default.json
```
Browse to http://hostname:8983/solr/banana/src/index.html

## Apache NiFi

Now create a data flow in NiFi to send the twitter data to Solr.
![Apache NiFi/Twitter Dashboard](https://github.com/UNGlobalPlatform/twitter-dash/blob/master/docs/nififlow.png?raw=true)

Configure the PutSolrContentStream processor as follows, this will push the tweets to Solr.
![Apache NiFi/Twitter Dashboard](https://github.com/UNGlobalPlatform/twitter-dash/blob/master/docs/solrconfig2.jpeg?raw=true)
![Apache NiFi/Twitter Dashboard](https://github.com/UNGlobalPlatform/twitter-dash/blob/master/docs/solrconfig1.jpeg?raw=true)

Enable the flow and then check Solr.
Use the following URL and search the Solr index.

http://hostname:8983/solr

## Dashboard
Use the following URL to access the dashboard.
http://hostname:8983/solr/banana/src/index.html#/dashboard

You will have a timeline of tweets in the dashboard. The dashboard is highly configurable, so have a play.

## Contributing

Please read [CONTRIBUTING.md](https://gist.github.com/PurpleBooth/b24679402957c63ec426) for details on our code of conduct, and the process for submitting pull requests to us.

## Authors

* **Mark Craddock** - *Initial work*
* **Neville de Mendonca** - *Initial work*

See also the list of [contributors](https://github.com/your/project/contributors) who participated in this project.

## License

TBD

## Acknowledgments

https://community.hortonworks.com/articles/1282/sample-hdfnifi-flow-to-push-tweets-into-solrbanana.html
https://blogs.apache.org/nifi/entry/indexing_tweets_with_nifi_and
