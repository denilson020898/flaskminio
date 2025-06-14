# flaskapp.dockerfile
FROM python:3.8-slim
WORKDIR /app
COPY . .
RUN pip install Flask requests
EXPOSE 5000
ENV FLASK_ENV=development
CMD ["python", "/app/main.py"]
