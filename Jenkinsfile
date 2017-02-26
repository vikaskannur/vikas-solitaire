stage 'CI'
node {
     
      checkout scm
    //git branch: 'jenkins2-course', 
     //   url: 'https://github.com/g0t4/solitaire-systemjs-course'

    // pull dependencies from npm
    // on windows use: bat 'npm install'
    sh 'npm install'

    // stash code & dependencies to expedite subsequent testing
    // and ensure same code & dependencies are used throughout the pipeline
    // stash is a temporary archive
    stash name: 'everything', 
          excludes: 'test-results/**', 
          includes: '**'
    
    // test with PhantomJS for "fast" "generic" results
    // on windows use: bat 'npm run test-single-run -- --browsers PhantomJS'
    sh 'npm run test-single-run -- --browsers PhantomJS'
    
    // archive karma test results (karma is configured to export junit xml files)
    step([$class: 'JUnitResultArchiver', 
          testResults: 'test-results/**/test-results.xml'])
          
}

def notify(status){
    emailext (
      to: "vikaskrishna@gmail.com",
      subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
      body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
        <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>""",
    )
}

node ('master')
{
notify ( "Are you sure we need to deploy?")


}

input 'Are you sure we need to deploy?'
//limit concurrency so we don't perform simulatenous deploys
// and if multiple pipelines are executing,
//newest is only that will be allowed though, rest will be cancelled

stage name: 'Deploy', concurrency: 1
node {
      // write build number to index page so we can see this update
      sh "echo  '<h1>${env.BUILD_DISPLAY_NAME}</h1>' >> app/index.html"
     // deploy to a docker container mapped to port 3000
      sh '/usr/local/bin/docker-machine version;export DOCKER_HOST="unix:///var/run/docker.sock";docker-compose up -d --build'
     notify 'Solitaire Deployed!'
    }
