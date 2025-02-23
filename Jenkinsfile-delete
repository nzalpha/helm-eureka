
pipeline{
    agent{
        label 'workernode-1'
    }

    tools{
        maven 'mvn-3.8.8'
        jdk 'jdk-17'
    }
    environment {
        Application_Name = "eureka"
        Pom_Version = readMavenPom().getVersion()
        Pom_Packaging = readMavenPom().getPackaging()
        Docker_Hub = "docker.io/aadil08"
        Docker_Creds = credentials('docker_creds')
        //Docker_Host_ip
    }
    stages{
        stage ('Build'){
            // This will takee care of building the application
            steps{
                echo "Building ${env.Application_Name} Application"
                // build using maven
                sh 'mvn clean package -DskipTests=true'
                archiveArtifacts artifacts: 'target/*.jar'
            }
        }

        /*stage ('Unit Tests'){
            steps {
                echo "Executing Unit Tests for ${env.Application_Name} App"
                sh 'mvn test'
            }
        } 
        */

        stage ('Docker Format'){
            // This is to format artifact
            steps{
                //install Pipeline Utility to use readMavenPOM
                //i27-eureka-0.0.1-SNAPSHOT.jar →> eureka-build number-branch name.jar
                echo "The actual format is ${env.Application_Name}-${env.Pom_Version}.${env.Pom_Packaging}"

                 // Expected ureka-buildnumber-branchname.jar to get build number in Jenkins goto Pipeline Syntax> Global Variables Reference

                 echo "Custom Format is ${env.Application_Name}-${BUILD_NUMBER}-${BRANCH_NAME}.${env.Pom_Packaging}"

            }
        }

        stage ('Docker Build & Push') {
            steps{
                echo "Starting Docker Build "
                sh """
                ls -la
                pwd
                echo "Copy the jar to the folder where Docker file is present"
                cp ${WORKSPACE}/target/i27-${env.Application_Name}-${env.Pom_Version}.${env.Pom_Packaging} ./.cicd/

                echo "********************* Building Docker Image ********************"
                docker build --force-rm  --no-cache --build-arg JAR_SRC=i27-${env.Application_Name}-${env.Pom_Version}.${env.Pom_Packaging}  -t ${env.Docker_Hub}/${env.Application_Name}:${GIT_COMMIT} ./.cicd
                docker images

                echo "********************* Login to Docker Repo ********************"
                docker login -u ${Docker_Creds_USR} -p ${Docker_Creds_PSW}

                echo "********************* Docker Push ********************"
                docker push ${env.Docker_Hub}/${env.Application_Name}:${GIT_COMMIT}

                """
            }
        }

        stage ('Deploy to Dev'){
            steps {
               // echo "*************************  Deploy to Dev  *****************************"
                //withCredentials([usernamePassword(credentialsId: 'ali_dock-vm', passwordVariable: 'password', usernameVariable: 'username')]) {
                //With this block Slave will connect to docker server using ssh and will execute the commands we want.
                // Connect to the Docker Server from Jenkin machine. Credentials for user ali are stored in Jenkin  using the above. The user ali is root user in Docker machine which we created.
                // When we execute this, jenking master initiates jenkin slave. Jenkin slave will connect to Docker Vm using the cred and execuste
                
                
                // As we dont want to hardcode ip address, we can keep public ip addrs as "Environment variables" under Manage Jenkins>System>Global Properties
                //sshpass -p password ssh -o StrictHostKeyChecking=no username@ipaddress command_to_run. 
                // Command: docker run -d -p hp:cp --name containername image:tagname ( container port 8761 is always same in all different environment for Eureka application)
                // docker run -d -p 5761:8761 --name ${env.Application_Name}-dev ${env.Docker_Hub}/${env.Application_Name}:${GIT_COMMIT}
              //  sh "sshpass -p  ${password} ssh -o StrictHostKeyChecking=no ${username}@${docker_server_ip} docker run -d -p 5761:8761 --name ${env.Application_Name}-dev ${env.Docker_Hub}/${env.Application_Name}:${GIT_COMMIT}"
                //}

                script{
                    // This block is written because if the container is running already and run the jenkin file it was throwing error because the container name already exists so we are writing the below
                    // to make sure we catch any exception in try catch.

                    // pull the image 
                  sh "docker pull  ${env.Docker_Hub}/${env.Application_Name}:${GIT_COMMIT}"

                  try{
                    //Stop the container
                    sh "docker stop ${env.Application_Name}-dev"

                    //remove the container
                    sh "docker rm ${env.Application_Name}-dev"

                  } catch(err){
                    echo "Error Caught: $err"
                  }
                  // create the container
                echo "*************************  Running the Dev Container  *****************************"
                sh "docker run -d -p 5761:8761 --name ${env.Application_Name}-dev ${env.Docker_Hub}/${env.Application_Name}:${GIT_COMMIT}"
                }
            }
        }

        stage ("Deploy to Stage"){
            steps {
               // echo "*************************  Deploy to stage  *****************************"
                //withCredentials([usernamePassword(credentialsId: 'ali_dock-vm', passwordVariable: 'password', usernameVariable: 'username')]) {
                //With this block Slave will connect to docker server using ssh and will execute the commands we want.
                // Connect to the Docker Server from Jenkin machine. Credentials for user ali are stored in Jenkin  using the above. The user ali is root user in Docker machine which we created.
                // When we execute this, jenking master initiates jenkin slave. Jenkin slave will connect to Docker Vm using the cred and execuste
                
                
                // As we dont want to hardcode ip address, we can keep public ip addrs as "Environment variables" under Manage Jenkins>System>Global Properties
                //sshpass -p password ssh -o StrictHostKeyChecking=no username@ipaddress command_to_run. 
                // Command: docker run -d -p hp:cp --name containername image:tagname ( container port 8761 is always same in all different environment for Eureka application)
                // docker run -d -p 5761:8761 --name ${env.Application_Name}-dev ${env.Docker_Hub}/${env.Application_Name}:${GIT_COMMIT}
              //  sh "sshpass -p  ${password} ssh -o StrictHostKeyChecking=no ${username}@${docker_server_ip} docker run -d -p 5761:8761 --name ${env.Application_Name}-dev ${env.Docker_Hub}/${env.Application_Name}:${GIT_COMMIT}"
                //}

                script{
                    // This block is written because if the container is running already and run the jenkin file it was throwing error because the container name already exists so we are writing the below
                    // to make sure we catch any exception in try catch.

                    // pull the image 
                  sh "docker pull  ${env.Docker_Hub}/${env.Application_Name}:${GIT_COMMIT}"

                  try{
                    //Stop the container
                    sh "docker stop ${env.Application_Name}-stg"

                    //remove the container
                    sh "docker rm ${env.Application_Name}-stg"

                  } catch(err){
                    echo "Error Caught: $err"
                  }
                  // create the container
                echo "*************************  Running the stg Container  *****************************"
                sh "docker run -d -p 7761:8761 --name ${env.Application_Name}-stg ${env.Docker_Hub}/${env.Application_Name}:${GIT_COMMIT}"
                }
            }
        }
        }
    }
}