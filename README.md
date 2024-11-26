# Scalable Microservices Application: MP3 Video to Audio Converter  

This repository contains the source code and configuration for a **scalable microservices application** built using Python, Flask, Docker, Kubernetes, MySQL, MongoDB, and RabbitMQ. The application allows users to convert video links into audio files. Each functionality is encapsulated in its dedicated service, promoting modularity, scalability, and maintainability.  

---

## **Architecture Overview**  

The application is built around five microservices, each with a specific role:  

1. **Gateway Service**  
2. **Auth Service**  
3. **Database Services (MySQL and MongoDB GridFS)**  
4. **Converter Service**  
5. **Notification Service**  

Each service runs as a separate Docker container and is deployed in a Kubernetes cluster.  

---

## **Features**  

- **User Authentication**: Secure user login and registration.  
- **Video Downloading and Storage**: Store video files in MongoDB GridFS.  
- **Task Queuing**: Asynchronous task management using RabbitMQ.  
- **Video to Audio Conversion**: Convert videos to audio files in MP3 format.  
- **Real-Time Notifications**: Notify users when the audio conversion is complete.  
- **Scalable and Resilient Deployment**: Fully containerized application deployed in Kubernetes for high availability.  

---

## **Microservices Description**  

### 1. **Gateway Service**  
- **Purpose**: Acts as the entry point to the application.  
- **Responsibilities**:  
  - Redirects users to the appropriate service.  
  - Authenticates incoming requests and directs unauthenticated users to the Auth Service.  
  - Ensures secure routing between services.  
- **Technology**: Flask, Docker.  

### 2. **Auth Service**  
- **Purpose**: Manages user authentication and registration.  
- **Responsibilities**:  
  - Allows users to register and log in securely.  
  - Stores user data in the MySQL database service.  
  - Generates and validates authentication tokens.  
- **Technology**: Flask, MySQL, Docker.  

### 3. **Database Services**  
#### **MySQL**  
- **Purpose**: Stores user authentication and profile data.  
- **Role in Application**:  
  - Works with the Auth Service to manage user-related data.  
#### **MongoDB GridFS**  
- **Purpose**: Stores video files and converted audio files.  
- **Role in Application**:  
  - Enables efficient storage and retrieval of large binary files (videos and audios).  

### 4. **Converter Service**  
- **Purpose**: Handles the conversion of video files into audio files.  
- **Responsibilities**:  
  - Consumes messages from RabbitMQ to determine which video to convert.  
  - Fetches the video from MongoDB GridFS.  
  - Converts the video to MP3 format.  
  - Stores the converted audio back in MongoDB GridFS.  
  - Publishes a success message to RabbitMQ once the conversion is complete.  
- **Technology**: Flask, FFmpeg (for video-to-audio conversion), MongoDB, RabbitMQ, Docker.  

### 5. **Notification Service**  
- **Purpose**: Notifies the user when the video-to-audio conversion is complete.  
- **Responsibilities**:  
  - Listens to RabbitMQ for messages indicating successful conversion.  
  - Sends notifications to the user (e.g., email, push notification, etc.).  
- **Technology**: Flask, RabbitMQ, Docker.  

---

## **System Workflow**  

1. **User Interaction**  
   - The user accesses the application via the **Gateway Service**.  
   - If unauthenticated, they are redirected to the **Auth Service** for login or registration.  

2. **Task Submission**  
   - An authenticated user posts a video link.  
   - The **Gateway Service** processes the request and forwards it to the **Converter Service**.  

3. **Video Download and Storage**  
   - The video is downloaded by the **Converter Service**.  
   - The video file is stored in **MongoDB GridFS** with a unique ID.  

4. **Task Queuing**  
   - A message containing the video ID is sent to the RabbitMQ **task queue**.  

5. **Video Conversion**  
   - The **Converter Service** reads the task message from RabbitMQ.  
   - Fetches the video from MongoDB GridFS.  
   - Converts the video to an MP3 file using FFmpeg.  
   - Stores the MP3 file in MongoDB GridFS.  
   - Sends a success message to the RabbitMQ **notification queue**.  

6. **User Notification**  
   - The **Notification Service** reads messages from the **notification queue**.  
   - Sends a notification to the user indicating that their audio file is ready.  

---

## **Technologies Used**  

- **Backend Framework**: Python, Flask  
- **Message Broker**: RabbitMQ  
- **Databases**:  
  - MySQL (Relational database for user data)  
  - MongoDB GridFS (Binary file storage for videos and audios)  
- **Containerization**: Docker  
- **Orchestration**: Kubernetes  
- **Video Conversion**: FFmpeg  

---

## **Setup and Deployment**  

### Prerequisites  
- Docker  
- Kubernetes Cluster (Minikube or Cloud Provider)  
- Python 3.10  
- FFmpeg  

### Steps  

1. **Clone the Repository**  
   ```bash  
   git clone mp3_microservices_app
   cd microservices-python-main
2. **Build Docker Images**: For each directory/service, build the Docker images for each microservice.
   ```bash  
   docker build -t ./
3. **Deploy to Kubernetes**: For each directory/service, apply the Kubernetes configurations in the k8s directory.
   ```bash  
   kubectl apply -f k8s/  
