podTemplate(label: 'mypod',
    volumes: [
        hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock'),
        secretVolume(secretName: 'registry-account', mountPath: '/var/run/secrets/registry-account'),
        configMapVolume(configMapName: 'registry-config', mountPath: '/var/run/configs/registry-config')
    ],
    containers: [
        containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl', ttyEnabled: true, command: 'cat'),
        containerTemplate(name: 'docker' , image: 'docker:17.06.1-ce', ttyEnabled: true, command: 'cat')
  ]) {

    node('mypod') {
        checkout scm
        build job: "helloworldapp", wait: true
        container('kubectl') {
            stage('Deploy new Docker Image') {
                sh """
                #!/bin/bash
                set +e
                NAMESPACE=`cat /var/run/configs/registry-config/namespace`
                REGISTRY=`cat /var/run/configs/registry-config/registry`
                DEPLOYMENT=`kubectl get deployments | grep Hel | grep web | awk '{print \$1}'`

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
}