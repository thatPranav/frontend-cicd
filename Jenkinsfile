node {

    stage('clone repo') {
        checkout scm
        url: 'https://github.com/thatPranav/angular-ci'
    }
    
    stage('npm install') {
        sh 'npm install'
    }

    stage('get code coverage') {
        script {
            COVERAGE_SUMM = sh(
                returnStdout: true, 
                script: 'ng test --code-coverage'
            ).trim()
        }
    }

    stage('check code coverage and deploy') {
        def result = (COVERAGE_SUMM =~ /[0-9.]+%/).findAll()
        def statements = result[0].substring(0, result[0].length() - 1).toInteger(); 
        def branches = result[1].substring(0, result[1].length() - 1).toInteger(); 
        def functions = result[2].substring(0, result[2].length() - 1).toInteger(); 
        def lines = result[3].substring(0, result[3].length() - 1).toInteger(); 
        if (statements > 70 && branches > 70 && functions > 70 && lines > 70){
            sh 'docker build -t angular-ci:v1 .'
            sh 'docker run --rm -d -p 80:80/tcp angular-ci:v1'
            // notify('Deployment to staging done!')
        }
        else {
            // notify('Code coverage below 70%')
        }
    }
    

}

// mailhog needed to send email!
def notify(status){
    emailext (
        to: "jawajipranav@gmail.com",
        subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
        body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
            <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>""",
    )
}



// stage name: 'Deploy to staging', concurrency: 1
// node {
//     sh "echo '<h1>${env.BUILD_DISPLAY_NAME}</h1>' >> app/index.html"
    
//     sh 'docker-compose up -d --build'
    
//     notify('Solitaire Deployed!')
// }
