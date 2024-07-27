node {
    stage('Build') {
        // Menjalankan perintah untuk mengkompilasi file Python
        sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        
        // Menyimpan hasil kompilasi
        stash name: 'compiled-results', includes: 'sources/*.py*'
    }
    
    stage('Test') {
        try {
            // Menjalankan tes dengan pytest dan menyimpan hasilnya
            sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
        } finally {
            // Menampilkan laporan tes selalu
            junit 'test-reports/results.xml'
        }
    }
}
