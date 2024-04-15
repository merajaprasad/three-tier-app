# Wanderlust - Your Ultimate Travel Blog 🌍✈️

WanderLust is a simple MERN travel blog website ✈ This project is aimed to help people to contribute in open source, upskill in react and also master git.

![Preview Image](https://github.com/krishnaacharyaa/wanderlust/assets/116620586/17ba9da6-225f-481d-87c0-5d5a010a9538)

### Prerequites
1. Install Node.js from this [link](https://nodejs.org/en/download/package-manager)
2. Install MongoDB from this [link](https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/) for ubuntu system

## Setting up the project locally

### Setting up the Backend

1. **Fork and Clone the Repository**
   ```
   git clone <repo-url>
   ```

2. **Navigate to the Backend Directory**

   ```bash
   cd backend
   ```

3. **Install Required Dependencies**

   ```bash
   npm i
   ```

4. **Set up your MongoDB Database**

   - Open MongoDB Compass and connect MongoDB locally at `mongodb://localhost:27017`.

5. **Import sample data**

   > To populate the database with sample posts, you can copy the content from the `backend/data/sample_posts.json` file and insert it as a document in the `wanderlust/posts` collection in your local MongoDB database using either MongoDB Compass or `mongoimport`.

   ```bash
   mongoimport --db wanderlust --collection posts --file ./data/sample_posts.json --jsonArray
   ```

6. **Configure Environment Variables**

   ```bash
   cp .env.sample .env
   ```

7. **Start the Backend Server**

   ```bash
   npm start
   ```

   > You should see the following on your terminal output on successful setup.
   >
   > ```bash
   > [BACKEND] Server is running on port 5000
   > [BACKEND] Database connected: mongodb://127.0.0.1/wanderlust
   > ```
   
   Now you can access the server from browser using [publicip:5000]

### Setting up the Frontend

1. **Open a New Terminal**

   ```bash
   cd frontend
   ```

2. **Install Dependencies**

   ```bash
   npm i
   ```

3. **Configure Environment Variables**
   

   open `.env.sample` file and change the _localhost:5000_ with EC2 server _PublicIPaddress:5000_ and save. then run below


   ```bash
   cp .env.sample .env.local
   ```

5. **Launch the Development Server without exposing port**

   ```bash
   npm run dev
   ```
   **Launch the Development Server with exposing network port**

   ```bash
   npm run dev -- --host
   ```
   **Launch the Development Server in Background**
   ```bash
   nohup npm run dev -- --host &
   ```


## Deploy on Docker Container on AWS EC2

   **Install docker on Ubuntu**
   ```
   sudo apt-get install docker.io
   ```
### Setting up backend Container

   **Navigate to the Backend Directory**

   ```bash
   cd backend
   ```

   **Create Dockerfile**
   ```
   FROM node:21
   WORKDIR /app
   COPY . .
   RUN npm i
   COPY .env.sample .env
   EXPOSE 5000
   CMD ["npm","start"]
   ```
   **Build Backend image through dockerfile**
   ```
   sudo docker build -t backend .
   ```
   **Install mongodb Image**
   ```
   sudo docker run -d -p 27017:27017 --name mongo mongo:latest
   ```
   now go inside mongoDB container using `docker exec -it mongo bash` and run the command `mongosh` to test mongoDB is installed and running.

   **create and run backend container**
   ```
   sudo docker run -d -p 5000:5000 --name backend backend:latest
   ```
### Setting up Frontend Container

   **Navigate to the Frontend Directory**

   ```bash
   cd frontend
   ```
   **Create Dockerfile**
   ```
   FROM node:21
   WORKDIR /app
   COPY . .
   RUN npm i
   COPY .env.sample .env.local
   EXPOSE 5173
   CMD ["npm", "run", "dev", "--", "--host"] 
   ```
   **Build Frontend image through dockerfile**
   ```
   sudo docker build -t frontend .
   ```
   **Run frontend container**
   ```
   sudo docker run -d -p 5173:5173 --name frontend frontend:latest
   ```
   Now you would be able to see our website is running from browser using **[ public-ip:5173 ]**. but here feature posts data will not be feach from backend. for that we have to add the backend server(where backend container is running) public ip in `.env.sample` variable file. and again we have to build frontend image and create frontend container using same above command.
   
# Docker Compose

   **Install Docker-Compose on ubuntu**
   ```
   sudo apt-get install docker-compose
   ```
   ***Create Docker-compose.yml file**

   ```
   version: "3.8"
   services:
      mongodb:
         container_name: mongo
         image: mongo:latest #pull mongodb latest image from dockerhub
         volumes:  # maping volume for sample data
            - ./backend/data:/data # hostpath : containerpath
         ports:
            - "27017:27017"

      backend:
         container_name: backend
         build: ./backend # docker file location to build image
         env_file:
            - ./backend/.env.sample
         ports:
            - "5000:5000"
         depends_on: # backend-container depend-on mongodb-container to create
            - mongodb

      frontend:
         container_name: frontend
         build: ./frontend # docker file location to build image
         env_file:
            - ./backend/.env.sample
         ports:
            - "5173:5173"

   # Declaring the volume

   volumes:
      data:

   ```

   **Run docker compose file**
   
   before running the docker-compose file we have to add mongodb container name in `.env.sample` variable file because container usese container names to communicate, it do not use `127.0.0.1(localhost)` IP to communicate with other container. 
   
   ```
   docker-compose up -d
   ```

   **import sample data**
   ```
   sudo docker exec -it mongo mongoimport --db wanderlust --collection posts --file ./data/sample_posts.json --jsonArray
   ```

   Now you would be able to see our website is running from browser using **[ public-ip : 5173 ]**.


## 🌟 Ready to Contribute?

Kindly go through [CONTRIBUTING.md](https://github.com/krishnaacharyaa/wanderlust/blob/main/.github/CONTRIBUTING.md) to understand everything from setup to contributing guidelines.

## 💖 Show Your Support

If you find this project interesting and inspiring, please consider showing your support by starring it on GitHub! Your star goes a long way in helping me reach more developers and encourages me to keep enhancing the project.

🚀 Feel free to get in touch with me for any further queries or support, happy to help :)
