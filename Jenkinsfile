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
			   if(envname != "dev")
			   {
					appdata = readYaml file: envname+".yml"
				}
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
    stage("TS Linting") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
           dir('service'){
		   sh 'npm run lint'
		   }
        }
    }
   /* stage("Unit Testcase") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
            dir('service'){
			 sh 'npm run test'
			 }
        }
    }
    stage("Code Coverage") 
    {
        when {expression{(pipelinetype != "deploy")}}
        steps 
        {
              dir('service'){
			sh "npm run test:cov"
			}
        }
    }*/
    stage("SonarQube Code Analysis") 
    {
        when {expression{(pipelinetype != "deploy") && (mydatas.sonar == true)}}
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
        when {expression{(pipelinetype != "deploy") && (mydatas.sonar == true)}}
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
        when {expression{(pipelinetype != "deploy") && (mydatas.checkmarx == true)}}
        steps 
        {
            echo "security scan"
        }
     }
     stage("Docker build") 
       {
        when {expression{(pipelinetype != "deploy")}} 
        steps 
        {
           
		   dir('service'){
		   sh "docker build -t '${mydatas.ecrrepo.nonprod}:${servicename}-${currentBuild.number}' ."
		   }
        }
     }
	 stage("Docker push") 
       {
        when {expression{(pipelinetype != "deploy")}} 
        steps 
        {
           
		   sh "aws ecr get-login --registry-id '${mydatas.ecrregistry.nonprod}' --region '${mydatas.ecrregistry.nonprodregion}' | xargs xargs"
		   sh "docker push '${mydatas.ecrrepo.nonprod}:${servicename}-${currentBuild.number}'"
		   sh "docker rmi '${mydatas.ecrrepo.nonprod}:${servicename}-${currentBuild.number}'"
		  
        }
		}
     
   }
 
  /* post 
   {
      always 
      {
         emailext attachLog: true, body: "${currentBuild.result}: ${currentBuild.absoluteUrl}", compressLog: true, replyTo: 'dhivya.krish15@gmail.com', subject: "${currentBuild.result} pipeline: ${currentBuild.fullDisplayName}", to: 'dhivyakrish1491@gmail.com'
      }
   }*/
}

