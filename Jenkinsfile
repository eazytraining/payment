pipeline {
	environment {
        DOCKER_ID = "eazytraining"
        IMAGE_NAME = "payment"
        IMAGE_TAG = "${BUILD_NUMBER}"
	}
agent any
stages{
    stage('Cloner le code source') {
					steps {
						script {
									sh '''
										git clone git@github.com:eazytraining/meetup-paris-payment.git
									'''
								}
						}
				}
	stage ('Build de notre Image Docker'){
						steps {
								script {
									sh '''
									     cd meetup-paris-payment
                                         docker build -t $DOCKER_ID/$IMAGE_NAME:$IMAGE_TAG .
										 cd ..
										 rm -Rf meetup-paris-payment
									'''
								}
						}
					}
    	stage ('Docker Push sur dockerhub'){
					steps {

							script {
								sh '''
								   docker login -u $DOCKER_ID -p $DOCKER_PASSWORD
								   docker push $DOCKER_ID/$IMAGE_NAME:$IMAGE_TAG
								   docker rmi $DOCKER_ID/$IMAGE_NAME:$IMAGE_TAG
								'''
							}
					}
			}

    stage('Mis à Jour des manifests pour ARGO') {
					 steps {
						script {
								   sh '''
									git clone git@github.com:eazytraining/meetup-paris-deployment-payment.git
									cd meetup-paris-deployment-payment
									rm -Rf .git
									git init
									git config user.email eazytraining@gmail.com
									git config user.name eazytraining
									cat values.yaml"
									sed -i 's+tag.*+tag: "${IMAGE_TAG}"+g' values.yaml
									cat values.yaml
									git add .
									git remote add origin git@github.com:eazytraining/meetup-paris-deployment-payment.git
									git commit -m "Valeur de l'image mise à jour par Jenkins: ${IMAGE_TAG}"
									git push origin master
									cd ..
									rm -Rf meetup-paris-deployment-payment
				  '''
				  
				}
			  }
		}
	}
}