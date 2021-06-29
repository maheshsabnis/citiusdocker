# Docker Desktop for the OS must be installed
    - https://www.docker.com/products/docker-desktop
    - The docker repository account is mandatory in real-world app development
        - https://hub.docker.com/
# The Interaction or Management of Docker is done using Docker Commands     
    - The 'docker' is command line utility 
# Important docker commands
1. Version of docker
    - docker -v
2. List of Images
    - docker images 
        - Image REPOSITORY
            - The name of teh image that is provided when am image is build or created
                - Used to manage the image e.g. remove image, push the image to repository    
        - IMAGE ID
            - The ramdom ID provided by the Docker Registry of Docker Engine on local machine by default
                - Used to manage the image e.g. remove image, push the image to repository    
        - CREATED
            - The time duration of image creation
        - SIZE
            - The size of image with all of its depednencies
        - TAG
            - The version name / number that is provided while creating an image
            - Recommended so that its can be easily idenottified while pushing and pulling to and from repository
3. List of containers
    - docker ps
        - CONTAINER ID
            - Randomly generate ID
        - IMAGE 
            - The image id running in the container
        - CREATED
            - The time duration when the container is created
        - STATUS
            - If the Container is running or existed
        - PORT
            - Each container is provided with an IP and network bridge by Docker Engine so that it can be accessible by other containers or by host machine
            - The port is the gateway  using which app running inside the container will be accessible from outside    
        - NAMES
            - The name of the container
4. List of containers those are currently started or running
    - docker ps -a       
5. How to import standard image from repository and use it? 
    - Authenticate against the docker repository
        - docker login
            - ENter username and password
    - The Standard SQL Image
        - docker pull <IMAGE-REPOSITORY-NAME>:<TAG>
        - docker pull mcr.microsoft.com/mssql/server
        - docker pull mcr.microsoft.com/mssql/server:2019-latest
    - Run the Image in the container
        - docker run -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=P@ssw0rd_" -p 1433:1433 --name sql1 -h sql1 -d mcr.microsoft.com/mssql/server:2019-latest
            - -e "ACCEPT_EULA=Y", acept the end-user license agreement
            - -e "SA_PASSWORD=P@ssw0rd_", the crdentials to connect to SQL Server
            - -p 1433:1433
                - The container in which the image is running must export a port and map it with the port on local machine so that the container can be connected
                    - -p <PORT-OF-LOCAL-MACHINE>:<PORT-EXPOSED-BY-CONTAINER>
            - --name sql1
                - the container name in which the image will be hosted and executed
                - --name [CONTAINER-NAME]
            - -h sql1, the host name (this is used only in case of Database Servers)
            -  -d mcr.microsoft.com/mssql/server:2019-latest
                - The image that will be loaded into container and executed
                    - -d [IMAGE-NAME]               
        - OPening the container in Interactive Mode, so that we can access the contents of container
            - docker exec -it [CONTAINER-NAME] "bash"
        - inreatcting with SQL Server inside the container using SQLCMD
            - /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P P@ssw0rd_                
6. How to Create Image?
    - Creat dockerfile that will contains all configuration for creating image
    - Define the base or runtime image that will be used by your application as depednency
        - FROM [IMAGE-FROM-REPPOSITORY]
    - Define a Workdirectory for the application in the container
        - WORKDIR [dir-name]
    - Copy project file of the ASP.NET Core Application into the image
        - COPY [PROJECT-FILE] [TARGET-DIRECTORY-ON-IMAGE]
    - Run the command to build the dependencies in the image
        - RUN dotnet restore <PROJECT-FILE-NAME>      
    - Copy the code file from Source to Targer
        - COPY [SOURCE-DIRECTORY-FROM-PROJECT] [TARGET-DIRECTORY-ON-IMAGE]
    - Build the app inside the image
        - RUN dotnet build [PROJECT-FILE] -c Debug -o [OUTPUT-PATH]
    - Define which base runtime wil be used by the application in image to execute the application
        - FROM [ASP.NET-CORE-IMAGE]
    - Set the Working Directory aka Application Directory for execution
        - WORKDIR [DIR-PATH-FROM-WHERE-THE-APP-STARTS]
    - Copy build output to the Working Directory
        - COPY --from=[RUNTIME-IMAGE-NAME-OR-ITS-ALISE] [BUILD-SOURCE-PATH]
    - Expose the port ffrom the container so that it can accept requistes from outside world
        - EXPORT [PORT]
    - Set an Entry-point to start execution of the application in the ontainer
        - ENTRYPOINT ["dotnet", "<APPLICATION.DLL-FOR-ASPNET-CORE-APP>"]    
``` csharp
FROM mcr.microsoft.com/dotnet/sdk:3.1 AS builder
WORKDIR /src
COPY ./serverapp.csproj .
RUN dotnet restore serverapp.csproj
COPY . .
RUN dotnet build serverapp.csproj -c Debug -o /src/out
# using ASP.NET Core SDK to run the image inside the container
FROM mcr.microsoft.com/dotnet/aspnet:3.1
WORKDIR /app
COPY --from=builder /src/out .

EXPOSE 80
ENTRYPOINT [ "dotnet", "serverapp.dll"]
```

    - Run the following command to build the docker image. Make sure that the path from which the following command is executed, must contain the Dockerfile
        - docker build . -t [IMAGE-NAME]:[TAG]
        - docker build . -t service:v1
7. How to Run the Image inside default container provided by docker?
    - docker run -p [LOCAL-POTY]:[CONTAINER-PORT] [IMAGE-NAME]:[TAG]
8. How to Run the image inside a container created by us?
    - docker run -p [LOCAL-POTY]:[CONTAINER-PORT] --name=[CONTAINER-NAME] [IMAGE-NAME]:[TAG]
    - docker run -d -p 5000:80 --name=servervm service:v1
        - -d means the container is running in background till it does not thows exceptio or it is not stopped by us
9. How to Stop and Delete Container?
    - Stopping container
        - docker stop [CONTAINER-NAME | CONTAIER-ID]
    - Start the Stopped container
        - docker start [CONTAINER-NAME | CONTAINER-ID]
    - DELETE the Container
        - docker rm [CONTAINER-NAME | CONTAIER-ID]     

10. How to Delete Image?
    - docker rmi [IMAGE-NAME | IMAGE-ID]
11. How to Push Image in Public Repository?             
