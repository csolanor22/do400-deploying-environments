pipeline {
    agent {
        node {
            label 'maven'
        }
    }
    stages {
        stage('Tests') {
            steps {
                sh './mvnw clean test'
            }
        }
        stage('Package') {
            steps {
                sh '''
                    ./mvnw package -DskipTests \
                    -Dquarkus.package.type=uber-jar
                '''
                archiveArtifacts 'target/*.jar'
            }
        }
        stage('Build Image') {
            environment { QUAY = credentials('QUAY_USER') } ❶
            steps {
                sh '''
                    ./mvnw quarkus:add-extension \
                    -Dextensions="kubernetes,container-image-jib" ❷
                '''
                sh '''
                    ./mvnw package -DskipTests \
                    -Dquarkus.jib.base-jvm-image=quay.io/redhattraining/do400-java-alpine-openjdk11-jre:latest \
                    -Dquarkus.container-image.build=true \ ❸
                    -Dquarkus.container-image.registry=quay.io \ ❹
                    -Dquarkus.container-image.group=$QUAY_USR \ ❺
                    -Dquarkus.container-image.name=do400-deploying-environments \ ❻
                    -Dquarkus.container-image.username=$QUAY_USR \ ❼
                    -Dquarkus.container-image.password="$QUAY_PSW" \ ❽
                    -Dquarkus.container-image.push=true ❾
                '''
            }
        }
    }
}