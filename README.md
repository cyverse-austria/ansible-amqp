# CyVerse DS AMQP Playbooks

This is a collection of playbooks for deploying a AMQP broker for the CyVerse Data Store.

This is original taken from [ds-deploy](https://gitlab.cyverse.org/tugraz/ds-deploy).

## Role Variables

| Variable               | Choices/Defaults | Comments |
|:---------------------- | ----------------:|:-------- |
| rabbitmq__version      | "3.10.8-1.1"     | To change this default other apt-repos have to be enabled |
| rabbitmq__broker_port  | 5672             | The port used by the broker |
| rabbitmq__management_port | 15672         | The port used by the management interface |
| rabbitmq__user.name    | "cyverse"        | The AMQP broker admin user |
| rabbitmq__user.pass    | "supersecret"    | Change on command-line (in pipeline) |
| rabbitmq__vhosts       | "/cyverse/de"<br/>"/cyverse/data-store"            | List of vhosts |


## example playbook

### minimal example

```yaml
- hosts: all
  roles:
    - role: rabbitmq
```

### full feature example

```yaml
- hosts: all
  roles:
    - role: rabbitmq
      rabbitmq__broker_port: 1234
      rabbitmq__management_port: 12345
      rabbitmq__vhosts:
        - "/mynew/first"
        - "/mynew/second"
      rabbitmq__user:
        name: "thatsme"
        pass: "&thisismy1pass"
```
## run the playbook

```bash
ansible-playbook --inventory <inventory-file/dir>  --user root site.yml
```

## check status of rabbitmq
```bash
# access the node
ssh root@AMQP_HOST

# check amqp status
systemctl status rabbitmq-server

# List of all vhosts
curl -i -u ${rabbitmq__user.name}:${rabbitmq__user.pass} http://localhost:15672/api/vhosts | <json_pp | jq .>

# list users
rabbitmqctl list_users

# list vhosts
rabbitmqctl list_vhosts

# create vhost
rabbitmqctl add_vhost /cyverse/de
rabbitmqctl add_vhost /cyverse/data-store

# add new user
# rabbitmqctl add_user <username> <password>
rabbitmqctl add_user cyverse password

# Granting Permissions to a User
# rabbitmqctl set_permissions -p "custom-vhost" "username" ".*" ".*" ".*"
rabbitmqctl set_permissions -p "/cyverse/de" "cyverse" ".*" ".*" ".*"

# make user a administrator
# rabbitmqctl set_user_tags username administrator
rabbitmqctl set_user_tags cyverse administrator

# start rabbitmq
rabbitmqctl start_app

# list exchanges
rabbitmqctl list_exchanges

# list exchanges for /cyverse
rabbitmqctl list_exchanges --vhost /cyverse

# list vhosts
rabbitmqctl list_vhosts

# Connect To RabbitMQ From A Different Machine
# rabbitmqadmin -H SERVER-IP -u USER_NAME -p PASSWORD list vhosts
rabbitmqadmin -H AMQP_HOST -u cyverse -p PASSWORD list vhosts

```


**The institution and hosting server firewalls need to allow the following communication.**

* `1247-1248/tcp` Need to be open for inbound and outbound connections from iRODS server and iRODS resource server.

* `20000-20199/tcp,udp` Need to be open for inbound connections from all the **Condor** and **Kubernetes** worker nodes to allow the Discovery Environment to access data using the web interface and while doing analyses.
