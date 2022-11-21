aws server login details
-------
username:surya3cs@gmail.com
password:Surya@3399

Go to Task Server instance in aws ec2 console 
--------
This instance is ubuntu and i have already installed docker and jenkins there


  
  
 First Task:
 -----------
  
  i took the node js application , the application is running on docker container and node js app is to connect mysql data base using docker
  
  Connect to MYsql
  ----------------
  for connecting the database mysql , i wrote index.js file given below
  
  The index.js file will look like as below:
-----------------
var mysql = require('mysql');
var express = require('express');

var app = express();
var port = process.env.PORT || 8005;
var responseStr = "MySQL Data:";

app.get('/',function(req,res){
   
   var mysqlHost = process.env.MYSQL_HOST || 'localhost';
   var mysqlPort = process.env.MYSQL_PORT || '3306';
   var mysqlUser = process.env.MYSQL_USER || 'root';
   var mysqlPass = process.env.MYSQL_PASS || 'root';
   var mysqlDB   = process.env.MYSQL_DB   || 'node_db';

   var connectionOptions = {
     host: mysqlHost,
     port: mysqlPort,
     user: mysqlUser,
     password: mysqlPass,
     database: mysqlDB
   };

   console.log('MySQL Connection config:');
   console.log(connectionOptions);

   var connection = mysql.createConnection(connectionOptions);
   var queryStr = SELECT * FROM MOE_ITEM_T;
   
   connection.connect();
 
   connection.query(queryStr, function (error, results, fields) {
     if (error) throw error;
     
     responseStr = '';

     results.forEach(function(data){
        responseStr += data.ITEM_NAME + ' : ';
        console.log(data);
     });

     if(responseStr.length == 0)
        responseStr = 'No records found';

     console.log(responseStr);

     res.status(200).send(responseStr);
   });
    
   connection.end();
});


app.listen(port, function(){
    console.log('Sample mySQL app listening on port ' + port);
});


Here with this very simple example i am connecting to the MySQL database. i am  using environment variables to pass the details here. 


var mysqlHost = process.env.MYSQL_HOST || 'localhost';
var mysqlPort = process.env.MYSQL_PORT || '3306';
var mysqlUser = process.env.MYSQL_USER || 'root';
var mysqlPass = process.env.MYSQL_PASS || 'root';
var mysqlDB   = process.env.MYSQL_DB   || 'node_db';
It connects to the database and execute an SELECT SQL query and finally returns the values of the ITEM_NAME field from table MOE_ITEM_T. 


Using Docker
----------

To spin up the nodejs,mysql,phpmyadmin docker containers by using docker-compose. docker-compose file given below
version: "3.2"
services:
  nodejs:
    build: 
      context: .
    image: suryabezawada/nodejsapp
    
    networks:
      - frontend
      - backend
    environment:
      - MYSQL_HOST=moe-mysql-app
      - MYSQL_USER=moeuser
      - MYSQL_PASS=moepass
      - MYSQL_DB=moe_db
    container_name: moe-nodejs-app
    volumes:
      - ./www/:/var/www/html/
    ports:
      - "30001:8005"
    
  mysql:
    image: mysql:5.7
    networks:
      - backend
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_USER=moeuser
      - MYSQL_PASSWORD=moepass 
      - MYSQL_DATABASE=moe_db
    container_name: moe-mysql-app
  phpmyadmin:
    image: phpmyadmin/phpmyadmin:4.7
    depends_on:
      - mysql
    networks:
      - backend
    ports:
      - "30002:80"
    environment:
      - PMA_HOST=moe-mysql-app
      - PMA_PORT= 3306
    
    volumes:
      - /sessions
    container_name: moe-phpmyadmin-app
    
 
networks:
  frontend:
  backend:


first nodejs image to build the application in docker container that is already written docker file for node js is hosted in this repositoty
second image for mysql : set the environment variables for host,username and password
third image is php: i used this image to access the data base application . instead of mysql image i used php image to access the application


To access the data base server: Data base applications is running on 30002 port number i have set the reverse proxy without port number . when user can access the application without port number
----------
databaseservername: http://18.204.89.75  
and give below details for username and password
username:moeuser
passsword:moepass

To access the node jsapplication in browser using URL http://18.204.89.75:30001/


ci/cd pipeline : i used jenkins declartive pipeline( pipeline as code)
---------

below jenkins file
pipeline {
    agent any
    
tools {
    dockerTool 'Docker'
}

    stages {
        stage('git') {
            steps {
                git 'https://github.com/surya3cs/mysql-nodejs.git'
            }
        }
        
        stage('docker-mysql-nodesjs') {
            steps {
                sh 'sudo docker-compose up -d'
            }
        }
        
    }
}


To access the jenkins application
----------------
http://18.204.89.75:8080/
username: admin
passowrd: admin






  

