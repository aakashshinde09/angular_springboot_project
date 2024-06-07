# Hosting Angular Website Manually in Docker Container

## Prerequisites

1. **Instance Setup**
   - Launch an instance and connect to it.
   - Login as a root user.
   - Install Docker:
     ```sh
     yum install docker -y
     ```
   - Start and enable Docker:
     ```sh
     systemctl enable --now docker
     ```

2. **Database Setup (RDS MySQL)**
   - Create a DB instance in RDS.
   - Allow all TCP in the DB instance security group.
   - Copy the endpoint of the DB instance.
   - Login to MySQL in CloudShell:
     ```sh
     mysql -h <endpoint> -u <user> -p<password>
     ```
   - Create a database:
     ```sh
     CREATE DATABASE springbackend;
     ```
   - Clone the repository to get the schema file:
     ```sh
     git clone https://github.com/aakashshinde09/angular_springboot_project.git
     ```
   - Copy the schema file outside the project:
     ```sh
     cp Project-Angular-App/springbackend.sql .
     ```
   - Create the schema:
     ```sh
     mysql -h <endpoint> -u <user> -p<password> springbackend < springbackend.sql
     ```
   - Login to the database again:
     ```sh
     mysql -h <endpoint> -u <user> -p<password>
     ```
   - View all the databases created:
     ```sh
     SHOW DATABASES;
     ```
   - Use the database:
     ```sh
     use springbackend;
     ```
   - View the tables created:
     ```sh
     SHOW tables;
     ```
   - View the content of the table:
     ```sh
     select * from tbl_workers;
     ```
       ![Database Setup](https://github.com/aakashshinde09/assets/blob/2e7cf8a833c8c0296cdf590bc3f1ff9c3a31246c/angular_project_assets/1.jpg)

## Backend Setup

1. **Run Backend Container**
   - Use the Tomcat image to host the backend:
     ```sh
     docker run -d -p 8085:8085 tomcat
     ```
   - Check if the container is running:
     ```sh
     docker ps
     ```
     ![Backend Setup](https://github.com/aakashshinde09/assets/blob/2e7cf8a833c8c0296cdf590bc3f1ff9c3a31246c/angular_project_assets/2.jpg)

2. **Deploy Backend Manually**
   - Enter the container:
     ```sh
     docker exec -it <container_id> /bin/bash
     ```
   - Install Git:
     ```sh
     apt update
     apt install git
     ```
   - Clone the repository:
     ```sh
     git clone https://github.com/aakashshinde09/angular_springboot_project.git
     ```
   - Navigate to the backend directory:
     ```sh
     cd Project-Angular-App/spring-backend
     ```
   - Install Java:
     ```sh
     apt install openjdk-8-jdk
     ```
   - Install Maven:
     ```sh
     apt install maven
     ```
   - Configure the database connection:
     ```sh
     vim src/main/resources/application.properties
     ```
     - Replace `localhost` with your DB instance endpoint.
     - Add your username, password, and the port your Docker container is running on.
      ![Deploy Backend Manually](https://github.com/aakashshinde09/assets/blob/2e7cf8a833c8c0296cdf590bc3f1ff9c3a31246c/angular_project_assets/6.jpg)
   - Build the project:
     ```sh
     mvn clean package -Dmaven.test.skip=true
     ```
      ![Deploy Backend Manually](https://github.com/aakashshinde09/assets/blob/2e7cf8a833c8c0296cdf590bc3f1ff9c3a31246c/angular_project_assets/8.jpg)
   


## Frontend Setup

1. **Run Frontend Container**
   - Use the nginx image to host the Angular application:
     ```sh
     docker run -d -p 30080:30080 nginx
     ```
   - Check if the container is running:
     ```sh
     docker ps
     ```
     ![Frontend Setup](https://github.com/aakashshinde09/assets/blob/2e7cf8a833c8c0296cdf590bc3f1ff9c3a31246c/angular_project_assets/11.jpg)

2. **Deploy Frontend Manually**
   - Enter the container:
     ```sh
     docker exec -it <container_id> /bin/bash
     ```
   - Update and install Git:
     ```sh
     apt update
     apt install git -y
     ```
   - Clone the repository:
     ```sh
     git clone https://github.com/sharadrathod/Project-Angular-App.git
     ```
   - Navigate to the frontend directory:
     ```sh
     cd Project-Angular-App/angular-frontend
     ```
   - Install Node.js and npm:
     ```sh
     apt install nodejs npm -y
     ```
   - Install Angular CLI:
     ```sh
     npm install -g @angular/cli
     ```
   - Check Angular CLI installation:
     ```sh
     ng version
     ```
     ![Frontend Setup](https://github.com/aakashshinde09/assets/blob/2e7cf8a833c8c0296cdf590bc3f1ff9c3a31246c/angular_project_assets/13.jpg)
   - Configure `worker.service.ts`:
     ```sh
     vim src/app/services/worker.service.ts
     ```
     - Replace `localhost` with your instance's public IP address.
     - Replace port `8080` with the port your backend Docker container is exposed on (e.g., `8085`).

      ![Frontend Setup](https://github.com/aakashshinde09/assets/blob/2e7cf8a833c8c0296cdf590bc3f1ff9c3a31246c/angular_project_assets/14.jpg)
   - Build the project:
     ```sh
     npm install
     ng build
     ```
     ![Frontend Setup](https://github.com/aakashshinde09/assets/blob/2e7cf8a833c8c0296cdf590bc3f1ff9c3a31246c/angular_project_assets/18.jpg)

## Starting the Servers

1. **Start Backend Server**
   - Go to the backend instance.
   - Start the backend server:
     ```sh
     java -jar target/spring-backend-v1.jar
     ```
     ![Starting the Servers](https://github.com/aakashshinde09/assets/blob/2e7cf8a833c8c0296cdf590bc3f1ff9c3a31246c/angular_project_assets/20.jpg)

2. **Start Frontend Server**
   - Go back to the frontend container.
   - Start the frontend server:
     ```sh
     ng serve --host 0.0.0.0 --port=30080
     ```
     ![Start Frontend Server](https://github.com/aakashshinde09/assets/blob/2e7cf8a833c8c0296cdf590bc3f1ff9c3a31246c/angular_project_assets/21.jpg)

3. **Access the Application**
   - Copy the public IP of the instance and paste it into the browser with port `30080/workers`.
   - You should see the page with the data.
   - To add a worker, click "Add Worker" and fill in the data.
    ![Access the Application](https://github.com/aakashshinde09/assets/blob/2e7cf8a833c8c0296cdf590bc3f1ff9c3a31246c/angular_project_assets/22.jpg)

       ![Access the Application](https://github.com/aakashshinde09/assets/blob/2e7cf8a833c8c0296cdf590bc3f1ff9c3a31246c/angular_project_assets/23.jpg)

       ![Access the Application](https://github.com/aakashshinde09/assets/blob/2e7cf8a833c8c0296cdf590bc3f1ff9c3a31246c/angular_project_assets/24.jpg)

## Notes

- Make sure the backend and frontend containers are running on the same port to ensure proper communication.
- Adjust security group rules to allow necessary traffic for your application.
