node 
{
    git url: 'https://github.com/dhivyakiran/helloworld-angular.git'
    mydatas = readYaml file: "app.yml"
}
pipeline 
{
agent
{
    label 'master'
    }
    stages 
{
stage('Read YML file from current repository') 
{
steps 
{
script 
{
     echo "Build url:${currentBuild.absoluteUrl}"
   }
}
}
stage('Download Dependencies')
{
    when {expression{(mydatas.pipeline != "Deploy")}}
    steps 
    {
        nodejs(nodeJSInstallationName: 'NodeJS')
        {
        sh 'npm install'
        }
   }
}
stage('Zip the sales app')
   {
     when {expression{(mydatas.pipeline != "Deploy")}}    
    steps 
    {
     script
    {
       def artifact = mydatas.artifact.size()
       for (int i = 0; i < artifact; i++) 
       {
       zip archive: true, dir: mydatas.artifact[i], zipFile: mydatas.artifact[i]+"/"+"${currentBuild.number}/"+mydatas.artifact[i]+"_${currentBuild.number}.zip" 
       
       }
      } 
    }
   }
}
}
