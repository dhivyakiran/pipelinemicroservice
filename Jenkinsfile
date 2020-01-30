node 
{
   deleteDir()
   git branch: 'development', url: 'https://github.com/dhivyakiran/pipelinemicroservice.git'
   mydatas = readYaml file: "pipeline.yml"
}
pipeline 
{
agent
{
   label "${mydatas.agentname}"
}
environment 
{
   envname="${params.envname}"
   servicename="${params.servicename}"

}
stages 
{
   stage('Initialization') 
    {
        steps 
        {
           script 
            {
                if(envname=="dev" )
                {
                  pipelinetype = "build_deploy"
                }
                else if(envname=="int" || envname=="uat" || envname=="qa" || envname=="prod")
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
            dir('service'){
			script
              {
               deleteDir()
               git branch: mydatas.branch, url: mydatas.giturl.cmsservice
               appdata = readYaml file: envname+".yml"
			   sh "cp -R /opt/Jenkins/code/${servicename}/* ."
   
              }
			}  
         }
    }
    stage('Download Dependencies')
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            dir('service'){
			sh 'npm install'
			}
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
    stage("Code Coverage") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            echo "code coverage"
        }
    }*/
    stage("SonarQube Code Analysis") 
    {
        when {expression{(pipelinetype != "deploy") && (mydatas.sonar == "true")}}
        steps 
	     {
           dir('service'){
		   withSonarQubeEnv('sonarqube') 
           { 
		   
             sh "cp ../sonar-${servicename}.properties sonar-project.properties"
			 sh "/opt/Jenkins/sonar-scanner-4.2.0.1873/bin/sonar-scanner"
	        }
        }
		}
     }
     stage("SonarQube Quality Gate") 
     {
        when {expression{(pipelinetype != "deploy") && (mydatas.sonar == "true")}}
        steps 
        {
			dir('service'){
            timeout(time: 30, unit: 'SECONDS') 
            {
               waitForQualityGate abortPipeline: true
            }
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
    /* stage("Docker build") 
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
   }*/
}

