# openshift-health-check-exercise
Exercise in adding health probes to an application in Openshift.

## Goals
* Deploy an application with HTTP endpoints to openshift
* Add readiness and liveness probes to the application

## Steps

### Background

### Setup
1. Log in to the CLI of Openshift as the `developer` user.
1. Create a new project, e.g. `nodejs-helloworld`
1. Deploy the nodejs app we used in [web-console intro lab](https://github.com/svejk-ciber/openshift-web-console-deploy-image-exercise):
   ```
   $ oc new-app --name helloworld --env="WORLD_STATE=infected" svejkciber/nodejs-helloworld:00ce99e2badb634a700862bc97963a8d28032f46
   ...
   --> Success
    Application is not exposed. You can expose services to the outside world by executing one or more of thecommands below:
     'oc expose svc/helloworld'
    Run 'oc status' to view your app.
   ```
 1. Verify that the app is running with `oc status`, `oc get pod` and view the running pod's log.
 1. Expose an external  HTTP route to the app (_hint_: just follow the advice given above)
 1. Test the app with a web broser or `curl` (_hint_: get the DNS name of the route from `oc get route`).
 
 ### Add HTTP readiness probe
 Since the application's default HTTP endpoint normally returns `200 OK`, we can simply reuse it for health probes. 
 _Note_ that this is somewhat quick and dirty,  in realistic applications one would usually implement dedicated endpoints for each probe, e.g. `/isReady` and `/isAlive` .
 
 1. Review Openshift's informational warning about missing health probes:
    ```
    $ oc status --suggest
    ...
    Info:
      * pod/helloworld-1-deploy has no liveness probe to verify pods are still running.
        try: oc set probe pod/helloworld-1-deploy --liveness ...
      * dc/helloworld has no readiness probe to verify pods are ready to accept traffic or ensure deployment is successful.
        try: oc set probe dc/helloworld --readiness ...
      * dc/helloworld has no liveness probe to verify pods are still running.
        try: oc set probe dc/helloworld --liveness ...
    ```
    We will fix these issues soon.
 1. Add a readiness probe to the deployment configuration:
    ```
    $ oc set probe dc/helloworld --readiness --get-url=http://:8080/
    ```
 1. Verify that the probe is added to the `DeploymentConfiguration`
    ```
    $ oc describe dc/helloworld
    ...
    Pod Template:
    Labels:       app=helloworld
    ...
    Containers:
     helloworld:
     ...
     Readiness:  http-get http://:8080/ delay=0s timeout=1s period=10s #success=1 #failure=3
     ...
    ```
    We have added a readines probe with default parameter.
 
 ### Add a HTTP liveness probe
 This step is identical to the above probe configuration, except that the prope type argument in step 2
 is `--liveness` instead of  `--readiness`.
 
 
 ### Check that the project status is ok with respect to probes
 Run `oc status` again to check that there are no more complaints of missing probes.
 
 ## Bonus
 1. Fork the Git repo of the nodejs-helloworld app at https://github.com/svejk-ciber/nodejs-helloworld.
 1. Perform a source build from your forked repo in Openshift. The `new-app` command becomes something like:
    ```
    $ oc new-app --name <NAME> --env="WORLD_STATE=<SOME STATE>" https://github.com/svejk-ciber/nodejs-helloworld
    ```
    You may have to specify the docker source strategy, if Openshift doesn't pick up that this is a Dockerfile build.
 1. Implement a couple of custom probe endpoints, e.g. `/isAlive` and  `isReady`. Tweak the implementations so that 
    the probes fail. Observe Openhsift/Kubernetes behaviour on failing probes and adjust the probe implementations.
 
 
