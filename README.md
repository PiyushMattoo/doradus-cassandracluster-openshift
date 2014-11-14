Doradus on OpenShift
===============================

This is a Red Hat OpenShift prototype that demonstrates how to deploy and use Doradus (https://github.com/dell-oss/Doradus) deployed in one gear that connects to Cluster of Cassandra nodes on other gears on the same OpenShift broker.  


Running on OpenShift
----------------------------

Create an OpenShift account at https://www.openshift.com

Set up the OpenShift Environment (https://developers.openshift.com/en/getting-started-client-tools.html)

Set up amcluster of 2 Cassandra nodes (using https://github.com/dell-oss/cassandra-instance)
and note down the IP of 1 Cassandra node to use for CASSANDRA_NODE_IP below.

Create Doradus instance

    rhc app create doradus diy

Check if the private IP of 1st cassandra instance can be reached from Doradus enviroment 
   
    ssh <to the gear of doradus>
    curl http://<CASSANDRA_NODE_IP>:19042
    
    (expect to see the message other than “curl: (7) couldn't connect to host”)
    if you get that message, then you need to retry the step “Create Doradus instance” until Openshift gives you the environment that can connect to 1 of Cassandra nodes.

Config CQL cassandra node as part of the cluster above for Doradus

    cd doradus

    rhc env set CASSANDRA_NODE_IP=<CASSANDRA_NODE_IP> CASSANDRA_NODE_PORT=19042 DORADUS_STORAGE_SERVICE=com.dell.doradus.service.spider.SpiderService

Check the environment variables are set
	
    rhc env-list

Add this upstream repo

    git remote add upstream https://github.com/TraDuong1/doradus-cassandracluster-openshift
    git pull -s recursive -X theirs upstream master


Then push the repo upstream

    git push

Test 

    Doradus APIs
    ============

    Invoke this URL to list all applications under Doradus
    http://doradus-$yournamespace.$youropenshiftserver/_applications
    
    For the first time, you should expect to see empty list.
    
    Using Rest Client, post this XML schema to create a new application and its tables 
    in Doradus via the URL above
    
     <application name="MyApplication"> 
            <key>Test123</key> 
            <options> 
                <option name="StorageService">SpiderService</option> 
            </options> 
            <tables> 
                <table name="Movies"> 
                    <fields> 
                        <field name="Name" type="text"/> 
                        <field name="ReleaseDate" type="timestamp"/> 
                        <field name="Cancelled" type="boolean"/> 
                        <field name="Director" type="text"/> 
                        <field name="Leads" type="text" collection="true"/> 
                        <field name="Budget" type="integer"/> 
                    </fields> 
                </table> 
            </tables> 
        </application>
    
    Invoke the URL http://doradus-$yournamespace.$youropenshiftserver/_applications again to see the Doradus the application and its data.

    Read more about REST APIs on the Doradus website.


    Doradus High-availability
    =========================

    Bring down 1 of cassandra nodes
    
    rhc app stop -a <cassandra_server1> 
    or 
    rhc app stop -a <cassandra_server2>

    Verify http://doradus-$yournamespace.$youropenshiftserver/_applications still works


    Restart the server

    rhc app start -a <cassandra_server1>
    or 
    rhc app start -a <cassandra_server2>
    
    Verify http://doradus-$yournamespace.$youropenshiftserver/_applications works



    
    

