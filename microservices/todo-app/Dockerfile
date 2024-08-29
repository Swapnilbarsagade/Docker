# Use the official Ubuntu base image
FROM ubuntu:latest

# Update the package repository and install Apache
RUN apt-get update && \
    apt-get install -y apache2 && \
    apt-get clean

# Expose port 80 to the outside world
EXPOSE 80

# Copy the static ToDo app files to the Apache server directory
COPY ./todo/ /var/www/html/

# Start Apache in the foreground
CMD ["apache2ctl", "-D", "FOREGROUND"]
