pipeline {
	environment {
        DOCKER_ID = "eazytraining"
        IMAGE_TAG = "${BUILD_NUMBER}"
	REPOSITORY_NAME = "payment"	
	}
	
agent {
    node {
        label 'docker-agent-meetup'
        customWorkspace '/home/jenkins/pipeline/'
    }
}
stages{
	stage ('Build de notre Image Docker'){
		    steps {
					script {
						sh '''
						mvn clean install
                            			docker build -t $DOCKER_ID/$REPOSITORY_NAME:$IMAGE_TAG .
						'''
					}
		    }
	}
    	stage ('Docker Push sur dockerhub'){
		    steps {

					script {
						sh '''
						   docker login -u $DOCKER_ID -p $DOCKER_PASSWORD
						   docker push $DOCKER_ID/$REPOSITORY_NAME:$IMAGE_TAG
						   docker rmi $DOCKER_ID/$REPOSITORY_NAME:$IMAGE_TAG
						'''
					}
		    }
	}
    stage('Mise à Jour des manifests pour ARGO') {
					 steps {
						script {
					   sh '''
						git clone git@github.com:eazytraining/deployment-${REPOSITORY_NAME}.git
						cd deployment-${REPOSITORY_NAME}
						cat values.yaml
						sed -i "s+tag.*+tag: ${IMAGE_TAG}+g" values.yaml
						cat values.yaml
						git add .
						git config user.email eazytraining@gmail.com
						git config user.name eazytraining
						git commit -m "Valeur de l'image mise à jour par Jenkins: ${IMAGE_TAG}"
						git push origin master
						cd ..
						rm -Rf deployment-${REPOSITORY_NAME}
				  '''
				}
			  }
		}
}
}
