# DevOps Compose [![CircleCI](https://circleci.com/gh/int128/devops-compose.svg?style=shield)](https://circleci.com/gh/int128/devops-compose)

A compose of Docker containers:

* JIRA software
* Confluence
* GitBucket
* Jenkins
* Artifactory
* SonarQube
* Mattermost
* ownCloud
* PostgreSQL
* LDAP

## How to Use

This is designed to work behind SSL termination such as AWS ELB.

Run containers on Docker Compose.
This may take a few minutes.

```sh
echo 'REVERSE_PROXY_DOMAIN_NAME=example.com' >> .env
docker-compose build
docker-compose up -d
```

We can provision an EC2 instance with [the Ansible playbook](playbook-docker-compose.yml).

```sh
ssh-add ~/.ssh/aws.pem
ansible-playbook -i hosts playbook-docker-compose.yml
```

### Setup JIRA

Open JIRA and configure the database connection.

- Database server: `db`
- Type: PostgreSQL
- Database name: `jira`
- User: `jira`
- Password: `jira`

Configure LDAP authentication and create a user.

- LDAP server: `ldap`
- Admin DN: `cn=admin,dc=example,dc=com` with `admin`
- Base DN: `dc=example,dc=com`
- User attribute: `cn`
- Name attribute: `displayName`
- Mail attribute: `mail`

### Setup Confluence

Open Confluence and configure the database connection.

- Database server: `db`
- Type: PostgreSQL
- Database name: `confluence`
- User: `confluence`
- Password: `confluence`

Configure an application link to JIRA.

### Setup Jenkins

Get the initial admin password by following command:

```sh
docker exec devopscompose_jenkins_1 cat /var/jenkins_home/secrets/initialAdminPassword
```

Open Jenkins and configure LDAP authentication.

- LDAP server: `ldap`
- Admin DN: `cn=admin,dc=example,dc=com` with `admin`
- Root DN: `dc=example,dc=com`
- Base DN: (empty)
- User attribute: `cn={0}`
- Name attribute: `displayname` (default)
- Mail attribute: `mail` (default)

### Setup GitBucket

Open GitBucket and configure LDAP authentication.

- LDAP server: `ldap`
- Admin DN: `cn=admin,dc=example,dc=com` with `admin`
- Base DN: `dc=example,dc=com`
- User attribute: `cn`
- Name attribute: `displayname`
- Mail attribute: `mail`

### Setup Artifactory

Open Artifactory and configure LDAP authentication.

- LDAP server: `ldap://ldap/dc=example,dc=com`
- User DN: `cn={0}`
- Mail attribute: `mail`
- Search filter: `(*)`
- Search base: `dc=example,dc=com`
- Manage DN: `cn=admin,dc=example,dc=com` with `admin`

### Setup SonarQube

SonarQube does not support LDAP authentication.

### Setup Mattermost

Mattermost (Community Edition) does not support LDAP authentication.
Configure a mail service such as AWS SES and use the email sign up.

### ownCloud

Open ownCloud and configure LDAP authentication.

- LDAP server: `ldap:389`
- Admin DN: `cn=admin,dc=example,dc=com` with `admin`
- Base DN: `dc=example,dc=com`

### Register init script

We provide the init script for LSB.
Register as follows:

```sh
sudo ln -s /opt/devops-compose/init-lsb.sh /etc/init.d/devops-compose
sudo chkconfig --add devops-compose
```

## Backup and Restore

It may be best to backup and restore volumes under `/var/lib/docker/volumes`.

## Contribution

This is an open source software licensed under Apache-2.0.
Feel free to open issues or pull requests.
