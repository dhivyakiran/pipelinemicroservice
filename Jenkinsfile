node 
{
   git url: 'https://github.com/dhivyakiran/pipelineportal.git'
   mydatas = readYaml file: "pipeline.yml"
}
pipeline 
{
agent
{
   label "master"
}
stages 
{
    stage('Source code checkout') 
    {
        steps 
        {
           script 
            {
                git branch: mydatas.giturl.branch, url: mydatas.giturl.path
                appdata = readYaml file: "app.yml"
                //echo "Build url:${currentBuild.absoluteUrl}"
             }
         }
     }
    stage('Download Dependencies')
    {
        when {expression{(appdata.env == "dev") || (appdata.env == "int")}}
        steps 
        {
            nodejs(nodeJSInstallationName: 'NodeJS')
            {
            sh 'npm install'
            }
        }
    }
    stage('Zip the app')
    {
        when {expression{(appdata.env == "dev") || (appdata.env == "int")}}    
        steps 
        {
            script
            {
                def artifact = appdata.artifact.size()
                for (int i = 0; i < artifact; i++) 
                {
                    zip archive: true, dir: appdata.artifact[i], zipFile: appdata.artifact[i]+"/"+"${currentBuild.number}/"+appdata.artifact[i]+"_${currentBuild.number}.zip" 
                    nexusArtifactUploader artifacts: [[artifactId: appdata.artifact[i], classifier: '', file: appdata.artifact[i]+"/"+"${currentBuild.number}/"+appdata.artifact[i]+"_${currentBuild.number}.zip", type: 'zip']], credentialsId: 'nexus', groupId: 'portal', nexusUrl: 'ec2-3-15-13-91.us-east-2.compute.amazonaws.com:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'portal', version: '1.1'
                }
             } 
         }
      }
     /* stage('Download the artifact')
      {
         steps
         {
            script
            {
               sh "wget http://ec2-3-15-13-91.us-east-2.compute.amazonaws.com:8081/repository/portal/portal/sales/1.0/sales-1.0.zip -P ./salesportal/" 
            }
         }
      }
      stage('UnZip the app')
      {
         steps 
         {
            script
            {
               unzip dir: './salesportal/', glob: '', zipFile: "sales-1.0.zip"
            } 
          }
       }*/
   }
}
