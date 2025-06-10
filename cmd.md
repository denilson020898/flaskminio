stolen from https://blog.min.io/minio-webhook-event-notifications/

“test”

We will be working inside of an interactive terminal for the “minio” container, which we can spawn by running a single command:

docker exec -it minio /bin/sh

The reason for running our mc commands from within this shell is because the Docker minio/minio image already has mc installed and ready to go.

Once inside the container’s interactive terminal, the process of configuring MinIO for event notifications using the MinIO Client (mc) involves the following key steps:

Setting Up MinIO Alias: The first step involves creating an alias for your MinIO server using the MinIO Client (mc). This alias is a shortcut to your MinIO server, allowing you to easily execute further mc commands without repeatedly specifying the server’s address and access credentials. This step  simplifies the management of your MinIO server through the client.

mc alias set myminio http://localhost:9000 minio minio123

Adding the Webhook Endpoint to MinIO: Configure a new webhook service endpoint in MinIO. This setup is done using either environment variables or runtime configuration settings, where you define important parameters such as the endpoint URL, an optional authentication token for security, and client certificates for secure connections.

mc admin config set myminio notify_webhook:1 endpoint="http://flaskapp:5000/minio-event" queue_limit="10"

Restarting the MinIO Deployment: Once you have configured the settings, restart your MinIO deployment to ensure the changes take effect.

mc admin service restart myminio

Expect:
Restart command successfully sent to myminio. Type Ctrl-C to quit or wait to follow the status of the restart process....Restarted myminio successfully in 1 seconds

Configuring Bucket Notifications: The next step involves using the mc event add command. This command is used to add new bucket notification events, setting the newly configured Webhook service as the target for these notifications.

# prefix?
mc event add myminio/mybucket arn:minio:sqs::1:webhook --event put,get,delete

Expect:
Successfully added arn:minio:sqs::1:webhook

List Bucket Subscribed Events: Run this command to list the event assigned to myminio/mybucket:

<!-- minio mc event list myminio/mybucket -->
mc event list myminio/mybucket

Expect:
arn:minio:sqs::1:webhook   s3:ObjectCreated:*,s3:ObjectAccessed:*,s3:ObjectRemoved:*   Filter:

List Bucket Assigned Events (in JSON): Run this command to list the event assigned to myminio/mybucket in JSON format: 

<!-- minio mc event list myminio/mybucket arn:minio:sqs::1:webhook --json -->
mc event list myminio/mybucket arn:minio:sqs::1:webhook --json

Expect: 
{ "status": "success", "id": "", "event": ["s3:ObjectCreated:","s3:ObjectAccessed:", "s3:ObjectRemoved:*"], "prefix": "", "suffix": "", "arn": "arn:minio:sqs::1:webhook"}
