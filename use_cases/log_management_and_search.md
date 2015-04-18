# Log Management and Search

## Free Alternative to Splunk Using Fluentd

[Splunk](http://www.splunk.com/) is a great tool for searching logs, but its high cost makes it prohibitive for many teams. In this article, we present a free and open source alternative to Splunk by combining three open source projects: Elasticsearch, Kibana, and Fluentd.

<center>
<img src="/images/kibana-screenshot.png" width="90%"/><br />
</center><br/>
<br/>

[Elasticsearch](http://www.elasticsearch.org/) is an open source search engine known for its ease of use. [Kibana](http://kibana.org/) is an open source Web UI that makes Elasticsearch user friendly for marketers, engineers and data scientists alike.

By combining these three tools (Fluentd + Elasticsearch + Kibana) we get a scalable, flexible, easy to use log search engine with a great Web UI that provides an open-source Splunk alternative, all for free.

<center>
<img width="540" src="/images/fluentd-elasticsearch-kibana.png"/>
</center><br /><br />

In this guide, we will go over installation, setup, and basic use of this combined log search solution. This article was tested on Mac OS X Mountain Lion. **If you're not familiar with Fluentd**, please learn more about Fluentd first.

<center>
<div class="btn-look" style="width: 300px;">
<a href="http://docs.fluentd.org/articles/architecture">What is Fluentd?</a>
</div>
</center>
<br/>
<br/>

## Prerequisites

### Java for Elasticsearch

Please confirm that your Java version is 6 or higher.

    :::term
    $ java -version
    java version "1.6.0_45"
    Java(TM) SE Runtime Environment (build 1.6.0_45-b06-451-11M4406)
    Java HotSpot(TM) 64-Bit Server VM (build 20.45-b01-451, mixed mode)

Now that we've checked for prerequisites, we're now ready to install and set up the three open source tools.

## Set Up Elasticsearch

To install Elasticsearch, please download and extract the Elasticsearch package as shown below.

    :::term
    $ curl -O https://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-0.90.0.RC2.tar.gz
    $ tar zxvf elasticsearch-0.90.0.RC2.tar.gz
    $ cd elasticsearch-0.90.0.RC2/

Once installation is complete, start Elasticsearch.

    :::term
    $ ./bin/elasticsearch -f

## Set Up Kibana

To install Kibana, download it via the official webpage and extract it. Kibana is a HTML / CSS / JavaScript application.

    :::term
    $ curl -O https://download.elasticsearch.org/kibana/kibana/kibana-3.0.0milestone5.tar.gz
    $ tar zxvf kibana-3.0.0milestone5.tar.gz
    $ cd kibana-3.0.0milestone5/

Once installation is complete, start Kibana and open `index.html`. You can modify Kibana's configuration via `config.js`.

    :::term
    $ open index.html

## Set Up Fluentd (td-agent)

In this guide We'll install td-agent, the stable release of Fluentd. Please refer to the guides below for detailed installation steps.

* [Debian Package](install-by-deb)
* [RPM Package](install-by-rpm)
* [Ruby gem](install-by-gem)

Next, we'll install the Elasticsearch plugin for Fluentd: fluent-plugin-elasticsearch.

NOTE: The latest version of fluent-plugin-elasticsearch requires <strong>libcurl</strong> and <strong>make</strong> (if your system has no "make"). For Ubuntu, run "sudo apt-get install libcurl4-gnutls-dev". For RedHat/CentOS, run "sudo yum install libcurl-devel".

Then, install fluent-plugin-elasticsearch as follows.

* td-agent v2: `/usr/sbin/td-agent-gem install fluent-plugin-elasticsearch`
* td-agent v1: `/usr/lib/fluent/ruby/bin/fluent-gem install fluent-plugin-elasticsearch`

We'll configure td-agent (Fluentd) to interface properly with Elasticsearch. Please modify `/etc/td-agent/td-agent.conf` as shown below:

    :::text
    <source>
      type syslog
      port 42185
      tag syslog
    </source>

    <source>
      type forward
    </source>

    <match syslog.**>
      type elasticsearch
      logstash_format true
      flush_interval 10s # for testing
    </match>

fluent-plugin-elasticsearch comes with a logstash_format option that allows Kibana to search stored event logs in Elasticsearch.

Once everything has been set up and configured, we'll start td-agent.

    :::term
    $ sudo /etc/init.d/td-agent start

## Set Up rsyslogd

In our final step, we'll forward the logs from your rsyslogd to Fluentd. Please add the following line to your `/etc/rsyslog.conf`, and restart rsyslog. This will forward your local syslog to Fluentd, and Fluentd in turn will forward the logs to Elasticsearch.

    :::text
    *.* @127.0.0.1:42185

Please restart the rsyslog service once the modification is complete.

    :::text
    $ sudo /etc/init.d/rsyslog restart

## Store and Search Event Logs

Once Fluentd receives some event logs from rsyslog and has flushed them to Elasticsearch, you can search the stored logs using Kibana by accessing Kibana's index.html in your browser.

<center><img src="/images/kibana-screenshot.png" width="90%"/></center><br/><br/>

To manually send logs to Elasticsearch, please use the `logger` command.

    :::text
    $ logger -t test foobar

When debugging your td-agent configuration, using [out_copy](out_copy) + [out_stdout](out_stdout) will be useful. All the logs including errors can be found at `/etc/td-agent/td-agent.log`.

    :::text
    <match syslog.**>
      type copy
      <store>
        # for debug (see /var/log/td-agent.log)
        type stdout
      </store>
      <store>
        type elasticsearch
        logstash_format true
        flush_interval 10s # for testing
      </store>
    </match>

## Demo Environment

Please access the Kibana Demo Environment from the link below.

- [Kibana Demo Environment](http://kibana.fluentd.org/)

## Conclusion

This article introduced the combination of Fluentd and Kibana (with Elasticsearch) which achieves a free alternative to Splunk: storing and searching machine logs. The examples provided in this article have not been tuned.

If you will be using these components in production, you may want to modify some of the configurations (e.g. JVM, Elasticsearch, Fluentd buffer, etc.) according to your needs.

## Learn More

- [Fluentd Architecture](architecture)
- [Fluentd Get Started](quickstart)
- [Downloading Fluentd](http://www.fluentd.org/download)
