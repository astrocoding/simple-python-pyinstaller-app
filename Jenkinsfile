pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                echo "Starting build stage..."
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                echo "Build stage completed successfully."
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                echo "Starting test stage..."
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
                echo "Test stage completed."
            }
            post {
                always {
                    echo "Publishing test results..."
                    junit 'test-reports/results.xml'
                    echo "Test results published."
                }
            }
        }
    }
}
