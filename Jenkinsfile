pipeline {
    agent {
        kubernetes {
            label 'jenkins-agent-my-app'
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    component: ci
spec:
  containers:
  - name: python
    image: python:3.7
    command: ["cat"]
    tty: true
  - name: docker
    image: docker:latest
    command: ["cat"]
    tty: true
    volumeMounts:
    - name: docker-socket
      mountPath: /var/run/docker.sock
  - name: dind
    image: docker:24-dind
    command: ["dockerd", "--host=unix:///var/run/docker.sock", "--host=tcp://0.0.0.0:2375"]
    securityContext:
      privileged: true
    volumeMounts:
    - name: docker-socket
      mountPath: /var/run
  volumes:
  - name: docker-socket
    emptyDir: {}
"""
        }
    }
    stages {
        stage('Test python') {
            steps {
                container('python') {
                    sh "pip install -r requirements.txt"
                    sh "python test.py"
                }
            }
        }
        stage('Build image') {
            steps {
                container('docker') {
                    sh "docker build -t localhost:4000/pythontest:latest ."
                    sh "docker push localhost:4000/pythontest:latest"
                }
            }
        }
    }
}