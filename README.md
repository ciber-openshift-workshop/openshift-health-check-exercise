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
 1. Test the app with a web broser or `curl` (_hint_: get the DNS name of the route from `oc get route`.
 
 
