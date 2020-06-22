node {
stage('SCM Checkout')
{
git 'https://github.com/chandana-git/terraform-eks-getting-started.git'
}
stage("TF"){
     def tfHome = tool name: 'TF_PATH', type: 'terraform'
     
     sh "${tfHome}/terraform init"
     sh "${tfHome}/terraform plan"
     sh "${tfHome}/terraform apply -auto-approve"
     sh "${tfHome}/terraform output kubeconfig > ~/.kube/config"
     sh "${tfHome}/terraform output config_map_aws_auth > configmap.yml"
     
    
    sh 'kubectl get nodes'
    
    sh 'kubectl apply -f configmap.yml'
    sh 'kubectl create -f deploy.yml'
    sh 'kubectl create -f svc.yml'
    sh 'kubectl get service'     
    
     }
}
