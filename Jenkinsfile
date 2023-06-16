template = '''
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: packer
  name: packer
spec:
  containers:
  - command:
    - sleep
    - "3600"
    image: hashicorp/packer
    name: packer
    '''

def buildNumber = env.BUILD_NUMBER

properties([
    parameters([
        choice(choices: ['us-east-1', 'us-east-2', 'us-west-1', 'us-west-2'], name: 'region')
        ])
        ])
if ( env.BRANCH_NAME == "main" ) {
    region == "us-east-1"
}

else if ( env.BRANCH_NAME == "qa") {
    region = "us-east-2"
}
else if ( env.BRANCH_NAME == "dev") {
    region = "us-east-2"

}
    region = "us-west-2"
podTemplate(cloud: 'kubernetes', label: 'packer', showRawYaml: false, yaml: template) {
    node("packer"){
        container("packer"){

        withCredentials([usernamePassword(credentialsId: 'aws-creds', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
            withEnv(["AWS_REGION=${region}"]) {
            
            stage("Git Clone"){
                git branch: 'main', url: 'https://github.com/Tolypbayev7/packer.pkr.hcl.git'
            }
            
            stage("Packer"){
                sh "packer build -var 'jenkins_build_number=${buildNumber}' packer.pkr.hcl"
            }
            }
        }
        }
    }
}
