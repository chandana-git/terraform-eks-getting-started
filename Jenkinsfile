node {
stage('SCM Checkout')
{
git 'https://github.com/chandana-git/terraform-eks-getting-started.git'
}
    stage('install IAM authenticator'){
        sh """
	curl -o aws-iam-authenticator https://amazon-eks.s3-us-west-2.amazonaws.com/1.14.6/2019-08-22/bin/linux/amd64/aws-iam-authenticator
	chmod +x ./aws-iam-authenticator
	sudo cp ./aws-iam-authenticator /usr/bin/aws-iam-authenticator
	export PATH=/usr/bin:$PATH
	echo 'export PATH=/usr/bin:$PATH' >> ~/.bashrc
	source ~/.bashrc
	aws-iam-authenticator --help
				"""
    }
        stage('install kubectl'){
        sh """
curl -o kubectl https://amazon-eks.s3-us-west-2.amazonaws.com/1.14.6/2019-08-22/bin/linux/amd64/kubectl
	chmod +x ./kubectl
	sudo mkdir -p /usr/bin
	sudo cp ./kubectl /usr/bin/kubectl
	export PATH=/usr/bin:$PATH
	echo 'export PATH=/usr/bin:$PATH' >> ~/.bashrc
	source ~/.bashrc
	kubectl version --short --client
				"""
    }
stage("TF"){
     def tfHome = tool name: 'TF_PATH', type: 'terraform'
     //sh "${tfHome}/terraform destroy -auto-approve"
     //sh "${tfHome}/terraform apply -auto-approve"
     sh "${tfHome}/terraform init"
     sh "${tfHome}/terraform plan"
     if (fileExists('$HOME/.kube')) {
					echo '.kube Directory Exists'
				} else {
				sh 'mkdir -p $HOME/.kube'
				}
				sh """
					${tfHome}/terraform apply -auto-approve
					${tfHome}/terraform output kubeconfig > $HOME/.kube/config
				"""
				sh 'sudo chown $(id -u):$(id -g) $HOME/.kube/config'
				sleep 30
				sh 'kubectl get nodes'
				
     
     //sh "${tfHome}/terraform output kubeconfig > ~/.kube/config"
     //withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws-cred', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
    //sh 'aws eks --region us-west-2 update-kubeconfig --name terraform-eks-demo'
   // }
   //withCredentials([kubeconfigFile(credentialsId: 'kube_config', variable: 'KUBECONFIG')]){
     sh "${tfHome}/terraform output config_map_aws_auth > configmap.yml"
     
    sh 'kubectl apply -f deploy.yml'
    sh 'kubectl apply -f configmap.yml'
    sh 'kubectl apply -f svc.yml'
    sh 'kubectl expose deployment webapp-deploy --type=LoadBalancer'
    sleep 30
    sh 'kubectl get service'    
     }

}
