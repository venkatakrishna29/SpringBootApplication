def tag, containerName, httpPort = ""
def registryUrl = "azurewebappregistry.azurecr.io"
node(){
    stage('Prepare Environment'){
        echo 'Initialize Environment'
        tag="latest"
	containerName="backendapp"
	httpPort="8989"
    }
    
    stage('Code Checkout'){
        try{
            checkout scm
        }
        catch(Exception e){
            echo 'Exception occured in Git Code Checkout Stage'
            currentBuild.result = "FAILURE"
        }
    }
	
    stage('Maven Build'){
        sh "mvn clean package"        
    }
	
    stage('Docker Image Build'){
        echo 'Creating Docker image'
        sh "docker build -t $registryUrl/$containerName:$tag --pull --no-cache ."
    }  

    stage('Publishing Image to ACR'){
        echo 'Pushing the docker image to ACR'
        withCredentials([usernamePassword(credentialsId: 'dockerACRAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
		sh "docker login -u $dockerUser -p $dockerPassword $registryUrl"
		sh "docker push $registryUrl/$containerName:$tag"
		echo "Image push complete"
        } 
    }
}
