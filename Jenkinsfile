#!groovy

stage 'Dev'
node {
    checkout scm
    mvn 'clean package'
    dir('target') {stash name: 'war', includes: 'x.war'}
}

stage 'QA'
parallel(longerTests: {
    runTests(30)
}, quickerTests: {
    runTests(20)
})

stage name: 'Staging', concurrency: 1
node {
    deploy 'staging'
}

input message: "Does staging look good?"
try {
    checkpoint('Before production')
} catch (NoSuchMethodError _) {
    echo 'Checkpoint feature available in CloudBees Jenkins Enterprise.'
}

stage name: 'Production', concurrency: 1
node {
    echo 'Production server looks to be alive'
    deploy 'production'
    echo "Deployed to production"
}

def mvn(args) {
    sh "echo maven stuff ${args}"
    sh "id"
    println "pwd".execute().text 
    println "ls -al".execute().text 
    //   println "eco $PATH".execute().text
    // PULL IN ENVIRONMENT VARIABLES
    // Jenkins makes these variables available for each job it runs
    def buildNumber = env.BUILD_NUMBER
    def workspace = env.WORKSPACE
    def buildUrl = env.BUILD_URL
    // PRINT ENVIRONMENT TO JOB
    echo "workspace directory is $workspace"
    echo "build URL is $buildUrl"

    sh "set"
    sh "echo dollar{tool 'Maven 3.x'}/bin/mvn ${args}"
    sh "/home/robin/mvn333/apache-maven-3.3.3/bin/mvn ${args}"
    sh "set"
}

def runTests(duration) {
    node {
        sh "sleep ${duration}"
       } 
    }

def deploy(id) {
    unstash 'war'
    sh "cp x.war /tmp/${id}.war"
}

def undeploy(id) {
    sh "rm /tmp/${id}.war"
}
