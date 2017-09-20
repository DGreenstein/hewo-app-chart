node(){
    checkout scm
    build job: "helloworldapp", wait: true
    container('kubectl') {
        stage('Deploy new Docker Image') {
            sh """
            #!/bin/bash
            set +e
            NAMESPACE=`cat /var/run/configs/registry-config/namespace`
            REGISTRY=`cat /var/run/configs/registry-config/registry`
            DEPLOYMENT=`kubectl get deployments | grep hewo | grep web | awk '{print \$1}'`

            kubectl get deployments \${DEPLOYMENT}

            if [ \${?} -ne "0" ]; then
                kubectl create -f deployment.yaml
            fi

            # Update Deployment
            kubectl set image deployment/\${DEPLOYMENT} web=\${REGISTRY}/\${NAMESPACE}/bluecompute-ce-web:${env.BUILD_NUMBER}
            kubectl rollout status deployment/\${DEPLOYMENT}
            """
        }
    }
}
    