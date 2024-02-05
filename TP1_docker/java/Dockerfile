# Use a Java runtime as the base image
FROM openjdk:11-jre-slim

# Set the working directory inside the container
WORKDIR /app

# Copy the compiled Java code into the container
COPY src/Main.class /app/

# Run the Java application
CMD ["javac", "Main.class"]
