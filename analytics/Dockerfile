FROM public.ecr.aws/docker/library/python:3.10-slim-buster

#Installing dependencies
RUN apt update -y
RUN apt install build-essential libpq-dev -y
RUN pip install --upgrade pip setuptools wheel

COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

#Setting environment variables
ENV DB_PASSWORD=
ENV DB_USERNAME=
ENV DB_HOST=127.0.0.1
ENV DB_PORT=5433
ENV DB_NAME=mydatabase

COPY . .

# Start app
CMD python app.py