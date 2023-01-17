# CI/CD Template for Mulesoft Projects

This project uses:
 - Github as Source Code Management (SCM)
 - Jenkins as Automation Server
 - Nexus as Artifact Repository
 - MUnit (Unit Tests)
 - POSTMAN Newman (Integration Test)

![Components](/docs/components.JPG "Components")

## Continuous Integration Pipeline (Development)

![Overview-CI](/docs/overview.JPG "Overview-CI")

For more details download [CI Flow.pptx](docs/CI%20CD%20Template.pptx)

<br><br>

## Continuous Deployment Pipeline (QA, Production)

![Overview-CD](/docs/overview-CD.JPG "Overview-CD")

For more details download [CD Flow.pptx](docs/CD%20Template.pptx)

<br><br>

## INDEX

1. [ Anypoint Platform setup. ](#anypoint)
2. [ Github setup. ](#github)
3. [ Nexus setup. ](#nexus)
4. [ Jenkins setup. ](#jenkins)
5. [ Maven Security. ](#mavensec)
6. [ Mule Maven. ](#maven)
7. [ CI - Continuous Integration. ](#ci)
8. [ CD - Continuous Deployment. ](#cd)

<br><br>

<a name="anypoint"></a>
## 01 - Anypoint Platform Setup

 1. **Connected APPs for CI/CD Pipelines in Jenkins:**

| Connected App | Description |
| ------ | ------ |
| Jenkins Sandbox | Jenkins needs a Connected App to Deploy Mule Apps to CloudHub | 
| Jenkins Production | Jenkins needs a Connected App to Deploy Mule Apps to CloudHub | 

Permissions:
  - Runtime Manager: Create Applications
  - Runtime Manager: Manage Settings
  - Runtime Manager: Read Applications
  - General: Profile
  - General: View Environment

[How to use Connected Apps with CI/CD](https://help.mulesoft.com/s/article/How-to-use-Connected-Apps-with-CI-CD)

<br><br>

<a name="github"></a>
## 02 - Github setup

 1. **User for CI Pipeline in Jenkins Sandbox:**

| User | Description |
| ------ | ------ |
| Jenkins Sandbox (CI) | Jenkins needs a technical user to Check-out and commit Code (CI Pipeline) | 

Permissions:
  - Commit statuses - Read and Write
  - Contents: Read & write (to read the Jenkinsfile and the repository content during git fetchand update the repository such as tagging releases)
  - Metadata: Read-only
  - Pull requests: Read-only
  - Under Subscribe to events, enable all events.

<br><br>

 2. **User for CD Pipeline in Jenkins Sandbox:**

| User | Description |
| ------ | ------ |
| Jenkins Sandbox (CD) | Jenkins needs a technical user to Check-out Code (CD Pipeline) | 

Permissions:
  - Commit statuses - Read
  - Contents: Read-only (to read the Jenkinsfile and the repository content during git fetch).
  - Metadata: Read-only
  - Pull requests: Read-only
  - Under Subscribe to events, enable all events.

<br><br>

 3. **User for CD Pipeline in Jenkins Production:**

| User | Description |
| ------ | ------ |
| Jenkins Production (CD) | Jenkins needs a technical user to Check-out Code (CD Pipeline) | 

Permissions:
  - Commit statuses - Read
  - Contents: Read-only (to read the Jenkinsfile and the repository content during git fetch).
  - Metadata: Read-only
  - Pull requests: Read-only
  - Under Subscribe to events, enable all events.

<br><br>

 4. **Personal Access Tokens for Developers:**

| User | Description |
| ------ | ------ |
| Personal Access Token | Each Developer must be able to connect to SCM using Maven. PAT must be used. | 

 - [Creating a personal access token](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token)
  
<br><br>

 5. **SSH Keys for Jenkins:**

| User | Description |
| ------ | ------ |
| Jenkins Sandbox | Jenkins needs SSH Key to Check-out Code | 
| Jenkins Production | Jenkins needs a SSH Key to Check-out Code |

  - [Connecting to GitHub with SSH](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh)
  - [SSH authentication between GitHub and Jenkins](https://medium.com/appgambit/ssh-authentication-between-github-and-jenkins-d873dd138db0)

<br><br>

<a name="nexus"></a>
## 03 - Nexus setup

 1. **Create the users as below:**

| User | Description | Permissions |
| ------ | ------ | ------ |
| Developers | Each Mule Developer must be able to deploy artifacts and download artifacts locally (Anypoint Studio) | Read and Write SNAPSHOTS Repo / Read RELEASES Repo |
| Jenkins Sandbox (CI) | Jenkins needs a technical user to deploy artifacts and download artifacts (CI Pipeline) | Read and Write RELEASES Repo |
| Jenkins Sandbox (CD) | Jenkins needs a technical user to download artifacts (CD Pipeline) | Read RELEASES Repo |
| Jenkins Production (CD) | Jenkins needs a technical user to download artifacts (CD Pipeline) | Read RELEASES Repo |

<br><br>

<a name="jenkins"></a>
## 04 - Jenkins Setup

 1. **Credentials:**

 - Anypoint Platform Connected APP ID and Secret:

<br>

![Connected APP](/docs/con-app.JPG "Connected APP")

<br><br>

 - Github SSH Keys:
   - [Connecting to GitHub with SSH](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh)
   - [SSH authentication between GitHub and Jenkins](https://medium.com/appgambit/ssh-authentication-between-github-and-jenkins-d873dd138db0)


<br><br>

2. **Software to be installed:**

| SW | Description | Documentation |
| ------ | ------ | ------ |
| Java JDK | Maven needs JDK | https://www.oracle.com/java/technologies/javase-downloads.html |
| Apache Maven | Integration to Mulesoft using Mule Maven plugin | http://maven.apache.org/ |
| Node.js | Run Tests using Newman, that needs Node | https://nodejs.org/en/download/ |
| NPM | Run Tests using Newman, that needs NPM | https://docs.npmjs.com/downloading-and-installing-node-js-and-npm |
| Newman | Run Tests using Newman | https://www.npmjs.com/package/newman |
| HTML Reporter Extra | Run Tests using Newman, and show reports in HTML | https://www.npmjs.com/package/newman-reporter-htmlextra |

<br><br>

3. **Plugins to be installed:**

| Plugin | Description | Documentation |
| ------ | ------ | ------ |
| Extended Choice Parameter | Build Pipeline with Advanced Parameters | [plugins.jenkins.io/extended-choice-parameter/][PlEcp] |
| Pipeline Utility Steps | Read pom.xml properties easily | [plugins.jenkins.io/pipeline-utility-steps/][PlPus] |
| HTML Publisher | Show Test Reports in HTML | [plugins.jenkins.io/htmlpublisher/][PlHp] | 

<br><br>

4. **Pipelines:**

<br>

   - Continuous Integration - Development:
<br>

   ![CI](/docs/pipeline-ci.jpg "CI")

<br><br>

   - Continuous Deployment - Production:
<br>

   ![CD](/docs/pipeline-cd.jpg "CD")

<br><br>

<a name="mavensec"></a>
## 05 - Maven Security Encryption

 - [Maven Password Encryption](https://maven.apache.org/guides/mini/guide-encryption.html)
 - [How to define encrypted passwords in the Mule Maven Plugin](https://help.mulesoft.com/s/article/How-to-define-encrypted-passwords-in-the-Mule-Maven-Plugin)
 - [Mule Maven Encrypt Credentials](https://docs.mulesoft.com/mule-runtime/4.3/deploy-to-rtf#encrypt-credentials)

<br><br>

<a name="maven"></a>
## 06 - Connecting Mule Project using Maven

1. **Connect to Mule Corporate Nexus:**

**settings.xml**

![Nexus Credentials](/docs/nexus-cred.JPG "Nexus Credentials")

```xml
<profile>
  <id>Mule</id>
  <activation>
    <activeByDefault>true</activeByDefault>
  </activation>
  <repositories>
    <repository>
      <id>MuleRepository</id>
      <name>MuleRepository</name>
      <url>https://repository.mulesoft.org/nexus-ee/content/repositories/releases-ee/</url>
      <layout>default</layout>
      <releases>
        <enabled>true</enabled>
      </releases>
      <snapshots>
        <enabled>true</enabled>
      </snapshots>
    </repository>
  </repositories>
</profile>
```

<br>

  - [How to use Enterprise Maven Repository credentials with settings.xml and pom.xml example](https://help.mulesoft.com/s/article/How-to-use-Enterprise-Maven-Repository-credentials-with-settings-xml-and-pom-xml-example)

<br><br>

2. **Connect to Github using PAT:**

**settings.xml**

| ID | Username | Password |
| ------ | ------ | ------ |
| my-scm-server | github user | github PAT encrypted via Maven |

![SCM Credentials](/docs/scm-set.JPG "SCM Credentials")

<br>

**pom.xml**

```xml
<scm>
  <connection>scm:git:https://github.com/Timestamp-ITS/ci-cd-template.git</connection>
  <developerConnection>scm:git:https://github.com/Timestamp-ITS/ci-cd-template.git</developerConnection>
  <url>git://github.com/Timestamp-ITS/ci-cd-template.git</url>
</scm>
```

<br>

  - [Configuring SCM](https://maven.apache.org/scm/maven-scm-plugin/usage.html)

<br><br>

3. **Connect to Nexus Repository:**

**settings.xml**

| ID | Username | Password |
| ------ | ------ | ------ |
| nexus-releases | nexus user | nexus password encrypted via Maven |
| nexus-snapshots | nexus user | nexus password encrypted via Maven |

![Nexus Credentials](/docs/nexus-set.JPG "Nexus Credentials")

<br>

**pom.xml**

```xml
<repositories>

   <repository>
      <id>nexus-releases</id>
      <url>http://localhost:8099/repository/maven-releases/</url>
   </repository>

</repositories>


<distributionManagement>

   <repository>
      <id>nexus-releases</id>
      <url>http://localhost:8099/repository/maven-releases/</url>
   </repository>

   <snapshotRepository>
      <id>nexus-snapshots</id>
      <url>http://localhost:8099/repository/maven-snapshots/</url>
   </snapshotRepository>

</distributionManagement>
```

<br><br>

4. **Connect to Anypoint Exchange:**

**settings.xml**

![Exchange Credentials](/docs/exch-cred.JPG "Exchange Credentials")

<br>

**pom.xml**

```xml
<repositories>

   <repository>
      <id>anypoint-exchange-v2</id>
      <name>Anypoint Exchange</name>
      <url>https://maven.anypoint.mulesoft.com/api/v2/maven</url>
      <layout>default</layout>
   </repository>

</repositories>
```
<br><br>


<a name="ci"></a>
## 07 - Building the CI Pipeline (Develop)

1. **Build the CI Pipeline with Parameters:**

![CI Pipeline Parameters](/docs/ci-params.JPG "CI Pipeline Parameters")

<br>

2. **Check the result:**

![CI Pipeline Results](/docs/ci-logs.JPG "CI Pipeline Results")

<br>

3. **Check MUnit Test Coverage Report:**

![CI MUnit Report](/docs/ci-munit.JPG "CI MUnit Report")

<br>

![CI MUnit Coverage](/docs/ci-munit2.jpg "CI MUnit Coverage")

<br>

![CI MUnit Coverage Details](/docs/ci-munit3.jpg "CI MUnit Coverage Details")

<br>

4. **Check Integration Test (Newman) Report:**

![CI Newman Report](/docs/ci-newman.jpg "CI Newman Report")

<br>

![CI Newman Report Details](/docs/ci-newman2.jpg "CI Newman Report Details")

<br>

![CI Newman Report Details 2](/docs/ci-newman3.jpg "CI Newman Report Details 2")

<br><br><br>

<a name="cd"></a>
## 08 - Building the CD Pipeline (QA, PreProd, Production)

1. **Build the CD Pipeline with Parameters:**

![CD Pipeline Parameters](/docs/cd-params.JPG "CD Pipeline Parameters")

<br>

2. **Check the result:**

![CD Pipeline Results](/docs/cd-logs.JPG "CD Pipeline Results")

<br>



<br><br><br>

## Automation Overview (DevOps)

![Automation World](/docs/automation.png "Automation World")

<br><br><br>

## TO-DOs

- [ ] Newman Tests different Environments
    - https://learning.postman.com/docs/running-collections/using-newman-cli/integration-with-jenkins/
    - https://learning.postman.com/docs/writing-scripts/script-references/test-examples/
    - npm install -g newman-reporter-htmlextra
    - newman run ./src/test/resources/test-case.postman_collection.json -r htmlextra --reporter-htmlextra-export ./target/newman/report.html
    - https://www.npmjs.com/package/newman-reporter-htmlextra
- [ ] JRE and JDK in Jenkins (see params for Mule Test Coverage)
- [ ] Access Control by Pipeline in Jenkins
- [ ] Parent POM overwriting MMP Properties (cloudhub)
- [ ] Custom settings.xml (global or local with Jenkins Plugin)
- [ ] Permission for Coverage Report in HTML
    - System.setProperty("hudson.model.DirectoryBrowserSupport.CSP","script-src https://cdnjs.cloudflare.com https://cdn.datatables.net http://localhost:8080 'unsafe-inline';")
    - System.setProperty("hudson.model.DirectoryBrowserSupport.CSP","script-src *;")
    - https://book.hacktricks.xyz/pentesting-web/content-security-policy-csp-bypass
- [ ] Scripts for Approval
- [ ] Code Quality using SonarQube (or SonarCloud)
- [ ] API ID as Build Parameter
- [ ] Jenkins and Nexus as Container (Docker Image)
- [ ] Workspace Cleaning
- [ ] Archive MUnit and Newman Reports
- [ ] Mule Application Naming Rules (params and etc)
- [ ] More details about "Jenkinsfile" and "Jenkinsfile-deploy"
- [ ] More details about Newman Scripts


[//]: # (These are reference links used in the body of this note and get stripped out when the markdown processor does its job. There is no need to format nicely because it shouldn't be seen. Thanks SO - http://stackoverflow.com/questions/4823468/store-comments-in-markdown-syntax)

   [PlHp]: <https://plugins.jenkins.io/htmlpublisher/>
   [PlPus]: <https://plugins.jenkins.io/pipeline-utility-steps/>
   [PlEcp]: <	https://plugins.jenkins.io/extended-choice-parameter/>
