# Todo List Web Application

## 📌 Overview
This is a simple **To-Do List** web application built with **Flask** and **MySQL**, containerized using **Docker** and managed via **Docker Compose**. The app allows users to create, manage, and delete tasks efficiently.

## 🚀 Features
- Add, update, and delete tasks
- Support for task deadlines with color-coded urgency
- Data persistence using MySQL
- Multi-container architecture with Docker Compose
- Load balancing with Nginx for multiple app instances

## 🏗 Technologies Used
- **Backend:** Flask (Python)
- **Database:** MySQL
- **Frontend:** HTML, CSS
- **Containerization:** Docker, Docker Compose
- **Reverse Proxy & Load Balancer:** Nginx

## 📂 Project Structure
```
Todo_list/
│── app.py           # Flask application logic
│── templates/       # HTML templates for UI
│── requirements.txt # Python dependencies
│── Dockerfile       # Docker image build configuration
│── docker-compose.yml # Multi-container orchestration
│── nginx/           # Nginx configuration
└── README.md        # Project documentation
```

## 🛠 Installation & Setup
### **1️⃣ Clone the Repository**
```bash
git clone https://github.com/yp3yp3/Todo_list.git
cd Todo_list
```
### **2️⃣ Build and Run with Docker Compose**
```bash
docker compose up --build -d
```
### **3️⃣ Access the Application**
Once running, visit:
```bash
http://localhost
```

## ⚙️ Environment Variables
Ensure you configure the following environment variables in `docker-compose.yml`:
```yaml
environment:
  DB_HOST: db
  DB_USER: myuser
  DB_PASSWORD: mypassword
  DB_NAME: my_database
```

## 📝 Nginx Load Balancer Configuration
Nginx is configured to distribute requests between multiple app instances:
```nginx
upstream flask_backend {
    server app-1:5000;
    server app-2:5000;
}
server {
    listen 80;
    location / {
        proxy_pass http://flask_backend;
    }
}
```

## 🏗 Future Improvements
- User authentication
- Task prioritization and categories
- Deploying on AWS/GCP with CI/CD pipeline

## 🤝 Contributing
Feel free to submit **issues** or **pull requests** to improve this project!

## 📜 License
This project is licensed under the **MIT License**.

---
🚀 **Developed by [@yp3yp3](https://github.com/yp3yp3)**

