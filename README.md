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

Download Solr and install it.

### Verify Java

```
java -version
```
Veryify that Java is installed.

### Install Solr

```
cd /opt
sudo wget http://apache.mirror.anlx.net/lucene/solr/7.0.0/solr-7.0.0.tgz
sudo tar xzf solr-7.0.0.tgz
cd solr-7.0.0/
bin/solr start -e cloud -noprompt
bin/solr create_collection -c tweets -d _default -s 1 -rf 1
```

Check Solr installation

```
sudo service solr stop
sudo service solr start
sudo service solr status
```

Create the Solr Collection for Tweets
```
bin/solr create_collection -c tweets -d data_driven_schema_configs -s 1 -rf 1
```
Edit solrconfig.xml by adding <str>EEE MMM d HH:mm:ss Z yyyy</str> underParseDateFieldUpdateProcessorFactory so it looks like below. This is done to allow Solr to recognize the timestamp format of tweets.

```
vi /opt/lucidworks-hdpsearch/solr/server/solr/configsets/data_driven_schema_configs/conf/solrconfig.xml
  <processor>
    <arr name="format">
      <str>EEE MMM d HH:mm:ss Z yyyy</str>
```

![Apache NiFi/Twitter Dashboard](https://github.com/UNGlobalPlatform/twitter-dash/blob/master/docs/twitter-dashboard.png?raw=true)

Once you start the solr you can open the web url at http://hostname:8983/solr

## Banana

Banana is a tool to create dashboards to visualize data you have stored in Solr. Commonly used with Logstash for log data. Its a fork of kibana.
Download Banana and install it.
Run Solr at least once to create the webapp directory:
```
cd $SOLR_HOME/bin/
./solr start
```
Copy banana folder to $SOLR_HOME/server/solr-webapp/webapp/
```
 cd $SOLR_HOME/server/solr-webapp/webapp
 cp -R $BANANA_HOME/src ./banana
```

```
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
