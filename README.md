sbt-cassandra-plugin
====================

This is a work in progress project.  The goal is to allow launching [Cassandra](http://cassandra.apache.org) during tests, and test your application against it.
at this pre-mature phase, only the very basic functionality works. API is not final, and might (probably will) change down the road.
However, the plugin is already usable as is.

## Installation ##
Add the following to your `project/plugins.sbt` file:
    addSbtPlugin("com.github.hochgi" % "sbt-cassandra-plugin" % "0.1-SNAPSHOT")
Until i'll get this plugin hosted, you can build it yourself, and use `sbt publish-local` to have it available in your local `~/.ivy`.

## Usage ##
### Basic: ###
    import com.github.hochgi.sbt.cassandra._
    
    seq(CassandraPlugin.cassandraSettings:_ *)
    
    test in Test <<= (test in Test).dependsOn(startCassandra)
### Advanced: ##
To choose a specific version of cassandra (default is 2.0.6), you can use:
    cassandraVersion := "2.0.6"
Now, assuming your project depends on cassandra (well duh...), you probably have your dependencies declared like:
    libraryDependencies += "org.apache.cassandra" % "apache-cassandra" % "2.0.6"
~~a nifty tweak to set the correct version automatically, could look something like:~~(currently not working, due to a cyclic reference)
    cassandraVersion <<= (libraryDependencies) (_.collect{case ModuleID("org.apache.cassandra","apache-cassandra",version,_,_,_,_,_,_,_,_) => version}.head)
~~to stop cassandra after the tests finished, you could use:~~(currently not working)
    testOptions in Test <+= (cassandraPid) map {pid => Tests.Cleanup(() => Process(Seq("kill",pid)).!)}
to use special configuration files suited for your use case, use:
    cassandraConfigDir := "/path/to/your/conf/dir"
to intialize cassandra with your custom cassandra-cli commands, use:
    cassandraCliInit := "/path/to/cassandra-cli/commands/file"
~~to intialize cassandra with your custom cql commands, use:~~(not implemented yet)
    cassandraCqlInit := "/path/to/cassandra-cql/commands/file"
to set specific JVM arguments, modify this however you want:
    cassandraJvmArgs ~= (_.collect{/* some defaults you want to change */})
or:
    cassandraJvmArgs ++= Seq(/* some additions that were not in the defaults */)