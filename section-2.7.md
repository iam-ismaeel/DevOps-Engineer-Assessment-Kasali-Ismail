Question
Write Prometheus exporter in Python/Golang that connects to specified RabbitMQ
HTTP API (it's in the management plugin) and periodically reads the following
information about all queues in all vhosts:
● messages (total count of messages)
● messages_ready
● messages_unacknowledged
It should then export 3 new metrics:
● rabbitmq_individual_queue_messages{host,vhost,name}
● rabbitmq_individual_queue_messages_ready{host,vhost,name
}
● rabbitmq_individual_queue_messages_unacknowledged{host,v
host,name}
where the host is RabbitMQ hostname, vhost is RabbitMQ vhost
and name is name of the queue.
It should use RABBITMQ_HOST, RABBITMQ_USER, and
RABBITMQ_PASSWORD environment variables to run multiple
deployments of this and just change the env in them.


I do not have any experience with Message brokers.
