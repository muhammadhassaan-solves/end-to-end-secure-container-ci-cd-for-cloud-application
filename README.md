<h1>End-to-End Container CI/CD for Cloud Application</h1>


<h2>Description</h2>
Designed and implemented a fully automated CI/CD pipeline for a Node.js application using Jenkins, Docker, and AWS EC2. The pipeline ensures reliable, traceable, and fast deployments by integrating automated testing, containerization, Docker image versioning, and health checks. <br />


<h2>Tools and Technologies</h2>

- Jenkins 
- Docker
- AWS EC2 
- Node.js 
- Git 
- Trivy

<h2>Project Walk-through</h2>

<p align="center">
1. Configured Jenkins to build and test code inside Docker containers for consistent environments <br />
<img src="https://i.postimg.cc/RCHkBjzc/a.jpg"/>
<br />
<br />
2. Created a demo Node.js application and Dockerfile to containerize the application <br/>
<img src="https://i.postimg.cc/KvbHyjf2/b.jpg" />
<br />
<br />
3. Developed declarative pipeline with stages: Checkout, Test, Build, Security Scan, Push, Deploy. <br/>
<img src="https://i.postimg.cc/6pyLsxkK/d.jpg"/>
<br />
<br />
4. Trigger Pipeline from GitHub that deploy Docker containers to EC2 with health checks. <br/>
<img src="https://i.postimg.cc/sx56FdqK/h.jpg" />
<br />
<br />


</p>

