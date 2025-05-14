pipeline {
    agent any

    tools {
        maven "MAVEN_HOME"
    }

    stages {
        stage('Clone') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    git branch: 'main',
                        credentialsId: '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',
                        url: 'https://github.com/yucradavid/sytemalmacen.git'
                }
            }
        }

        stage('Build') {
            steps {
                timeout(time: 8, unit: 'MINUTES') {
                    sh "mvn -DskipTests clean package -f SysAlmacen/pom.xml"
                }
            }
        }

        stage('Test') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    sh "mvn clean install -f SysAlmacen/pom.xml"
                }
            }
        }

        stage('Sonar') {
            steps {
                timeout(time: 4, unit: 'MINUTES') {
                    withSonarQubeEnv('sonarqube') {
                        sh "mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.9.0.2155:sonar -Pcoverage -f SysAlmacen/pom.xml"
                    }
                }
            }
        }

        stage('Quality gate') {
            steps {
                timeout(time: 4, unit: 'MINUTES') {
                    script {
                        // Espera el resultado del análisis desde SonarQube
                        def qualityGate = waitForQualityGate()
                        if (qualityGate.status != 'OK') {
                            error "Falló la puerta de calidad: ${qualityGate.status}"
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                timeout(time: 8, unit: 'MINUTES') {
                    echo "mvn spring-boot:run -f SysAlmacen/pom.xml"
                    // Aquí podrías descomentar si quieres que realmente se ejecute
                    // sh "mvn spring-boot:run -f SysAlmacen/pom.xml"
                }
            }
        }
    }
}
