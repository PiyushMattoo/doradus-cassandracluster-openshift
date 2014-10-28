Doradus on OpenShift
===============================

This is a simple Red Hat OpenShift example that demonstrates how to deploy and use Doradus (https://github.com/dell-oss/Doradus) in one gear that connects to Cassandra instance on another gear.  


Running on OpenShift
----------------------------

Create an account at https://www.openshift.com

Setup cluster of 2 Cassandra nodes (https://github.com/dell-oss/cassandra-instance)

Create Do It Yourself (DIY) Application. 

    rhc app create doradus diy

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

    Invoke this URL to list all applications under Doradus
    http://doradusdemo-$yournamespace.$youropenshiftserver/_applications
    
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
    
    Invoke the URL http://doradusdemo-$yournamespace.$youropenshiftserver/_applications again to see the Doradus the application and its data.

    Read more about REST APIs on the Doradus website.

    

