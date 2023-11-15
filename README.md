# CyVerse DS AMQP Playbooks

This is a collection of playbooks for deploying a AMQP broker for the CyVerse Data Store.

This is original taken from [ds-deploy](https://gitlab.cyverse.org/tugraz/ds-deploy).

## Variables

None of these variables are required.

Variable               | Default | Choices | Comments
---------------------- | ------- | ------- | --------
`amqp_admin_username`  | guest   |         | The AMQP broker admin user
`amqp_admin_password`  | guest   |         | The password for `amqp_admin_username`
`amqo_broker_port`     | 5672    |         | The port used by the broker
`amqp_management_port` | 15672   |         | The port used by the management interface


## run playbook
```bash
ansible-playbook -i inventory/ main.yml --user root
```

## check status of rabbitmq
```bash
# access the node
ssh root@AMQP_HOST

# check amqp status
systemctl status rabbitmq-server

# List of all vhosts
curl -u guest:guest -X PUT http://AMQP_HOST:15672/api/vhosts | json_pp

# list users
rabbitmqctl list_users

# list vhosts
rabbitmqctl list_vhosts

# TODO: via ansible
# create vhost
rabbitmqctl add_vhost /cyverse/de
rabbitmqctl add_vhost /cyverse/data-store

# TODO: via ansible
# add new user
# rabbitmqctl add_user <username> <password>
rabbitmqctl add_user cyverse password

# TODO: via ansible
# Granting Permissions to a User
# rabbitmqctl set_permissions -p "custom-vhost" "username" ".*" ".*" ".*"
rabbitmqctl set_permissions -p "/cyverse/de" "cyverse" ".*" ".*" ".*"

# TODO: via ansible
# make user a administrator
# rabbitmqctl set_user_tags username administrator
rabbitmqctl set_user_tags cyverse administrator

# TODO: via ansible
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

