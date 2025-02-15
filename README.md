# **📌 Todo List Web Application**

This project is a simple **To-Do List Web Application** built using **Flask** and **MySQL**, deployed using **Docker** and orchestrated with **Docker Compose**. The application is designed to be modular and scalable, using **Nginx** as a reverse proxy.

---

## **📁 Project Structure**
```
Todo_list/
├── README.md              # Project documentation
├── app/                   # Flask application directory
│   ├── Dockerfile         # Docker configuration for the Flask app
│   ├── app.py             # Main Flask application
│   ├── requirements.txt   # Python dependencies
│   └── templates/         # HTML templates
│       └── index.html     # Main template file
├── docker-compose.yaml    # Docker Compose configuration
└── nginx/                 # Nginx reverse proxy configuration
    └── default.conf       # Nginx configuration file
```

---

## **🚀 Prerequisites**
Ensure you have the following installed:
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

---

## **🔧 Installation & Setup**
### **1️⃣ Clone the Repository**
```bash
git clone https://github.com/yp3yp3/Todo_list.git
cd Todo_list
```

### **2️⃣ Build and Start the Containers**
```bash
docker-compose up --build -d
```
This will:
- Build the Flask app inside a container.
- Start a MySQL database (if included in `docker-compose.yaml`).
- Configure Nginx as a reverse proxy.

### **3️⃣ Check Running Containers**
```bash
docker ps
```
Expected output:
```
CONTAINER ID   IMAGE        STATUS         PORTS
xyz123         todo-app     Up 3 minutes  5000->5000
abc456         nginx        Up 3 minutes  80->80
```

### **4️⃣ Access the Application**
Open a browser and navigate to:
```
http://localhost
```

---

## **⚙️ Application Components**
### **📌 Flask Application (`app/`)**
- **`app.py`** - Main Python file that runs the Flask application.
- **`requirements.txt`** - List of Python dependencies.
- **`templates/index.html`** - Basic HTML template for the frontend.

### **📌 Nginx Configuration (`nginx/`)**
- **`default.conf`** - Configures Nginx as a reverse proxy for Flask.

### **📌 Docker Setup**
- **`docker-compose.yaml`** - Defines all services.
- **`app/Dockerfile`** - Builds the Flask container.

---

## **📜 Usage**
### **📌 Stopping & Restarting Containers**
To stop all containers:
```bash
docker-compose down
```
To restart with changes:
```bash
docker-compose up --build -d
```

### **📌 Viewing Logs**
```bash
docker-compose logs -f app
```

### **📌 Debugging the App Inside the Container**
```bash
docker exec -it <container_id> sh
```

---

## **🐞 Troubleshooting**
### **🔹 Flask App Not Running?**
Check logs:
```bash
docker-compose logs app
```

### **🔹 Nginx Not Proxying?**
Make sure **`default.conf`** is correctly set and mapped:
```nginx
location / {
    proxy_pass http://app:5000;
}
```

---

## **📜 License**
This project is **open-source** and free to use.

🚀 **Enjoy coding!** If you have any issues, feel free to open an issue on GitHub! 😃
