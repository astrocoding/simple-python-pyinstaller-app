pipeline {
    agent any
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
            agent {
                docker {
                    image 'cdrx/pyinstaller-linux:python2'
                    args '-p 3000:3000' // Menyesuaikan dengan port jika diperlukan
                }
            }
            steps {
                script {
                    // Menjalankan kontainer untuk debugging jika perlu
                    sh 'docker run -it --rm cdrx/pyinstaller-linux:python2 /bin/sh'
                    sh 'echo "Memulai Deploy Stage"'
                    
                    // Pastikan pyinstaller terinstal dan jalankan perintah deploy
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
