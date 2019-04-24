## Sample Multi Project CI/CD Pipeline

Setup the OpenShift environment with the following Projects:

```
oc new-project cicd --description="CI/CD Pipeline Demo"
oc new-project cicd-dev --description="CI/CD - Dev"
oc new-project cicd-prod --description="CI/CD - Prod"
oc new-project cicd-staging --description="CI/CD - Staging"
```

Give `jenkins` Service Account `edit` access to the other Projects
```
oc policy add-role-to-user edit system:serviceaccount:cicd:jenkins -n cicd-dev
oc policy add-role-to-user edit system:serviceaccount:cicd:jenkins -n cicd-prod
oc policy add-role-to-user edit system:serviceaccount:cicd:jenkins -n cicd-staging 
```


## Testing the app in local computer

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