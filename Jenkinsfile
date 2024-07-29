node {
    // Stage: Build
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }
    
    // Stage: Test
    stage('Test') {
        docker.image('qnib/pytest').inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
        // Post actions
        junit 'test-reports/results.xml'
    }
    
    // Stage: Manual Approval
    stage('Manual Approval') {
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
    
    // Stage: Deploy
    stage('Deploy') {
        docker.image('python:3.8').inside('-u root') {
            script {
                sh 'pip install pyinstaller'
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
            echo 'Menunggu 1 menit sebelum mengakhiri...'
            sleep(time: 1, unit: 'MINUTES')
            echo 'Eksekusi pipeline selesai.'
        }
        // Post actions
        archiveArtifacts 'dist/add2vals'
    }
}
