# create-a-Golang-program
Use docker to create a web application with date &amp; time as the only content.
# main.go
package main

import (
	"fmt"
	"net/http"
	"time"
)

func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "Current date and time: %s", time.Now().Format(time.RFC1123))
}

func main() {
	http.HandleFunc("/", handler)
	http.ListenAndServe(":8080", nil)
}

---
# Dockerfile
FROM golang:1.20-alpine
WORKDIR /app
COPY . .
RUN go build -o main .
CMD ["./main"]
EXPOSE 8080

---
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: datetime-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: datetime
  template:
    metadata:
      labels:
        app: datetime
    spec:
      containers:
      - name: datetime-container
        image: <your-dockerhub-username>/datetime-app
        ports:
        - containerPort: 8080

---
# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: datetime-service
spec:
  type: LoadBalancer
  selector:
    app: datetime
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080

