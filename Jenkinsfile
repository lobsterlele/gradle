node {
    def gradleHome = tool 'gradle 5.1'
    git 'https://github.com/lobsterlele/gradle'

    stage('Checkout') {
        checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: true, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/lobsterlele/gradle']]])
    }
    
    stage('Gradle build') {
        // run Gradle to execute compile and unit testing
        sh "'${gradleHome}/bin/gradle' build"
        
    }
    stage('parallelTest') {
        parallel test1: {
            sh "'${gradleHome}/bin/gradle' test1"
            sleep 10
        }, test2: {
            sh "'${gradleHome}/bin/gradle' test2"
            sleep 13
        }, test3: {
            sh "'${gradleHome}/bin/gradle' test3"
            sleep 16
        }
    }
    
    stage('trigerJob') {
        build 'hello'
    }

    stage('package') {
       sh 'tar -cf $JENKINS_HOME/workspace/testPipeJob/pipeline-$BUILD_NUMBER.tar.gz Jenkinsfile'
       
    }
    stage('upload') {
        nexusArtifactUploader artifacts: [[artifactId: 'SomeArt', classifier: '', file: '$JENKINS_HOME/workspace/testPipeJob/pipeline-$BUILD_NUMBER.tar.gz', type: 'tar.gz']], credentialsId: 'logintonexus', groupId: 'Hello', nexusUrl: '172.23.11.47:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'pipe', version: '$BUILD_NUMBER'
    }
       
    stage('Asking for manual approval') {
         timeout(time: 2, unit: "MINUTES") {
         input message: 'Approve Deploy?', ok: 'Yes' 
     }}

       
    stage('Deployment'){
         sh 'wget http://172.23.11.47:8081/repository/pipe/Hello/SomeArt/$BUILD_NUMBER/SomeArt-$BUILD_NUMBER.tar.gz'
         sh 'tar -xf SomeArt-$BUILD_NUMBER.tar.gz'
         
        
    } 
}
