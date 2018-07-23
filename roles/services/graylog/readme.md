

### Vars
graylog_user_username
graylog_user_email


`pwgen -N 1 -s 96`
graylog_config_password_secret


`echo -n yourpassword | shasum -a 256`
graylog_config_root_password_sha2


### Reset Docker

sudo docker rm -f graylog mongo graylog_elasticsearch ; \
sudo docker volume rm -f mongo_data graylog_config graylog_data graylog_plugins graylog_elasticsearch_data



