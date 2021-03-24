# jenkins1
1) install docker https://docs.docker.com/engine/install/ubuntu/
2)install and run jenkins
docker pull jenkins/jenkins:lts
docker run -d -p 8080:8080 --volume jenkins-docker-cert:/cert/client --volume jenkins-data:/var/jenkins_home --name=jenkins  jenkins/jenkins:lts
docker logs --tail 50 -f jenkins
3)Finish installation
go to http://34.121.42.64:8080/
and use code as a pw (eg fd1af3f76e244f958c221ea1ffb9a82d)
install suggested plugins and go through the installation to the finish
4)Fix time zone
dpkg-reconfigure tzdata
choose Europe/Moscow
5)Install plugins
Copy Artifact
Deploy to container
6)Add maven
add maven here http://34.66.113.156:8080/configureTools/
7)Download tomcat
wget https://apache-mirror.rbc.ru/pub/apache/tomcat/tomcat-9/v9.0.44/bin/apache-tomcat-9.0.44.zip
8)Install required soft
apt update && apt install default-jdk
apt install unzip
unzip apache-tomcat-9.0.44.zip
mkdir tomcat_dev
cd apache-tomcat-9.0.44
cp -r * /home/elena.razgulayeva/tomcat_dev
cd ..
cd tomcat_dev
9)Configure tomcat
cd conf
nano server.xml (change connector port to 8090(should be previously opened with firewall))
nano tomcat-users.xml
add to tomcat-users
<role rolename="manager-script"/>
<role rolename="admin-gui"/>
<user username="tomcat" password="tomcat" roles="manager-script, admin-gui"/>
10)Restart tomcat
cd ..
cd bin
chmod +x ./*
./startup.sh
11)
check tomcat works http://34.121.42.64:8090/
12)Prepare Jenkins jobs
Build artifact (homework_job)
Install maven to jenkins (Global Tool Configuration->Maven->set name and let it be installed automatically)
Create freestyle project
Repository url https://github.com/elenarazguliaeva/java-tomcat-maven-example.git
Source code git
Build trigger:
build periodically and poll scm
Check branch (master in this homework)
Build:
put mvn name to Maven Version
put clean install to Goals
Post-Build Actions  
Archive the artifact (**/*.war)
Build another project(our build artifact job - tomcat_hw_job)

Deploy jobs (tomcat_hw_job)
Create freestyle project
Build:
Copy artifact from another project 
Project name - put name of job building artifact
Artifact to copy - **/*.war
Post-Build Actions:
Deploy war/ear to container
war/ear files - **/*.war
add tomcat with creds
