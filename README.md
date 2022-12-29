# python_docker_heroku
_Learn how to dockerize Python scripts and a Python web app and deploy to Heroku in a multi-arch setup from MacBook with M1 chip_

## Step 1: Containerize a Python script and a Python app
* `docker build -t fastapi-tutorial .`
* `docker run -d --name myfastapicontainer -p 80:80 fastapi-tutorial`
* References
   * How to containerize Python applications with Docker
      * [YouTube](https://youtu.be/0UG2x2iWerk)
      * [GitHub](https://github.com/patrickloeber/python-docker-tutorial)
   * [Blog Post: How to “Dockerize” Your Python Applications](https://www.docker.com/blog/how-to-dockerize-your-python-applications/)

## Step 2: Containerize a Python app with multi-arch containers
* `docker build --platform linux/amd64 -t fastapi-tutorial .`
* `docker run -d --name myfastapicontainer -p 80:80 fastapi-tutorial`

## Step 3: Deploy a multi-arch container from M1 Macbook to Heroku
* Set up Docker for multi-arch deployment via container registries
   * _[FIRST TIME ONLY]_ Create a new Docker builder instance: `docker buildx create --name mybuilder --use --bootstrap`
   * List builder instances to confirm creation was successful: `docker buildx ls`
* Push a multi-arch image to the GitHub Container Registry
   * Save your GitHub personal access token as an environment variable: `export CR_PAT=YOUR_TOKEN`
   * Sign in to the GitHub Container registry service at `ghcr.io`: `echo $CR_PAT | docker login ghcr.io -u USERNAME --password-stdin`
   * Push multi-arch image to GitHub Container Registry: `docker buildx build --push --platform linux/amd64,linux/arm64 --tag ghcr.io/karnaker/python_docker_heroku:latest .`
* Push a multi-arch (ish) image to the Heroku Container Registry (note: Heroku will only accept linux/amd64 images, hence the "ish")
   * Log in to Heroku: `heroku login`
   * Log in to the Heroku container registry: `heroku container:login`
   * Push linux/amd64 image to Heroku Container Registry:
      * `docker buildx build --push --platform linux/amd64 --tag registry.heroku.com/python-docker-heroku/web .`
      * `heroku container:release web`
* References
   * Heroku
      * [Container Registry & Runtime (Docker Deploys)](https://devcenter.heroku.com/articles/container-registry-and-runtime)
      * [Create a Heroku remote](https://devcenter.heroku.com/articles/git#create-a-heroku-remote)
   * GitHub
      * [GitHub: Working with the Container registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)
      * [YouTube: GitHub Packages.. Containers in a GitHub repo?](https://youtu.be/gqseP_wTZsk)

<!--- TODO
Step 3: ~Push multi-arch image to GitHub Container Registry. Push linux/amd64 image to Heroku Container Registry.
Fix error below and test on Heroku.

Most likely need to replace PORT=80 with $PORT, as Heroku determines the port.

heroku logs --tail
2022-12-24T02:03:48.799335+00:00 heroku[web.1]: State changed from crashed to starting
2022-12-24T02:03:48.731845+00:00 heroku[web.1]: Process exited with status 1
2022-12-24T02:03:49.910711+00:00 heroku[router]: at=error code=H10 desc="App crashed" method=GET path="/" host=python-docker-heroku.herokuapp.com request_id=d92dc06e-a8d4-4d06-872f-4825090bed81 fwd="74.108.223.130" dyno= connect= service= status=503 bytes= protocol=https
2022-12-24T02:04:00.263993+00:00 heroku[web.1]: Starting process with command `uvicorn app.main:app --host 0.0.0.0 --port 80`
2022-12-24T02:04:01.377466+00:00 app[web.1]: INFO:     Started server process [4]
2022-12-24T02:04:01.377489+00:00 app[web.1]: INFO:     Waiting for application startup.
2022-12-24T02:04:01.377639+00:00 app[web.1]: INFO:     Application startup complete.
2022-12-24T02:04:01.377853+00:00 app[web.1]: ERROR:    [Errno 13] error while attempting to bind on address ('0.0.0.0', 80): permission denied
2022-12-24T02:04:01.377885+00:00 app[web.1]: INFO:     Waiting for application shutdown.
2022-12-24T02:04:01.377976+00:00 app[web.1]: INFO:     Application shutdown complete.
2022-12-24T02:04:01.514809+00:00 heroku[web.1]: Process exited with status 1
2022-12-24T02:04:01.565343+00:00 heroku[web.1]: State changed from starting to crashed
2022-12-24T02:04:02.227052+00:00 heroku[router]: at=error code=H10 desc="App crashed" method=GET path="/favicon.ico" host=python-docker-heroku.herokuapp.com request_id=1ec8f6c7-ea75-40e3-a230-b801862c7f57 fwd="74.108.223.130" dyno= connect= service= status=503 bytes= protocol=https
--->
