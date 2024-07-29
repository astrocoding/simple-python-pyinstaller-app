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
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Manual Approval') {
            steps {
                script {
                    def userInput = input(
                        id: 'Proceed', message: 'Lanjutkan ke tahap Deploy?', parameters: [
                            choice(name: 'Lanjutkan?', choices: 'Proceed\nAbort', description: 'Pilih opsi:')
                        ]
                    )
                    if (userInput == 'Abort') {
                        error('Pipeline dihentikan oleh pengguna.')
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    sh 'echo "Memulai Deploy Stage"'
                    sh 'ls -l' // Daftar file untuk memastikan sources/add2vals.py ada
                    sh 'which pyinstaller' // Pastikan pyinstaller terinstal
                    sh 'pyinstaller --onefile sources/add2vals.py'
                }
                echo 'Menunggu 1 menit sebelum mengakhiri...'
                sleep(time: 1, unit: 'MINUTES')
                echo 'Eksekusi pipeline selesai.'
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}
