# Sample Multi Project CI/CD Pipeline

- Setup the OpenShift environment with the following Projects:

```
oc new-project cicd --description="CI/CD Pipeline Demo"
oc new-project cicd-dev --description="CI/CD - Dev"
oc new-project cicd-prod --description="CI/CD - Prod"
oc new-project cicd-staging --description="CI/CD - Staging"
```

- Create `jenkins` Service Account in the cicd project

```
oc create serviceaccount jenkins -n cicd
```

- Give `jenkins` Service Account `edit` access to the other Projects

```
oc policy add-role-to-user edit system:serviceaccount:cicd:jenkins -n cicd-dev
oc policy add-role-to-user edit system:serviceaccount:cicd:jenkins -n cicd-prod
oc policy add-role-to-user edit system:serviceaccount:cicd:jenkins -n cicd-staging 
```

- Deploy the OpenShift Pipeline from Git repository

```
oc new-app https://github.com/williamcaban/podcicd.git -n cicd
```

- After about 10 minutes the Jenkins Master should be ready on the `cicd` Project.
- Start a new pipeline build (either from GUI or CLI)
  - From GUI:

    `Application Console` > Project `cicd` > Builds > Pipelines --> click the `Start Pipeline` button

  - From CLI:

    `oc start-build podcicd -n cicd`

- Monitor Jenkins logs and progress from the GUI at `Application Console` > Project `cicd` > Builds > Pipelines

- Note: If you receive an error similar to the following when accessing the jenkins route:

```
{"error":"server_error","error_description":"The authorization server encountered an unexpected condition that prevented it from fulfilling the request.","state":"N2UwMTkwNzktYjBmNi00"}
```
Execute the following command and retry:

```
oc annotate sa jenkins serviceaccounts.openshift.io/oauth-redirectreference.jenkins='{"kind":"OAuthRedirectReference","apiVersion":"v1","reference":{"kind":"Route","name":"jenkins"}}' -n cicd
```

# Cleaning the environment
To clean/uninstall the demo delete the projects 

```
oc delete project cicd-prod
oc delete project cicd-staging
oc delete project cicd-dev
oc delete project cicd
```

# Customizing the Demo App

The demo app included in this repo is quite simple, it returns the Pod name Build ID and the Project. If you like to further customize the demo app, this is how to test/run the app in a local computer:

```
python3 -m venv .

source bin/activate

pip3 install -r myapp/requirements.txt

```

Running local instance
```
$ python3 myapp/app.py
* Serving Flask app "app" (lazy loading)
* Environment: development
* Debug mode: on
* Running on http://0.0.0.0:8080/ (Press CTRL+C to quit)
* Restarting with stat
* Debugger is active!
* Debugger PIN: 123-456-789
```

To run a manual pylint test
```
$ pylint app.py
************* Module app
app.py:6:0: C0103: Constant name "app" doesn't conform to UPPER_CASE naming style (invalid-name)

------------------------------------------------------------------
Your code has been rated at 9.47/10 (previous run: 9.47/10, +0.00)
```
