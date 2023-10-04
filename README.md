# zabbix

podman pod create --name zabbix -p 80:8080 -p 10051:10051

podman login registry.connect.redhat.com

adds "net.ipv4.ip_unprivileged_port_start=80" >> /etc/sysctl.conf

sudo sysctl -p

podman run --name zabbix-agent -e ZBX_SERVER_HOST="127.0.0.1,localhost" --restart=always --pod=zabbix -d registry.connect.redhat.com/zabbix/zabbix-agent-64:latest

mkdir mysql

podman run --name mysql-server -t -e MYSQL_DATABASE="zabbix" -e MYSQL_USER="zabbix" -e MYSQL_PASSWORD="zabbix_pwd" -e MYSQL_ROOT_PASSWORD="root_pwd" -v ./mysql/:/var/lib/mysql/:Z --restart=always --pod=zabbix -d mysql:8.0 --character-set-server=utf8 --collation-server=utf8_bin --default-authentication-plugin=mysql_native_password

podman run --name zabbix-server-mysql -t -e DB_SERVER_HOST="127.0.0.1" -e MYSQL_DATABASE="zabbix" -e MYSQL_USER="zabbix" -e MYSQL_PASSWORD="zabbix_pwd" -e MYSQL_ROOT_PASSWORD="root_pwd" -e ZBX_JAVAGATEWAY="127.0.0.1" --restart=always --pod=zabbix  -d registry.connect.redhat.com/zabbix/zabbix-server-mysql-64

podman run --name zabbix-java-gateway -t --restart=always --pod=zabbix -d registry.connect.redhat.com/zabbix/zabbix-java-gateway-64

podman run --name zabbix-web-mysql -t -e ZBX_SERVER_HOST="127.0.0.1" -e DB_SERVER_HOST="127.0.0.1" -e MYSQL_DATABASE="zabbix" -e MYSQL_USER="zabbix" -e MYSQL_PASSWORD="zabbix_pwd" -e MYSQL_ROOT_PASSWORD="root_pwd" --restart=always --pod=zabbix -d registry.connect.redhat.com/zabbix/zabbix-web-mysql-64

podman pod stop zabbix

podman pod ps --ctr-status

