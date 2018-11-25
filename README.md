## SETUP PROJECT
- Create `requirments.txt`
```
Django==2.1.1
psycopg2
```
- Create `Dockerfile`
```
FROM python:3.6
RUN apt-get update \
    && apt-get install -y postgresql-client \
    && rm -rf /var/lib/apt/lists/*
ENV PYTHONUNBUFFERED 1
RUN mkdir /app
WORKDIR /app
ADD requirements.txt /app/
ADD wait-for-it.sh /app/
RUN pip install -r requirements.txt
RUN pip install --upgrade pip
ADD . /app/

EXPOSE 8000
```
- Create `docker-compose.yml`
```
version: '3'

services:
  db:
    image: postgres
  web:
    build: .
    command: "python3.6 manage.py runserver 0.0.0.0:8000"
    volumes:
      - .:/app
    ports:
      - "8000:8000"
    depends_on:
      - db
```
- Run command inside container for create project `example`
```
docker-compose run web django-admin.py startproject example .
```
- Edit `example/settings.py`
```
ALLOWED_HOSTS = []
↓
ALLOWED_HOSTS = ['*']
```
```
'default': {
    'ENGINE': 'django.db.backends.sqlite3',
    'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
}
↓
'default': {
    'ENGINE': 'django.db.backends.postgresql',
    'NAME': 'postgres',
    'USER': 'postgres',
    'HOST': 'db',
    'PORT': 5432,
}
```
- Migrate
```
docker-compose run web python3.6 manage.py migrate --noinput
```
- Create super user
```
docker-compose run web python3.6 manage.py createsuperuser
```
Super user
```
user: admin
email: admin@admin.com
password: 123@123a
```
- Run container
```
docker-compose up
```
- Access into container
```
docker-compose exec web sh
```
- Stop container
```
Control + C
```
- Remove image
```
docker-compose down
```
- Build image
```
docker-compose build
```
## HOW TO RUN?
