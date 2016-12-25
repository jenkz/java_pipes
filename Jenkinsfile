node('Linux'){
  try{
      def mvnHome = tool 'M3'
      env.PATH = "${mvnHome}/bin:${env.PATH}"

  stage name: 'Git Clone', concurrency: 1
      checkout([$class: 'GitSCM',
      branches: [[name: '*/master']],
      doGenerateSubmoduleConfigurations: false,
      extensions: [], submoduleCfg: [],
      userRemoteConfigs: [[credentialsId: 'jenks', url: 'git@github.com:RocketScienceProjects/BlueKing.git']]])

  stage name: 'Test & Scan', concurrency: 1
//  parallel Test_Publish: {
    try{

      withSonarQubeEnv {
       sh 'mvn verify sonar:sonar'
          }
     }
     catch(err){
       sh 'echo "Test have a FAILURE"'
       throw err
       currentBuild.result = 'FAILURE'
     } finally {
       junit '**\\target\\surefire-reports\\*.xml'
     }

  //stage name: 'Code_Scan', concurrency: 1
  // }, Code_Scan: {
    //withSonarQubeEnv {
      //  sh 'mvn verify sonar:sonar' //-Dsonar.login=jenkins -Dsonar.password=Letmein
      // }
  //},
  //  failFast: true

  // stage name: 'Deploy To Lab', concurrency: 1
  //   def p =  pwd()
  //   println p
  //   sh 'ls -al'
  //   def tomcatStatus = "$p/deploy.sh".execute().text


  }

  catch(err){
    stage 'Send Email Notification'
      emailext(to: "nirish.okram@gmail.com",
        subject: "Jenkins Build ${JOB_NAME} ${BUILD_NUMBER} Failed...",
        body: "<p>Hi,<br>Jenkins Job Link :  ${env.BUILD_URL}<br> <strong>Failed with Error</strong>: <i>${err}</i></p>  <p>Thanks<br>TestAdmin<br></p>",
        mimeType: 'text/html');

        throw err
       currentBuild.result = 'FAILURE'
  }

}
