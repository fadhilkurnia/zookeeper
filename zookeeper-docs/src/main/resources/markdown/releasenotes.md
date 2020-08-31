<!--
Copyright 2002-2020 The Apache Software Foundation

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
//-->



# Release Notes - ZooKeeper - Version 3.6.2
    
## Bug

* [ZOOKEEPER-3112](https://issues.apache.org/jira/browse/ZOOKEEPER-3112) - fd leak due to UnresolvedAddressException on connect.
* [ZOOKEEPER-3215](https://issues.apache.org/jira/browse/ZOOKEEPER-3215) - Handle Java 9/11 additions of covariant return types to java.nio.ByteBuffer methods
* [ZOOKEEPER-3772](https://issues.apache.org/jira/browse/ZOOKEEPER-3772) - JettyAdminServer should not allow HTTP TRACE method
* [ZOOKEEPER-3792](https://issues.apache.org/jira/browse/ZOOKEEPER-3792) - Reconcile document site in 3.5.7 &amp; 3.6.0
* [ZOOKEEPER-3801](https://issues.apache.org/jira/browse/ZOOKEEPER-3801) - Fix Jenkins link in pom
* [ZOOKEEPER-3814](https://issues.apache.org/jira/browse/ZOOKEEPER-3814) - ZooKeeper config propagates even with disabled dynamic reconfig
* [ZOOKEEPER-3818](https://issues.apache.org/jira/browse/ZOOKEEPER-3818) - fix zkServer.sh status command to support SSL-only server
* [ZOOKEEPER-3829](https://issues.apache.org/jira/browse/ZOOKEEPER-3829) - Zookeeper refuses request after node expansion
* [ZOOKEEPER-3830](https://issues.apache.org/jira/browse/ZOOKEEPER-3830) - After add a new node, zookeeper cluster won&#39;t commit any proposal if this new node is leader
* [ZOOKEEPER-3832](https://issues.apache.org/jira/browse/ZOOKEEPER-3832) - ZKHostnameVerifier rejects valid certificates with subjectAltNames
* [ZOOKEEPER-3842](https://issues.apache.org/jira/browse/ZOOKEEPER-3842) - Rolling scale up of zookeeper cluster does not work with reconfigEnabled=false
* [ZOOKEEPER-3857](https://issues.apache.org/jira/browse/ZOOKEEPER-3857) - ZooKeeper 3.6 doesn&#39;t build after Curator test committed
* [ZOOKEEPER-3865](https://issues.apache.org/jira/browse/ZOOKEEPER-3865) - fix backward-compatibility for ZooKeeperServer constructor
* [ZOOKEEPER-3876](https://issues.apache.org/jira/browse/ZOOKEEPER-3876) - zkServer.sh status command fails when IPV6 is configured
* [ZOOKEEPER-3878](https://issues.apache.org/jira/browse/ZOOKEEPER-3878) - Client connection fails if IPV6 is not enclosed in square brackets
* [ZOOKEEPER-3885](https://issues.apache.org/jira/browse/ZOOKEEPER-3885) - zoo_aremove_watches segfault: zk_hashtable needs locking!
* [ZOOKEEPER-3895](https://issues.apache.org/jira/browse/ZOOKEEPER-3895) - Client side NullPointerException in case of empty Multi operation
* [ZOOKEEPER-3905](https://issues.apache.org/jira/browse/ZOOKEEPER-3905) - Race condition causes sessions to be created for clients even though their certificate authentication has failed
                
## Improvement

* [ZOOKEEPER-3678](https://issues.apache.org/jira/browse/ZOOKEEPER-3678) - Remove Redundant GroupID from Maven POMs
* [ZOOKEEPER-3679](https://issues.apache.org/jira/browse/ZOOKEEPER-3679) - Upgrade maven-compiler-plugin For ZooKeeper-jute
* [ZOOKEEPER-3761](https://issues.apache.org/jira/browse/ZOOKEEPER-3761) - upgrade JLine jar dependency
* [ZOOKEEPER-3790](https://issues.apache.org/jira/browse/ZOOKEEPER-3790) - zkpython: Minor compilation and testing issues
* [ZOOKEEPER-3831](https://issues.apache.org/jira/browse/ZOOKEEPER-3831) - Add a test that does a minimal validation of Apache Curator
* [ZOOKEEPER-3834](https://issues.apache.org/jira/browse/ZOOKEEPER-3834) - Do Not Set Explicit Test Includes in POM
* [ZOOKEEPER-3844](https://issues.apache.org/jira/browse/ZOOKEEPER-3844) - Add useful metrics for ZK servers
* [ZOOKEEPER-3893](https://issues.apache.org/jira/browse/ZOOKEEPER-3893) - Enhance documentation for property ssl.clientAuth 
* [ZOOKEEPER-3913](https://issues.apache.org/jira/browse/ZOOKEEPER-3913) - Upgrade to Netty 4.1.50.Final
            
°° Task

* [ZOOKEEPER-3817](https://issues.apache.org/jira/browse/ZOOKEEPER-3817) - owasp failing due to CVE-2020-9488
* [ZOOKEEPER-3896](https://issues.apache.org/jira/browse/ZOOKEEPER-3896) - Migrate Jenkins jobs to ci-hadoop.apache.org
                                                                                                                                                
## Sub-task

* [ZOOKEEPER-3845](https://issues.apache.org/jira/browse/ZOOKEEPER-3845) - Add metric JVM_PAUSE_TIME
* [ZOOKEEPER-3846](https://issues.apache.org/jira/browse/ZOOKEEPER-3846) - Add a couple TLS related metrics
* [ZOOKEEPER-3847](https://issues.apache.org/jira/browse/ZOOKEEPER-3847) - Add a couple metrics to help track Netty memory usage
* [ZOOKEEPER-3856](https://issues.apache.org/jira/browse/ZOOKEEPER-3856) - Add a couple metrics to track inflight diff syncs and snap syncs




In previous releases of ZooKeeper any watches registered by clients were lost if the client lost a connection to a ZooKeeper server.
This meant that developers had to track watches they were interested in and reregister them if a session disconnect event was received.
In this release the client library tracks watches that a client has registered and reregisters the watches when a connection is made to a new server.
Applications that still manually reregister interest should continue working properly as long as they are able to handle unsolicited watches.
For example, an old application may register a watch for /foo and /goo, lose the connection, and reregister only /goo.
As long as the application is able to receive a notification for /foo, (probably ignoring it) it does not need to be changed.
One caveat to the watch management: it is possible to miss an event for the creation and deletion of a znode if watching for creation and both the create and delete happens while the client is disconnected from ZooKeeper.

This release also allows clients to specify call specific watch functions.
This gives the developer the ability to modularize logic in different watch functions rather than cramming everything in the watch function attached to the ZooKeeper handle.
Call specific watch functions receive all session events for as long as they are active, but will only receive the watch callbacks for which they are registered.

<a name="Java+API"></a>
#### Java API

1. The java package structure has changed from **com.yahoo.zookeeper*** to **org.apache.zookeeper***. This will probably affect all of your java code which makes use of ZooKeeper APIs (typically import statements)
1. A number of constants used in the client ZooKeeper API were re-specified using enums (rather than ints). See [ZOOKEEPER-7](https://issues.apache.org/jira/browse/ZOOKEEPER-7), [ZOOKEEPER-132](https://issues.apache.org/jira/browse/ZOOKEEPER-132) and [ZOOKEEPER-139](https://issues.apache.org/jira/browse/ZOOKEEPER-139) for full details
1. [ZOOKEEPER-18](https://issues.apache.org/jira/browse/ZOOKEEPER-18) removed KeeperStateChanged, use KeeperStateDisconnected instead

Also see [the current Java API](http://zookeeper.apache.org/docs/current/apidocs/zookeeper-server/index.html)

<a name="C+API"></a>
#### C API

1. A number of constants used in the client ZooKeeper API were renamed in order to reduce namespace collision, see [ZOOKEEPER-6](https://issues.apache.org/jira/browse/ZOOKEEPER-6) for full details

<a name="migration_data"></a>
### Migrating Server Data
The following issues resulted in changes to the on-disk data format (the snapshot and transaction log files contained within the ZK data directory) and require a migration utility to be run. 
  
* [ZOOKEEPER-27 Unique DB identifiers for servers and clients](https://issues.apache.org/jira/browse/ZOOKEEPER-27)
* [ZOOKEEPER-32 CRCs for ZooKeeper data](https://issues.apache.org/jira/browse/ZOOKEEPER-32)
* [ZOOKEEPER-33 Better ACL management](https://issues.apache.org/jira/browse/ZOOKEEPER-33)
* [ZOOKEEPER-38 headers (version+) in log/snap files](https://issues.apache.org/jira/browse/ZOOKEEPER-38)

**The following must be run once, and only once, when upgrading the ZooKeeper server instances to version 3.0.0.**

###### Note
> The <dataLogDir> and <dataDir> directories referenced below are specified by the *dataLogDir*
  and *dataDir* specification in your ZooKeeper config file respectively. *dataLogDir* defaults to
  the value of *dataDir* if not specified explicitly in the ZooKeeper server config file (in which
  case provide the same directory for both parameters to the upgrade utility).

1. Shutdown the ZooKeeper server cluster.
1. Backup your <dataLogDir> and <dataDir> directories
1. Run upgrade using
    * `bin/zkServer.sh upgrade <dataLogDir> <dataDir>`
    
    or
    
    * `java -classpath pathtolog4j:pathtozookeeper.jar UpgradeMain <dataLogDir> <dataDir>`
    
    where <dataLogDir> is the directory where all transaction logs (log.*) are stored. <dataDir> is the directory where all the snapshots (snapshot.*) are stored.
1. Restart the cluster.

If you have any failure during the upgrade procedure keep reading to sanitize your database. 

This is how upgrade works in ZooKeeper. This will help you troubleshoot in case you have problems while upgrading

1. Upgrade moves files from `<dataLogDir>` and `<dataDir>` to `<dataLogDir>/version-1/` and `<dataDir>/version-1` respectively (version-1 sub-directory is created by the upgrade utility).
1. Upgrade creates a new version sub-directory `<dataDir>/version-2` and `<dataLogDir>/version-2`
1. Upgrade reads the old database from `<dataDir>/version-1` and `<dataLogDir>/version-1` into the memory and creates a new upgraded snapshot.
1. Upgrade writes the new database in `<dataDir>/version-2`.

Troubleshooting.


1. In case you start ZooKeeper 3.0 without upgrading from 2.0 on a 2.0 database - the servers will start up with an empty database.
 This is because the servers assume that `<dataDir>/version-2` and `<dataLogDir>/version-2` will have the database to start with. Since this will be empty
 in case of no upgrade, the servers will start with an empty database. In such a case, shutdown the ZooKeeper servers, remove the version-2 directory (remember
 this will lead to loss of updates after you started 3.0.)
 and then start the upgrade procedure.
1. If the upgrade fails while trying to rename files into the version-1 directory, you should try and move all the files under `<dataDir>/version-1`
 and `<dataLogDir>/version-1` to `<dataDir>` and `<dataLogDir>` respectively. Then try upgrade again.
1. If you do not wish to run with ZooKeeper 3.0 and prefer to run with ZooKeeper 2.0 and have already upgraded - you can run ZooKeeper 2 with 
 the `<dataDir>` and `<dataLogDir>` directories changed to `<dataDir>/version-1` and `<dataLogDir>/version-1`. Remember that you will lose all the updates that you made after the upgrade.

<a name="migration_config"></a>
### Migrating Server Configuration

There is a significant change to the ZooKeeper server configuration file.

The default election algorithm, specified by the *electionAlg* configuration attribute, has
changed from a default of *0* to a default of *3*. See
[Cluster Options](zookeeperAdmin.html#sc_clusterOptions) section of the administrators guide, specifically
the *electionAlg* and *server.X* properties.

You will either need to explicitly set *electionAlg* to its previous default value
of *0* or change your *server.X* options to include the leader election port.


<a name="changes"></a>
## Changes Since ZooKeeper 2.2.1

Version 2.2.1 code, documentation, binaries, etc... are still accessible on [SourceForge](http://sourceforge.net/projects/zookeeper)

| Issue | Notes |
|-------|-------|
|[ZOOKEEPER-43](https://issues.apache.org/jira/browse/ZOOKEEPER-43)|Server side of auto reset watches.|
|[ZOOKEEPER-132](https://issues.apache.org/jira/browse/ZOOKEEPER-132)|Create Enum to replace CreateFlag in ZooKepper.create method|
|[ZOOKEEPER-139](https://issues.apache.org/jira/browse/ZOOKEEPER-139)|Create Enums for WatcherEvent's KeeperState and EventType|
|[ZOOKEEPER-18](https://issues.apache.org/jira/browse/ZOOKEEPER-18)|keeper state inconsistency|
|[ZOOKEEPER-38](https://issues.apache.org/jira/browse/ZOOKEEPER-38)|headers in log/snap files|
|[ZOOKEEPER-8](https://issues.apache.org/jira/browse/ZOOKEEPER-8)|Stat enchaned to include num of children and size|
|[ZOOKEEPER-6](https://issues.apache.org/jira/browse/ZOOKEEPER-6)|List of problem identifiers in zookeeper.h|
|[ZOOKEEPER-7](https://issues.apache.org/jira/browse/ZOOKEEPER-7)|Use enums rather than ints for types and state|
|[ZOOKEEPER-27](https://issues.apache.org/jira/browse/ZOOKEEPER-27)|Unique DB identifiers for servers and clients|
|[ZOOKEEPER-32](https://issues.apache.org/jira/browse/ZOOKEEPER-32)|CRCs for ZooKeeper data|
|[ZOOKEEPER-33](https://issues.apache.org/jira/browse/ZOOKEEPER-33)|Better ACL management|
|[ZOOKEEPER-203](https://issues.apache.org/jira/browse/ZOOKEEPER-203)|fix datadir typo in releasenotes|
|[ZOOKEEPER-145](https://issues.apache.org/jira/browse/ZOOKEEPER-145)|write detailed release notes for users migrating from 2.x to 3.0|
|[ZOOKEEPER-23](https://issues.apache.org/jira/browse/ZOOKEEPER-23)|Auto reset of watches on reconnect|
|[ZOOKEEPER-191](https://issues.apache.org/jira/browse/ZOOKEEPER-191)|forrest docs for upgrade.|
|[ZOOKEEPER-201](https://issues.apache.org/jira/browse/ZOOKEEPER-201)|validate magic number when reading snapshot and transaction logs|
|[ZOOKEEPER-200](https://issues.apache.org/jira/browse/ZOOKEEPER-200)|the magic number for snapshot and log must be different|
|[ZOOKEEPER-199](https://issues.apache.org/jira/browse/ZOOKEEPER-199)|fix log messages in persistence code|
|[ZOOKEEPER-197](https://issues.apache.org/jira/browse/ZOOKEEPER-197)|create checksums for snapshots|
|[ZOOKEEPER-198](https://issues.apache.org/jira/browse/ZOOKEEPER-198)|apache license header missing from FollowerSyncRequest.java|
|[ZOOKEEPER-5](https://issues.apache.org/jira/browse/ZOOKEEPER-5)|Upgrade Feature in Zookeeper server.|
|[ZOOKEEPER-194](https://issues.apache.org/jira/browse/ZOOKEEPER-194)|Fix terminology in zookeeperAdmin.xml|
|[ZOOKEEPER-151](https://issues.apache.org/jira/browse/ZOOKEEPER-151)|Document change to server configuration|
|[ZOOKEEPER-193](https://issues.apache.org/jira/browse/ZOOKEEPER-193)|update java example doc to compile with latest zookeeper|
|[ZOOKEEPER-187](https://issues.apache.org/jira/browse/ZOOKEEPER-187)|CreateMode api docs missing|
|[ZOOKEEPER-186](https://issues.apache.org/jira/browse/ZOOKEEPER-186)|add new "releasenotes.xml" to forrest documentation|
|[ZOOKEEPER-190](https://issues.apache.org/jira/browse/ZOOKEEPER-190)|Reorg links to docs and navs to docs into related sections|
|[ZOOKEEPER-189](https://issues.apache.org/jira/browse/ZOOKEEPER-189)|forrest build not validated xml of input documents|
|[ZOOKEEPER-188](https://issues.apache.org/jira/browse/ZOOKEEPER-188)|Check that election port is present for all servers|
|[ZOOKEEPER-185](https://issues.apache.org/jira/browse/ZOOKEEPER-185)|Improved version of FLETest|
|[ZOOKEEPER-184](https://issues.apache.org/jira/browse/ZOOKEEPER-184)|tests: An explicit include derective is needed for the usage of memcpy functions|
|[ZOOKEEPER-183](https://issues.apache.org/jira/browse/ZOOKEEPER-183)|Array subscript is above array bounds in od_completion, src/cli.c.|
|[ZOOKEEPER-182](https://issues.apache.org/jira/browse/ZOOKEEPER-182)|zookeeper_init accepts empty host-port string and returns valid pointer to zhandle_t.|
|[ZOOKEEPER-17](https://issues.apache.org/jira/browse/ZOOKEEPER-17)|zookeeper_init doc needs clarification|
|[ZOOKEEPER-181](https://issues.apache.org/jira/browse/ZOOKEEPER-181)|Some Source Forge Documents did not get moved over: javaExample, zookeeperTutorial, zookeeperInternals|
|[ZOOKEEPER-180](https://issues.apache.org/jira/browse/ZOOKEEPER-180)|Placeholder sections needed in document for new topics that the umbrella jira discusses|
|[ZOOKEEPER-179](https://issues.apache.org/jira/browse/ZOOKEEPER-179)|Programmer's Guide "Basic Operations" section is missing content|
|[ZOOKEEPER-178](https://issues.apache.org/jira/browse/ZOOKEEPER-178)|FLE test.|
|[ZOOKEEPER-159](https://issues.apache.org/jira/browse/ZOOKEEPER-159)|Cover two corner cases of leader election|
|[ZOOKEEPER-156](https://issues.apache.org/jira/browse/ZOOKEEPER-156)|update programmer guide with acl details from old wiki page|
|[ZOOKEEPER-154](https://issues.apache.org/jira/browse/ZOOKEEPER-154)|reliability graph diagram in overview doc needs context|
|[ZOOKEEPER-157](https://issues.apache.org/jira/browse/ZOOKEEPER-157)|Peer can't find existing leader|
|[ZOOKEEPER-155](https://issues.apache.org/jira/browse/ZOOKEEPER-155)|improve "the zookeeper project" section of overview doc|
|[ZOOKEEPER-140](https://issues.apache.org/jira/browse/ZOOKEEPER-140)|Deadlock in QuorumCnxManager|
|[ZOOKEEPER-147](https://issues.apache.org/jira/browse/ZOOKEEPER-147)|This is version of the documents with most of the [tbd...] scrubbed out|
|[ZOOKEEPER-150](https://issues.apache.org/jira/browse/ZOOKEEPER-150)|zookeeper build broken|
|[ZOOKEEPER-136](https://issues.apache.org/jira/browse/ZOOKEEPER-136)|sync causes hang in all followers of quorum.|
|[ZOOKEEPER-134](https://issues.apache.org/jira/browse/ZOOKEEPER-134)|findbugs cleanup|
|[ZOOKEEPER-133](https://issues.apache.org/jira/browse/ZOOKEEPER-133)|hudson tests failing intermittently|
|[ZOOKEEPER-144](https://issues.apache.org/jira/browse/ZOOKEEPER-144)|add tostring support for watcher event, and enums for event type/state|
|[ZOOKEEPER-21](https://issues.apache.org/jira/browse/ZOOKEEPER-21)|Improve zk ctor/watcher|
|[ZOOKEEPER-142](https://issues.apache.org/jira/browse/ZOOKEEPER-142)|Provide Javadoc as to the maximum size of the data byte array that may be stored within a znode|
|[ZOOKEEPER-93](https://issues.apache.org/jira/browse/ZOOKEEPER-93)|Create Documentation for Zookeeper|
|[ZOOKEEPER-117](https://issues.apache.org/jira/browse/ZOOKEEPER-117)|threading issues in Leader election|
|[ZOOKEEPER-137](https://issues.apache.org/jira/browse/ZOOKEEPER-137)|client watcher objects can lose events|
|[ZOOKEEPER-131](https://issues.apache.org/jira/browse/ZOOKEEPER-131)|Old leader election can elect a dead leader over and over again|
|[ZOOKEEPER-130](https://issues.apache.org/jira/browse/ZOOKEEPER-130)|update build.xml to support apache release process|
|[ZOOKEEPER-118](https://issues.apache.org/jira/browse/ZOOKEEPER-118)|findbugs flagged switch statement in followerrequestprocessor.run|
|[ZOOKEEPER-115](https://issues.apache.org/jira/browse/ZOOKEEPER-115)|Potential NPE in QuorumCnxManager|
|[ZOOKEEPER-114](https://issues.apache.org/jira/browse/ZOOKEEPER-114)|cleanup ugly event messages in zookeeper client|
|[ZOOKEEPER-112](https://issues.apache.org/jira/browse/ZOOKEEPER-112)|src/java/main ZooKeeper.java has test code embedded into it.|
|[ZOOKEEPER-39](https://issues.apache.org/jira/browse/ZOOKEEPER-39)|Use Watcher objects rather than boolean on read operations.|
|[ZOOKEEPER-97](https://issues.apache.org/jira/browse/ZOOKEEPER-97)|supports optional output directory in code generator.|
|[ZOOKEEPER-101](https://issues.apache.org/jira/browse/ZOOKEEPER-101)|Integrate ZooKeeper with "violations" feature on hudson|
|[ZOOKEEPER-105](https://issues.apache.org/jira/browse/ZOOKEEPER-105)|Catch Zookeeper exceptions and print on the stderr.|
|[ZOOKEEPER-42](https://issues.apache.org/jira/browse/ZOOKEEPER-42)|Change Leader Election to fast tcp.|
|[ZOOKEEPER-48](https://issues.apache.org/jira/browse/ZOOKEEPER-48)|auth_id now handled correctly when no auth ids present|
|[ZOOKEEPER-44](https://issues.apache.org/jira/browse/ZOOKEEPER-44)|Create sequence flag children with prefixes of 0's so that they can be lexicographically sorted.|
|[ZOOKEEPER-108](https://issues.apache.org/jira/browse/ZOOKEEPER-108)|Fix sync operation reordering on a Quorum.|
|[ZOOKEEPER-25](https://issues.apache.org/jira/browse/ZOOKEEPER-25)|Fuse module for Zookeeper.|
|[ZOOKEEPER-58](https://issues.apache.org/jira/browse/ZOOKEEPER-58)|Race condition on ClientCnxn.java|
|[ZOOKEEPER-56](https://issues.apache.org/jira/browse/ZOOKEEPER-56)|Add clover support to build.xml.|
|[ZOOKEEPER-75](https://issues.apache.org/jira/browse/ZOOKEEPER-75)|register the ZooKeeper mailing lists with nabble.com|
|[ZOOKEEPER-54](https://issues.apache.org/jira/browse/ZOOKEEPER-54)|remove sleeps in the tests.|
|[ZOOKEEPER-55](https://issues.apache.org/jira/browse/ZOOKEEPER-55)|build.xml failes to retrieve a release number from SVN and the ant target "dist" fails|
|[ZOOKEEPER-89](https://issues.apache.org/jira/browse/ZOOKEEPER-89)|invoke WhenOwnerListener.whenNotOwner when the ZK connection fails|
|[ZOOKEEPER-90](https://issues.apache.org/jira/browse/ZOOKEEPER-90)|invoke WhenOwnerListener.whenNotOwner when the ZK session expires and the znode is the leader|
|[ZOOKEEPER-82](https://issues.apache.org/jira/browse/ZOOKEEPER-82)|Make the ZooKeeperServer more DI friendly.|
|[ZOOKEEPER-110](https://issues.apache.org/jira/browse/ZOOKEEPER-110)|Build script relies on svnant, which is not compatible with subversion 1.5 working copies|
|[ZOOKEEPER-111](https://issues.apache.org/jira/browse/ZOOKEEPER-111)|Significant cleanup of existing tests.|
|[ZOOKEEPER-122](https://issues.apache.org/jira/browse/ZOOKEEPER-122)|Fix NPE in jute's Utils.toCSVString.|
|[ZOOKEEPER-123](https://issues.apache.org/jira/browse/ZOOKEEPER-123)|Fix the wrong class is specified for the logger.|
|[ZOOKEEPER-2](https://issues.apache.org/jira/browse/ZOOKEEPER-2)|Fix synchronization issues in QuorumPeer and FastLeader election.|
|[ZOOKEEPER-125](https://issues.apache.org/jira/browse/ZOOKEEPER-125)|Remove unwanted class declaration in FastLeaderElection.|
|[ZOOKEEPER-61](https://issues.apache.org/jira/browse/ZOOKEEPER-61)|Address in client/server test cases.|
|[ZOOKEEPER-75](https://issues.apache.org/jira/browse/ZOOKEEPER-75)|cleanup the library directory|
|[ZOOKEEPER-109](https://issues.apache.org/jira/browse/ZOOKEEPER-109)|cleanup of NPE and Resource issue nits found by static analysis|
|[ZOOKEEPER-76](https://issues.apache.org/jira/browse/ZOOKEEPER-76)|Commit 677109 removed the cobertura library, but not the build targets.|
|[ZOOKEEPER-63](https://issues.apache.org/jira/browse/ZOOKEEPER-63)|Race condition in client close|
|[ZOOKEEPER-70](https://issues.apache.org/jira/browse/ZOOKEEPER-70)|Add skeleton forrest doc structure for ZooKeeper|
|[ZOOKEEPER-79](https://issues.apache.org/jira/browse/ZOOKEEPER-79)|Document jacob's leader election on the wiki recipes page|
|[ZOOKEEPER-73](https://issues.apache.org/jira/browse/ZOOKEEPER-73)|Move ZK wiki from SourceForge to Apache|
|[ZOOKEEPER-72](https://issues.apache.org/jira/browse/ZOOKEEPER-72)|Initial creation/setup of ZooKeeper ASF site.|
|[ZOOKEEPER-71](https://issues.apache.org/jira/browse/ZOOKEEPER-71)|Determine what to do re ZooKeeper Changelog|
|[ZOOKEEPER-68](https://issues.apache.org/jira/browse/ZOOKEEPER-68)|parseACLs in ZooKeeper.java fails to parse elements of ACL, should be lastIndexOf rather than IndexOf|
|[ZOOKEEPER-130](https://issues.apache.org/jira/browse/ZOOKEEPER-130)|update build.xml to support apache release process.|
|[ZOOKEEPER-131](https://issues.apache.org/jira/browse/ZOOKEEPER-131)|Fix Old leader election can elect a dead leader over and over again.|
|[ZOOKEEPER-137](https://issues.apache.org/jira/browse/ZOOKEEPER-137)|client watcher objects can lose events|
|[ZOOKEEPER-117](https://issues.apache.org/jira/browse/ZOOKEEPER-117)|threading issues in Leader election|
|[ZOOKEEPER-128](https://issues.apache.org/jira/browse/ZOOKEEPER-128)|test coverage on async client operations needs to be improved|
|[ZOOKEEPER-127](https://issues.apache.org/jira/browse/ZOOKEEPER-127)|Use of non-standard election ports in config breaks services|
|[ZOOKEEPER-53](https://issues.apache.org/jira/browse/ZOOKEEPER-53)|tests failing on solaris.|
|[ZOOKEEPER-172](https://issues.apache.org/jira/browse/ZOOKEEPER-172)|FLE Test|
|[ZOOKEEPER-41](https://issues.apache.org/jira/browse/ZOOKEEPER-41)|Sample startup script|
|[ZOOKEEPER-33](https://issues.apache.org/jira/browse/ZOOKEEPER-33)|Better ACL management|
|[ZOOKEEPER-49](https://issues.apache.org/jira/browse/ZOOKEEPER-49)|SetACL does not work|
|[ZOOKEEPER-20](https://issues.apache.org/jira/browse/ZOOKEEPER-20)|Child watches are not triggered when the node is deleted|
|[ZOOKEEPER-15](https://issues.apache.org/jira/browse/ZOOKEEPER-15)|handle failure better in build.xml:test|
|[ZOOKEEPER-11](https://issues.apache.org/jira/browse/ZOOKEEPER-11)|ArrayList is used instead of List|
|[ZOOKEEPER-45](https://issues.apache.org/jira/browse/ZOOKEEPER-45)|Restructure the SVN repository after initial import |
|[ZOOKEEPER-1](https://issues.apache.org/jira/browse/ZOOKEEPER-1)|Initial ZooKeeper code contribution from Yahoo!|
