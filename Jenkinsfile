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
       sh 'mv clean package' //intensional invoke failure
     }
     catch(err){
       sh 'echo "Test have a FAILURE"'
       throw err
       currentBuild.result = 'FAILURE'
     } finally {
       junit '**\\target\\surefire-reports\\*.xml'
     }

  stage name: 'Code_Scan', concurrency: 1
  // }, Code_Scan: {
    withSonarQubeEnv {
        sh 'mvn verify sonar:sonar' //-Dsonar.login=jenkins -Dsonar.password=Letmein
      }
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
      mail (subject: "Jenkins Build Failed with ${err.message}",
  to: "nirish.okram@gmail.com",
  body: "Hi,\n \nJenkins Job Link :  ${env.BUILD_URL} \n \n Failed: ${err}  \n\n Thanks \n TestAdmin",
  mimeType: 'text/plain')
      throw err
      currentBuild.result = 'FAILURE'
  }

}
