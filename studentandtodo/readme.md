######### Hosting Microsevices on Docker Container on ubuntu instances in AWS #############

Step 1: Launching VM( ubuntu instance) with following security groups.

    SSH = 22
    http = 80
    https = 443
    MySQL/Aurora = 3306
    Tomcat = 8080
    Custom tcp = 3315

Step 2: Launch RDS instance with mariadb engine.

    #  Remember to save your credentials like following.
    Master username
    admin
    Master password
    redhat123

    # Allow to connect to  ec2 instance in action tab.

Step 3: Connect to ubuntu instance using SSH and Install Docker apt repository.

    1. Set up Docker's apt repository.

        # Add Docker's official GPG key:
    sudo apt-get update
    sudo apt-get install ca-certificates curl
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc

    # Add the repository to Apt sources:
    echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update

    2. Install the Docker packages.

    sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

Step 4: Creating a container Network.

    sudo docker network create my_network

Step 5: Hosting Tomcat and Mariadb containers for student microservice.

    # creating mariadb container

        sudo docker run -d \
    --name mariadb \
    --network my_network \
    -e MYSQL_ROOT_PASSWORD=my-secret-pw \
    -e MYSQL_DATABASE=my_database \
    -e MYSQL_USER=my_user \
    -e MYSQL_PASSWORD=my_user_pw \
    mariadb:10.5

    # creating tomcat container

    sudo docker run -d \
    --name tomcat \
    --network my_network \
    -p 3315:8080 \
    tomcat:9

    # upload student application artifacts i.e. (.war & .jar) file  using cp command and download mariadb connecter.

    # student.war --> webapps

    sudo docker cp <path_of_file> <containerid>:/usr/local/tomcat/webapps

    # mysql-connector.jar --> lib

    sudo docker cp <path_of_file> <containerid>:/usr/local/tomcat/lib

    # download mariadb connector for internet and add to lib directory of tomcat

    curl -O https://dlm.mariadb.com/3852266/Connectors/java/connector-java-3.4.1/mariadb-java-client-3.4.1.jar

    sudo docker cp <path_of_file> <containerid>:/usr/local/tomcat/lib


    # Configure the context.xml file usind vim editor

    sudo docker exec -it <container_id> bash
    sudo apt update -y
    sudo apt install vim -y

    # vim context.xml(line no 21)

    		<Resource name="jdbc/TestDB" auth="Container" type="javax.sql.DataSource"
               maxTotal="100" maxIdle="30" maxWaitMillis="10000"
               username="USERNAME" password="PASSWORD" driverClassName="com.mysql.jdbc.Driver"
               url="jdbc:mysql://DB-ENDPOINT:3306/DATABASE"/>

    # Connect to mariadb container and create the schema for storing entries of student.

    sudo docker exec -it <container_id> bash

    # login to db engine

    mysql -h endpointofdb -u (usernameofyourdb -p(passwordofyourdb)

    # Create db and table

    CREATE DATABASE studentapp;
    use studentapp;
    CREATE TABLE if not exists students(student_id INT NOT NULL AUTO_INCREMENT,
        student_name VARCHAR(100) NOT NULL,
        student_addr VARCHAR(100) NOT NULL,
        student_age VARCHAR(3) NOT NULL,
        student_qual VARCHAR(20) NOT NULL,
        student_percent VARCHAR(10) NOT NULL,
        student_year_passed VARCHAR(10) NOT NULL,
        PRIMARY KEY (student_id)
    );

    # Exit the mariadb and restart the container and check if security groups are properly mention.

    sudo docker restart <container_id>

Step 6 : Hosting todo microservice on nginx container.

    # Creating Nginx Container

    sudo docker run -d \
    --name todo \
    --network my_network \
    -p 80:80 \
    nginx

    # Uploading todo static folder in nginx location

    sudo docker cp <path_of_folder> <containerid>:/usr/share/nginx/html/

    # Restart the Container

    sudo docker restart <container_id>

Step 7: Copy the instance public ip and give port and proper path and see for the result

![alt text](<Screenshot (330).png>)

![alt text](<Screenshot (332).png>)

![alt text](<Screenshot (331).png>)
