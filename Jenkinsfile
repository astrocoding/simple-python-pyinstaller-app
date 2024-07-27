node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
            // Kompilasi file Python
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }

    stage('Test') {
        docker.image('qnib/pytest').inside {
            try {
                // Jalankan tes dengan pytest dan simpan hasilnya
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            } finally {
                // Publikasikan laporan tes
                junit 'test-reports/results.xml'
            }
        }
    }
}
