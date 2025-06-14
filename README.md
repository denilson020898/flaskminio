# flaskminio

docker exec -it minio sh

mc alias set myminio http://localhost:9000 minio minio123
mc admin config set myminio notify_webhook:1 endpoint="http://flaskapp:5000/minio-event" queue_limit="10"
mc admin service restart myminio

mc rb --force myminio/mybucket
mc mb --with-versioning myminio/mybucket/proformainput
mc event add myminio/mybucket arn:minio:sqs::1:webhook --event put --prefix proformainput

mc event list myminio/mybucket
mc event list myminio/mybucket arn:minio:sqs::1:webhook --json
