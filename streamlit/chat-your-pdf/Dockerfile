FROM python:3.9.10-slim-buster

RUN apt-get update && apt-get install python-tk python3-tk tk-dev git -y

WORKDIR /app

COPY app/. .

RUN pip install -r requirements.txt

EXPOSE 8080

ENTRYPOINT ["streamlit", "run"]

CMD ["main.py",  "--server.port", "8080", "--server.enableXsrfProtection", "false"]]
