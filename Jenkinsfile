def BLUE_CONTAINER_NAME="blue-container"
def GREEN_CONTAINER_NAME="green-container"
def BLUE_CONTAINER_NAME_TAG="babu479/blue_demo_application"
def GREEN_CONTAINER_NAME_TAG="babu479/green_demo_application"
def CONTAINER_TAG="1.0.1"
def DOCKER_HUB_USER="babu479"
def HTTP_PORT="8080"

node('') {
stage ('Initialize') {
        def mavenHome  = tool 'maven'
        env.PATH = "${mavenHome}/bin:${env.PATH}"
}
stage ('Clone') {
dir("blue"){
git branch: 'blue',
credentialsId: 'ae6c9066a-410c-4462-b2ed-56004c6c20ab',
url: 'https://github.com/babu479/Demo-Application.git'
}
}
stage ('Build') {
sh """
cd $WORKSPACE/blue
mvn clean install"""
}
stage ("Image Prune") {
    imagePrune(BLUE_CONTAINER_NAME)
}
stage('Image Build'){
        imageBuild(BLUE_CONTAINER_NAME,CONTAINER_TAG)
    }
    stage('Push to Docker Registry'){
        withCredentials([usernamePassword(credentialsId: 'DockerHubAccount', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
            pushToImage(BLUE_CONTAINER_NAME, CONTAINER_TAG, USERNAME, PASSWORD)
        }
    }

 def imagePrune(blueContainerName){
    try {
        sh """
        docker stop $blueContainerName
        docker image prune -f
        """
    } catch(error){

    }
}
def imageBuild(blueContainerName,tag){
    sh """
    cd $WORKSPACE/blue
    cp $WORKSPACE/blue/target/*.war $WORKSPACE/blue
	docker build -t $blueContainerName:$tag  -t $blueContainerName --pull --no-cache ."""
}

def pushToImage(blueContainerName, tag, dockerUser, dockerPassword){
    sh """docker login -u $dockerUser -p $dockerPassword
    docker tag $blueContainerName:$tag $dockerUser/$blueContainerName:$tag
    docker push $dockerUser/$blueContainerName:$tag """
    
    echo "Image push complete"
	}
