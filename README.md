# docker-mysql-replication
# 这是docker 单机版MySQL Replicaiton配置(注意是外挂了配置的MySQL但实例)

一、Master节点:
	git clone https://github.com/banna2019/docker-mysql-replication.git

	mv docker-mysql-replication /data

	chmod -R 777 /data
	chmod -R 644 /data/master/conf/mysql.conf.cnf		//挂载的配置文件不能拥有太高的读写权限,不然docker 容器启动的时候会,跳过指定挂载的配置文件
	cd /data/master 

	docker-compose up -d 
	docker ps -a 
	docker logs --tail 100 -f  contailer_name 

	创建mysql授权用户(在master上执行):
		grant replication slave on *.* to 'repl'@'192.168.2.%' identified by 'repl123';
		flush privileges;
		show master status;



二、Slave节点:
	git clone https://github.com/banna2019/docker-mysql-replication.git
	
	mv docker-mysql-replication /data
	
	chmod -R 777 /data
	chmod -R 644 /data/slave/conf/mysql.conf.cnf	//挂载的配置文件不能拥有太高的读写权限,不然docker 容器启动的时候会,跳过指定挂载的配置文件
	
	docker-compose up -d 
	docker ps -a 
	docker logs --tail 100 -f  contailer_name
	
	MySQL从节点上change Master服务器,开启slave获取binlog信息
		CHANGE MASTER TO MASTER_HOST='192.168.2.178',MASTER_USER='repl',MASTER_PASSWORD='repl123',MASTER_PORT=3306,MASTER_AUTO_POSITION=1;
		flush privileges;
		start slave;
		show slave status\G;
