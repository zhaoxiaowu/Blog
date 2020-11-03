1. cd /home/rcs/newrcsadmin/

2. ll

   ![image-20200410102807190](..\images\image-20200410102807190.png)

3. rm rcsadmin-0.0.1-SNAPSHOT.jar
4. rz 上传
5. ps -ef|grep java 找到rcsadmin 的 pid
6. kill -9 pid 
7. nohup java -jar rcsadmin-0.0.1-SNAPSHOT.jar &
8. cd logs 查看日志