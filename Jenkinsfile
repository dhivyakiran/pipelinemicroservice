node 
{
   git branch: 'development', url: 'https://github.com/dhivyakiran/pipelinemicroservice.git'
   mydatas = readYaml file: "pipeline.yml"
}
pipeline 
{
agent
{
   label "slave1"
}
environment 
{
   envname="${params.envname}"

}
stages 
{
   stage('Environment Initialization') 
    {
        steps 
        {
           script 
            {
                if(envname=="dev" || envname=="int")
                {
                  pipelinetype = "build_deploy"
                }
                else if(envname=="uat" || envname=="qa" || envname=="prod")
                {
                  pipelinetype = "deploy"
                }
                else
                {
                  pipelinetype = "build"
                }
               //echo "Build url:${currentBuild.absoluteUrl}}"
             }
         }
     }
    stage("Source code checkout") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            script
              {
               deleteDir()
               git branch: mydatas.microservice1.branch, url: mydatas.microservice1.path
               appdata1 = readYaml file: envname+".yml"
   
              }
         }
    }
    stage('Download Dependencies')
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            sh 'npm install'
        }
    }
   /* stage("TS Linting") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            echo "Linting"
        }
    }
    stage("Unit Testcase") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            echo "Execute unit tests"
        }
    }
    stage("Sonar Code Coverage") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            echo "code coverage"
        }
    }*/
    stage("SonarQube code analysis") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
	     {
           withSonarQubeEnv('sonarqube') 
           { 
             sh "/opt/Jenkins/sonar-scanner-4.2.0.1873/bin/sonar-scanner"
	        }
        }
     }
    /* stage("SonarQube Quality Gate") 
     {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            timeout(time: 10, unit: 'MINUTES') 
            {
               waitForQualityGate abortPipeline: true
            }
        }
     }
     stage("Security scan") 
     {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            echo "security scan"
        }
     }
     stage("Docker build") 
       {
        when {expression{(pipelinetype != "build")}} 
        steps 
        {
           sh 'docker build -t hellonodejs1:latest .'
        }
     }
   }*/
}
  /* post 
   {
      always 
      {
         emailext attachLog: true, body: "${currentBuild.result}: ${currentBuild.absoluteUrl}", compressLog: true, replyTo: 'dhivya.krish15@gmail.com', subject: "${currentBuild.result} pipeline: ${currentBuild.fullDisplayName}", to: 'dhivyakrish1491@gmail.com'
      }
   }
}*/

