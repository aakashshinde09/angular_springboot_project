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
     git clone https://github.com/sharadrathod/Project-Angular-App.git
     ```
   - Copy the schema file outside the project:
     ```sh
     cp Project-Angular-App/springbackend.sql .
     ```
   - Create the schema:
     ```sh
     mysql -h <endpoint> -u <user> -p<password> springbackend < springbackend.sql
     ```

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
     git clone https://github.com/sharadrathod/Project-Angular-App.git
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
   - Build the project:
     ```sh
     mvn clean package -Dmaven.test.skip=true
     ```

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
   - Configure `worker.service.ts`:
     ```sh
     vim src/app/services/worker.service.ts
     ```
     - Replace `localhost` with your instance's public IP address.
     - Replace port `8080` with the port your backend Docker container is exposed on (e.g., `8085`).
   - Build the project:
     ```sh
     npm install
     ng build
     ```

## Starting the Servers

1. **Start Backend Server**
   - Go to the backend instance.
   - Start the backend server:
     ```sh
     java -jar target/spring-backend-v1.jar
     ```

2. **Start Frontend Server**
   - Go back to the frontend container.
   - Start the frontend server:
     ```sh
     ng serve --host 0.0.0.0 --port=30080
     ```

3. **Access the Application**
   - Copy the public IP of the instance and paste it into the browser with port `30080/workers`.
   - You should see the page with the data.
   - To add a worker, click "Add Worker" and fill in the data.

## Notes

- Make sure the backend and frontend containers are running on the same port to ensure proper communication.
- Adjust security group rules to allow necessary traffic for your application.
