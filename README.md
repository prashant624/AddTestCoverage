# AddTestCoverage
AddTestCoverage
pipeline {
    agent any

    environment {
        NODE_ENV = 'test'
        GIT_COMMITTER_NAME = 'jenkins'
        GIT_COMMITTER_EMAIL = 'ci@jenkins.com'
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm run test' // Replace with your test command
                archiveArtifacts artifacts: 'testScan.json', onlyIfSuccessful: true
            }
        }

        stage('Publish Test Results') {
            steps {
                echo 'Publishing JUnit report (if any)'
                // Only include this if you generate JUnit XML reports
                // junit '**/test-results.xml'
            }
        }

        stage('Update README') {
            when {
                expression { fileExists('testScan.json') }
            }
            steps {
                script {
                    def testJson = readJSON file: 'testScan.json'
                    def passed = testJson.testsPassed ?: 0
                    def total = testJson.totalTests ?: 0
                    def coverage = testJson.coverage ?: 'N/A'

                    def readme = readFile 'README.md'
                    readme = readme
                        .replaceAll(/Tests\s+\|\s+\d+\/\d+/, "Tests | ${passed}/${total}")
                        .replaceAll(/Coverage\s+\|\s+\d+(\.\d+)?%/, "Coverage | ${coverage}%")
                    writeFile file: 'README.md', text: readme
                }
            }
        }

        stage('Commit README') {
            when {
                expression { fileExists('README.md') }
            }
            steps {
                sh '''
                    git config user.name "${GIT_COMMITTER_NAME}"
                    git config user.email "${GIT_COMMITTER_EMAIL}"
                    git add README.md
                    git commit -m "ci: update test and coverage in README"
                    git push origin develop
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
        failure {
            echo 'Build failed. Check logs and reports.'
        }
    }


    stage('Update README') {
    when {
        expression { fileExists('testScan.json') }
    }
    steps {
        script {
            def testJson = readJSON file: 'testScan.json'
            def passed = testJson.testsPassed ?: 0
            def total = testJson.totalTests ?: 0
            def coverage = testJson.coverage ?: 'N/A'

            def readme = readFile 'README.md'
            readme = readme
                .replaceAll(/Tests\s+\|\s+\d+\/\d+/, "Tests | ${passed}/${total}")
                .replaceAll(/Coverage\s+\|\s+\d+(\.\d+)?%/, "Coverage | ${coverage}%")
            writeFile file: 'README.md', text: readme
        }
    }
}
eature	innerHTML	textContent
Displays content	Yes	Yes
Treats content as HTML	✅ Yes (risky)	❌ No (safe)
Executes scripts	✅ Yes — big risk	❌ Never
Safe for user/API data	❌ No	✅ Yes
}
