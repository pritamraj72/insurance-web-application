pipeline {
    agent { 
        label 'Slave1' 
    }

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven-3.8.7"
    }

    environment {    
        DOCKERHUB_CREDENTIALS = credentials('dockerloginid')
    } 
    
    stages {
        stage('SCM Checkout') {
            steps {
                // Get some code from a GitHub repository
                git url: 'git@github.com:manju65char/star-agile-insurance-project.git'
            }
        }
        stage('Maven Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
        stage("Docker build") {
            steps {
                sh 'docker version'
                sh "docker build -t manjunathachar/insurance_app:${BUILD_NUMBER} ."
                sh 'docker image list'
                sh "docker tag manjunathachar/insurance_app:${BUILD_NUMBER} manjunathachar/insurance_app:latest"
            }
        }
        stage('Login to Docker Hub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Approve - push Image to Docker Hub') {
            steps {
                //----------------send an approval prompt-------------
                script {
                   env.APPROVED_DEPLOY = input message: 'User input required. Choose "yes" to approve or "Abort" to reject', ok: 'Yes', submitterParameter: 'APPROVER'
                }
                //-----------------end approval prompt------------
            }
        }
        stage('Push to Docker Hub') {
            steps {
                sh "docker push manjunathachar/insurance_app:latest"
            }
        }
        stage('Approve - Deployment to Kubernetes Cluster') {
            steps {
                //----------------send an approval prompt-----------
                script {
                   env.APPROVED_DEPLOY_KUBE = input message: 'User input required. Choose "yes" to approve or "Abort" to reject', ok: 'Yes', submitterParameter: 'APPROVER_KUBE'
                }
                //-----------------end approval prompt------------
            }
        }
        stage('Deploy to Ansible Controller') {
            steps {
                script {
                  sshPublisher(publishers: [sshPublisherDesc(configName: 'Ansible-controller', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook  ansible-playbook.yml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '/home/devopsadmin', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'ansible-playbook.yml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
                }
            }
        }
    }
}

