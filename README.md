All commands below assume you are on Ubuntu 24.04 and you run them with a user that has sudo privileges

Step 1 — Update the system
sudo apt update
Sample Output:
 

Step 2: Install Java
sudo apt install openjdk-11-jdk -y
java -version

Sample Output:
 
 

Java runtime and compiler will be available. Jenkins uses the JRE/JDK to run. If Java fails to install, Jenkins install will fail or warn.

Step 3 — Add Jenkins repository and key
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

sudo sh -c 'echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ > /etc/apt/sources.list.d/jenkins.list'

sudo apt update	

Sample Output:
 

Step 4: Install Jenkins
sudo apt install jenkins -y
Sample Output:
 

Step 5 — Start and enable Jenkins service
sudo systemctl start jenkins
sudo systemctl enable jenkins
sudo systemctl status Jenkins

 

Step 6 — Open port and access Jenkins UI
Access Jenkins in browser: http://localhost:8080.

What you will see the first time:
•	A web page asking to "Unlock Jenkins" with a field for the initial admin password. The UI prompts to paste the password from the next step.
If you cannot access:
•	If accessing from another machine, ensure firewall allows port 8080 (e.g., sudo ufw allow 8080), or use SSH port forwarding.
•	If Jenkins uses another port (customized), use that port instead.

Step 7 — Unlock Jenkins & initial setup
Command to get the initial password
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

What it does:
•	Prints a randomly created admin password. This is shown only once at first boot.
 

What you will do next (UI flow):
1.	Paste that password into the browser unlock page.
2.	Choose “Install suggested plugins” (recommended for beginners) — Jenkins downloads and installs common plugins.
3.	Create the first admin user (username/password).
4.	Click Save & Finish.

Post-installation configuration
•	Configure tools like JDK and Git under Manage Jenkins → Global Tool Configuration.
•	Create a job/pipeline.
•	Install any additional plugins you need.
•	Schedule backups of Jenkins config (e.g., copy /var/lib/jenkins or use a backup plugin).

Create & execute the example pipeline
Checkout → Build → Test from a GitHub repository (e.g., https://github.com/your-username/HelloWorldProject.git). 

pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                echo 'Cloning source code from GitHub...'
                git branch: 'main', url: 'https://github.com/your-username/HelloWorldProject.git'
            }
        }
        stage('Build') {
            steps {
                echo 'Building Java project...'
                sh 'javac HelloWorld.java'
            }
        }
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'java HelloWorld'
            }
        }
    }
    post {
        success { echo 'Pipeline executed successfully!' }
        failure { echo 'Pipeline failed. Check logs for details.' }
    }
}

Steps to create pipeline in UI
•	Open Jenkins Dashboard → New Item
•	Enter name (e.g., HelloWorldPipeline)
•	Choose Pipeline → Click OK
•	Paste the pipeline script into the Pipeline Script box → Save → Build Now.

