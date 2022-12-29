FROM python:3.9

# TARGETPLATFORM: Platform of the build result. Uses default unless the target
# platform is specified with the --platform flag on docker build.
ARG TARGETPLATFORM

RUN echo "TARGETPLATFORM: Docker is building for $TARGETPLATFORM"

WORKDIR /code

COPY ./requirements.txt /code/requirements.txt

RUN pip install --no-cache-dir --upgrade -r /code/requirements.txt

COPY ./app /code/app

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "80"]
