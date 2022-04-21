# Microservices experiments

This solution contains 4 services written in GO that talk to each other.

* Teacher Portal - Web application for teachers to write test scores for students
* Grading Service - Service for receiving data from Teacher Portal and storing it in memory
* Logs Service - Service for gathering logs
* Registry Service - Service registry, where each service reports to

## Build
Run the following commands in the root of repository 
```
go build app/cmd/teacherportal
go build app/cmd/gradingservice
go build app/cmd/logservice    
go build app/cmd/registryservice
```
## Run
Then run all bulit programs
```
.\registryservice.exe
.\logservice.exe
.\gradingservice.exe
.\teacherportal.exe
```
You can browse the website written in raw GO on `http://localhost:5000` (if the port 5000 was not taken by another application). Browse the Grade Book, see all student's scores and add test results.

---
## Explanation
On startup, every service registers itself in registry service. This allows the following:

Registry service pings all services that registered themselves on the startup to check if they are alive:
```
2022/04/21 20:33:40 Heartbeat check passed for TeacherPortal
2022/04/21 20:33:40 Heartbeat check passed for GradingService
2022/04/21 20:33:40 Heartbeat check passed for LogService
```

If one of the services fail (in this case I've stopped logging service), grading service, which was depending on logs service will get an update:
```
UPDATE RECEIVED
Update received: {Added:[] Removed:[{Name:LogService URL:http://localhost:4000}]}
```
If I'll start Logs Service again, Service Registry will notify Grading Service which will be visible in Grading Service's terminal:
```
UPDATE RECEIVED
Update received: {Added:[{Name:LogService URL:http://localhost:4000}] Removed:[]}
```