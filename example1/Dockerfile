# Dockerfile: A blueprint for building images
# Docker Image: A template for running containers
# Docker Container: Your running process with your application in it

FROM python:3.9

ADD main.py .

RUN pip install requests beautifulsoup4

CMD ["python", "./main.py"]
