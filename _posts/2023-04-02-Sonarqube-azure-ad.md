---
layout: post
subtitle: Setting up Azure Ad for Sonarqube
title: Sonarqube and Microsoft entra
date: 2024-04-02
---

In my work we use sonarqube as static code analysis tool that scans our codebase in PR branch and also master branch as a pipeline task.

But if we want to login to sonar qube we have to use shared credentials whihc sucks.

We use the username/password whihc is stored in 1pass.

Sonarqube does have the feature to configure to use azure as SAML provider.

So today I am going to trying to setup in my local machine and see how easy it is.

### Is is easy to setup Sonarqube in my home??

yes by using docker.
I am using the docker compose file to quickly spin up the containers and run the sonarqube.

I had to run `sysctl -w vm.max_map_count=524288` before the docker compose command.
Sonarqube needs atleast 262,144 memoery map areas.

```
version: '3'

services:

  postgresql:
    image: postgres:13
    container_name: sonarqube-postgresql
    environment:
      - POSTGRES_USER=sonar
      - POSTGRES_PASSWORD=admin123
    volumes:
      - postgresql:/var/lib/postgresql/data

  sonarqube:
    image: sonarqube:9.3-community
    container_name: sonarqube
    depends_on:
      - postgresql
    network_mode: host
    environment:
      - SONARQUBE_JDBC_USERNAME=sonar
      - SONARQUBE_JDBC_PASSWORD=admin123
      - SONARQUBE_JDBC_URL=jdbc:postgresql://localhost:5432/sonar
    volumes:
      - sonarqube-data:/opt/sonarqube/data
      - sonarqube-logs:/opt/sonarqube/logs
      - sonarqube-extensions:/opt/sonarqube/extensions

volumes:
  postgresql:
  sonarqube-data:
  sonarqube-logs:
  sonarqube-extensions:
```

You should have sonarqube running without any issues. That took me 5 mins lol :)

#### Azure AD setup

1. Created an enterprise application in Azure ad

Now we have to add SAML configuration

![Azure AD SAML](/assets/images/azure-ad-saml-config.png)

We have to user `https` for the repoly url. So I have to use ngork to expose locally running somarqube to internet.

Now we have to copy values from azure to sonarqube.

| Sonarqube | Azure AD |
| --------- | -------- |
|Application id| Identifier|
|Provider ID| Microsoft Entra Identifier|
|SAML login url | Login URL|
|SAML user login attribute| Additional claim- mail|
| SAML user name attribute| Additional claim- name|
|Identity provider certificate|Certificate (Base64)|

Now its almost ready to use Azure AD as SAML login.
In general, set the server base url as the default sonarqube url whihc we used to set in the azure.

We can test our config by the Test Configuration in the SAML screen and we should see the below result
![SAML Config test](/assets/images/authtest.png)

Tada, now we can login using azure SSO

![SSO](/assets/images/sso.png)

Thats it for today, See ya!!

