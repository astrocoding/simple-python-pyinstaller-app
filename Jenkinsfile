node {
    try {
        stage('Build') {
            docker.image('python:2-alpine').inside {
                try {
                    echo "Starting build stage..."
                    sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                    echo "Build stage completed successfully."
                } catch (Exception e) {
                    echo "Build stage failed with error: ${e}"
                    throw e
                }
            }
        }

        stage('Test') {
            docker.image('qnib/pytest').inside {
                try {
                    echo "Starting test stage..."
                    sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
                } catch (Exception e) {
                    echo "Test stage failed with error: ${e}"
                    throw e
                } finally {
                    junit 'test-reports/results.xml'
                    echo "Test stage completed."
                }
            }
        }
    } catch (Exception e) {
        echo "Pipeline failed with error: ${e}"
        currentBuild.result = 'FAILURE'
    } finally {
        echo "Pipeline result: ${currentBuild.result}"
    }
}
