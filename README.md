# Calculator test project

The aim of this project is to provide a project where tests can be run and reported in the command line.

# Tests

This is a basic calculator class with two very basic methods.

The tests have been written in both JUnit5 and JGiven.

## Setting JAVA_HOME

You will need to ensure JAVA_HOME is set correctly.

## Gradle & test

Simply running `gradle test` in the command line will run the tests and `gradle test -i` will give more verbose output.

There is no need to install gradle as everything is packaged within this project.

Test output can be found in:

**Build folder**

You will find an HTML report in the folder:

`build/reports/index.html`

you will also find `XML` result output in the folder:

`build/test-results/test/TEST-CalculatorUnitTests.xml`

`build/test-results/test/TEST-CalculatorJgiven.xml`

**JGiven reports**

you will find a JSON report for JGiven output:

`jgiven-reports/TestCalculatorJgiven.json`

# Creating a CICD Pipeline Using Jenkins

## GitHub Setup
- Create new repository with the application
- Initialise local repository in the project directory
```
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:ConorSM/calculator_test_cicd_pipeline.git
git push -u origin main
```

- Add a "dev" branch to the repository
- Add "dev branch to local repository
```
git checkout -b dev
git add .
git commit -m "first commit"
git push -u origin dev
```
- Generate SSH key
```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
- add public key to the specific repository in the settings
- Create Webhook
  - Go to settings of repository and select webhooks
  - Enter Jenkins url and add "/github-webhook/" to the end

## Jenkins Setup

### CI Job
- Create new item
- Create name and select "freestyle project"
- Add description
- Check "Discard old builds"
  - keep a max of 3 builds
- Check "GitHub project"
  - Add the https url of github repository with the project
- Keep Office 365 Connector as default
- Under Source Code Management select "Git"
  - Enter ssh url of repository
  - Create credentials using ssh key (add the private key)
- Branches to build - enter */dev 
- Under Build Triggers select "GitHub hook trigger for GITScm polling"
- Keep Build Environment as default
- Under Build select Execute shell
  - add ls and pwd
- Select Invoke Gradle script and select "Use Gradle Wrapper"
  - Make gradlew executable
  - For "Tasks" enter "test"
- Post-build Actions
  - after merge job is complete add it to the "build other projects" and select Trigger only if build is stable

### Merge job
- Create new item as before
- Keep max of 3 builds
- Enter https url of github repository
- add ssh link to source code management 
  - branches to build - */dev
  - Additional behaviours - Merge before build
    - name of repository - origin
    - Branch to merge to - main (or master)
    - Merge strategy - default
    - Fast-forward mode --ff
- Post-build Actions
  - Merge Results
  - Editable Email Notificaton
    - Enter email address in project recipient list under "$DEFAULT_RECIPIENTS"

### Docker job
- max 3 builds
- http of github repository
- ssh to source code management
- Branch specifier - */main
- Build environment
  - Delete workspace before build starts
- Execute shell
```
sudo docker build . -t conorsm/calc_jenkins
```
- Add to docker build and publish to build section
  - name of repository
- Docker Host URI
```
unix:///var/run/docker.sock
```
- Registry credentials
  - token from dockerhub as password

test