This modules provides a logback appender that writes to Graylog2 via GELF over TCP.

To use this module, add the following dependency to your build.sbt/Build.scala file:

    "org.graylog2" % "play2-graylog2" % "1.1"

TODO: publish to maven central

In your application.conf you can set a couple of entries to configure the behavior of the appender:
  
    app.graylog2.enable=true                        # enable graylog2 play plugin (overall). It will override access log settings
    graylog2.appender.host="127.0.0.1:12201"        # the graylog2 server to send logs to (its GELF TCP input)
    graylog2.appender.sourcehost="example.org"      # string to use as "source" field of the GELF messages
    graylog2.appender.queue-size=512                # the number of logged, but unsent log messages, stored in memory
    graylog2.appender.reconnect-interval=500ms      # if the connection graylog2 is lost, wait this long until trying a reconnect
    graylog2.appender.connect-timeout=1s            # the TCP connect timeout
    graylog2.appender.tcp-nodelay=false             # optionally disable Nagle's algorithm, improves throughput if you log small messages a lot
    graylog2.appender.sendbuffersize                # if unset, it uses the systems default TCP send buffer size, override to use a different value

To make use of the AccessLog that sends a structured access log to the configured graylog2 servers, include the Filter in your Global object:

For Java:

    import org.graylog2.logback.appender.AccessLog;

    @Override
    @SuppressWarnings("unchecked")
    public <T extends EssentialFilter> Class<T>[] filters() {
        return new Class[] {AccessLog.class};
    }

For Scala:

    import play.api.mvc._
    import org.graylog2.logback.appender.ScalaAccessLog

    object Global extends WithFilters(ScalaAccessLog) {
        / * optionally implement methods from trait GlobalSettings here ... */
    }

By default the access log is disabled, because it can potentially generate large amounts of data and could affect the throughput of your
application in a non-trivial manner, because it is a request Filter.

To use it, set the following in your application.conf:

    graylog2.appender.send-access-log=true

Finally, enable the plugin in your `conf/play.plugins`:

    10000:org.graylog2.logback.appender.Graylog2Plugin

Also consult the Play documentation at http://www.playframework.com/documentation/2.2.0/ScalaHttpFilters if you would like to know more about Filters.
