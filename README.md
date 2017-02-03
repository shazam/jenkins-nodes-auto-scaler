Tool that enable and disable build boxes according to the current needs.

The tool checks the Jenkins queue and in case is not empty it spins up additional boxes, based on the size of the queue and
 the number of workers per box.

When the queue is empty, it checks whether any node can be terminated by establishing whether the slave is in idle state.

One box is kept running all the time, apart during non working hours.

Once a node is online, it's kept alive for at least 10 minutes, since that's the minimum charge Google applies per node.

The tool assumes:
- all the boxes have the same number of workers configured
- working hours are considered to be between 7am and 7pm, Monday to Friday
- the slave names configured in Jenkins are the same as the node names configured in GCE

The tool options are:
```
  -gceProjectName string
    	project name where nodes are setup in GCE
  -gceZone string
    	GCE zone where nodes have been setup (default "europe-west1-b")
  -jenkinsApiToken string
    	Jenkins api token
  -jenkinsBaseUrl string
    	Jenkins server base url
  -jenkinsUsername string
    	Jenkins username
  -jobNameRequiringAllNodes string
    	Jenkins job name which requires all build nodes enabled
  -jobType string
    	defines which job to execute: auto_scaling, all_up, all_down (default "auto_scaling")
  -locationName string
    	Location used to determine working hours (default "Europe/London")
  -useLocalCreds
    	uses the local creds.json as credentials for Google Cloud APIs
  -workersPerBuildBox int
    	number of workers per build box (default 2)
``` 

![Jenkins nodes setup](/computer.png)

Assuming that GO is installed on the machine where the script will be executed, run:

```
go get github.com/shazam/jenkins-nodes-auto-scaler
```

The binary will be downloaded in the $GOPATH/bin folder.
Then simply run:

```
jenkins-nodes-auto-scaler \
   -jobType=auto_scaling \
   -gceProjectName=my-gce-project-name \
   -jenkinsApiToken=123456 \
   -jenkinsUsername=my-username \
   -jenkinsBaseUrl=http://my-jenkins.com:8888 \
   build1 build2 build3
```

where build1, build2 and build3 are the name of the nodes setup in Jenkins and in GCE.
If the script is not run within a GCE instance, the `useLocalCreds=true` option should 
be used, which indicates the script to use the service account credentials file `creds.json` 
in order to authenticate to the Google Cloud APIs.