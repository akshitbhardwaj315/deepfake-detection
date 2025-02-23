# Use Python 3.13 slim base image
FROM python:3.13-slim

# Environment variables to optimize Python behavior in Docker
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Install build tools and libraries for compiling dependencies and wheels
RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    gcc \
    g++ \
    make \
    libopenblas-dev \
    liblapack-dev \
    libjpeg-dev \
    zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

# Create directories for static files and videos
RUN mkdir -p /home/app/staticfiles/app/uploaded_videos/
WORKDIR /app

# Copy requirements and install dependencies
COPY ./requirements.txt /app/requirements.txt
RUN python -m pip install --upgrade pip
RUN pip install cmake
RUN pip install cython>=3.0.5  # Explicitly install Cython first
RUN pip install opencv-python==4.9.0.80
RUN pip install --prefer-binary -r requirements.txt  # Prefer prebuilt wheels

# Copy the entire project and collect static files
COPY . /app/
RUN python manage.py collectstatic --noinput

# Install Gunicorn for serving
RUN pip install gunicorn

# Create directory for uploaded videos (ensure it matches settings.py)
RUN mkdir -p /app/uploaded_videos/app/uploaded_videos/

# Define volume for runtime data (e.g., socket files)
VOLUME /app/run/

# Set entrypoint for Gunicorn
ENTRYPOINT ["/app/bin/gunicorn_start.sh"]