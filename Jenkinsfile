pipeline{
    agent any
    environment{
        DOCKERHUB_USERNAME = "vancelot7789"
        APP_NAME = "gitops-argo-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CRED = "dockerhub"
        DBCONTAINERNAME = 'pokemonpostgrestest_container'
    }

    stages{
        stage('Cleanup workspace'){
            steps{
                script{
                    cleanWs()
                }
            }
        }
        stage('Checkout SCM'){
            steps{
                script{
                    git credentialsId: 'github',
                    url: 'https://github.com/vancelot7789/PokemonAuthApi.git',
                    branch: 'main'
                }
            }
        }
        stage('Start Database') {
            steps {
                script {
                    // Ensure any previous container is removed
                    sh "docker rm -f ${DBCONTAINERNAME} || true"
                    
                    // Start a new database container
                    sh """
                    docker run --name ${DBCONTAINERNAME} \\
                      -e POSTGRES_USER=bbchang \\
                      -e POSTGRES_PASSWORD=changemeinprod! \\
                      -e POSTGRES_DB=pokemontest \\
                      -p 5433:5432 \\
                      -d postgres
                    """

                    // Wait for the database to become ready
                    sh """
                    until docker exec ${DBCONTAINERNAME} pg_isready -U bbchang; do
                      echo "Waiting for database to become ready..."
                      sleep 2
                    done
                    """
                }
            }
        }

        // Add schema and seed-data 
        // stage('Initialize Database') {
        //     steps {
        //         script {
        //             // Copy schema setup script into the running database container
        //             sh "docker cp ./database/schema.sql ${DBCONTAINERNAME}:/schema.sql"
                    
        //             // Execute the schema setup script
        //             sh "docker exec ${DBCONTAINERNAME} psql -U bbchang -d pokemontest -f /schema.sql"
                    
        //             // Copy seed data script into the running database container
        //             sh "docker cp ./database/seed-data.sql ${DBCONTAINERNAME}:/seed-data.sql"
                    
        //             // Execute the seed data script
        //             sh "docker exec ${DBCONTAINERNAME} psql -U bbchang -d pokemontest -f /seed-data.sql"
        //         }
        //     }
        // }

        stage('Build') {
            steps {
                sh './mvnw clean package' // Adjust Maven command to compile without testing
            }
        }

        stage('Test') {
            steps {
                sh './mvnw test' // Run tests without compiling
            }
        }

        stage('Build Docker Image'){
            steps{
                script{

                    docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('Push Docker Image'){
            steps{
                script{
                    docker.withRegistry('', "$REGISTRY_CRED"){
                        docker_image.push("$BUILD_NUMBER")
                        docker_image.push('latest')
                    }
                }      
            }  
        }
        stage('Delete Docker images'){

            steps{
                script{

                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Checkout Deployment Code') {
            steps {
                // Checkout the deployment scripts repository
                git credentialsId: 'github',
                url: 'https://github.com/vancelot7789/gitops_argocd_project.git',
                branch: 'main'
            }
        }
        

        stage('Updating kubernetes deployment file'){
            steps{
                script{

                    sh """
                    cat app-deployment.yaml
                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' app-deployment.yaml
                    cat app-deployment.yaml
                    """
                }
            }
        }

        stage('Push the changed deployment file to Git'){
            steps{
                script{
                    sh """
                      git config --global user.name "vancelot7789"
                      git config --global user.email "a0952072007@gmail.com"
                      git add app-deployment.yaml
                      git commit -m "updated the app-deployment file"
                    """
                    withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {

                    sh "git push https://github.com/vancelot7789/gitops_argocd_project.git main"
                  }
                }
            }
        }

    }
}