Rejoining MySQL nodes on Cluster:
https://dev.mysql.com/doc/mysql-router/8.0/en/mysql-router-deploying-sandbox.html

* **Checking processes:**
--------------------

```
ps -ef | grep -E "mysqlrouter|mysql"

systemctl status mysqlrouter / mysqld

```

* **Check Cluster STATUS:** (via mysqlsh)
--------------------

* Obs: It must be done from a working node

```
hostname=`hostname`;mysqlsh --uri root@${hostname}:3306 -e 'print(dba.getCluster().status())'


Output Example:

* Obs: **R/W** is the **MASTER** !

MySQL  SERVER_1:3306  JS > cluster.status ()
{
    "clusterName": "SACCluster", 
    "defaultReplicaSet": {
        "name": "default", 
        "primary": "SERVER_1:3306", 
        "ssl": "DISABLED", 
        "status": "OK", 
        "statusText": "Cluster is ONLINE and can tolerate up to ONE failure.", 
        "topology": {
            "SERVER_1:3306": {
                "address": "SERVER_1:3306", 
                "mode": "R/W", 
                "readReplicas": {}, 
                "role": "HA", 
                "status": "ONLINE"
            }, 
            "SERVER_2:3306": {
                "address": "SERVER_2:3306", 
                "mode": "R/O", 
                "readReplicas": {}, 
                "role": "HA", 
                "status": "ONLINE"
            }, 
            "SERVER_3:3306": {
                "address": "SERVER_3:3306", 
                "mode": "R/O", 
                "readReplicas": {}, 
                "role": "HA", 
                "status": "ONLINE"
            }
        }
    }, 
    "groupInformationSourceMember": "mysql://root@SERVER_1:3306"

```

* **Login:**
--------------------

```
mysqlsh --uri root@${hostname}:3306
```

* **Define Cluster variable**
--------------------

```
MySQL SERVER_1:3306 JS > var cluster = dba.getCluster()
```

* **Rejoin**
-------------

```
MySQL SERVER_1:3306 JS > cluster.rejoinInstance ('SERVER_2:3306')

Obs: SERVER_2 is the hostname of the FAILED/MISSING node
```


* **Recover ERROR STATUS from simultaneous SQL nodes**
------------------

In cases where all servers are failed, you can't check the Cluster Status <p>
All MySQL processes must be restarted, in all the servers from the Cluster <p>
After that, reboot the cluster. <p>

* **Error Message**
```
This function
is not available through a session to a standalone instance
```

* **Commands**

```
MySQL SERVER_1:3306 JS > var cluster = dba.getCluster()
MySQL SERVER_1:3306 JS > dba.rebootClusterFromCompleteOutage()


```
