FROM python:3.10-slim-buster

RUN apt update -y
RUN apt install build-essential libpq-dev -y
RUN pip install --upgrade pip setuptools wheel

COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

ARG DB_PASSWORD=$DB_PASSWORD
ARG DB_USERNAME=$DB_USERNAME

ENV DB_PASSWORD=$DB_PASSWORD
ENV DB_USERNAME=$DB_USERNAME
ENV DB_HOST=127.0.0.1
ENV DB_PORT=5433
ENV DB_NAME=mydatabase

COPY . .

CMD python app.py