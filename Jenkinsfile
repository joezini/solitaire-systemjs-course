stage('CI') {
    node {

        checkout scm

        // git branch: 'jenkins2-course', 
        //     url: 'https://github.com/g0t4/solitaire-systemjs-course'

        // pull dependencies from npm
        // on windows use: bat 'npm install'
        bat 'npm install'

        // stash code & dependencies to expedite subsequent testing
        // and ensure same code & dependencies are used throughout the pipeline
        // stash is a temporary archive
        stash name: 'everything', 
              excludes: 'test-results/**', 
              includes: '**'

        // test with PhantomJS for "fast" "generic" results
        // on windows use: bat 'npm run test-single-run -- --browsers PhantomJS'
        bat 'npm run test-single-run -- --browsers PhantomJS'

        // archive karma test results (karma is configured to export junit xml files)
        step([$class: 'JUnitResultArchiver', 
              testResults: 'test-results/**/test-results.xml'])

    }   
    
    // new node test
    // node('windows') {
    //     bat 'dir'
    //     bat 'del /S /Q *'
    //     unstash 'everything'
    //     bat 'dir'
    // }
}

stage('Browser Testing') {
    parallel chrome: {
        runTests("Chrome")
    }, firefox: {
        runTests("Firefox")
    }
}

def runTests(browser) {
    node {
        bat 'del /S /Q *'
        unstash 'everything'
        bat "npm run test-single-run -- --browsers ${browser}"
        step([$class: 'JUnitResultArchiver', 
              testResults: 'test-results/**/test-results.xml'])
    }
}

stage('User Confirmation') {
    input 'Deploy to Staging?'
}

stage('Deploy') {
    node {
        bat "echo ^<h1^>${env.BUILD_DISPLAY_NAME}^<^/h1^> >> app/index.html"
        // bat 'docker-compose up -d --build'
        // notify 'Solitaire deployed!'
    }
}