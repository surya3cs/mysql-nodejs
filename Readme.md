aws server login details
-------
username:surya3cs@gmail.com
password:Surya@3399

Go to Task Server instance in aws ec2 console 
--------
This instance is ubuntu and i have already installed docker and jenkins there


  
  
 First Task:
 -----------
  
  i took the node js application , the application is running on docker container and node js app is to connect mysql data base
  
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





  

