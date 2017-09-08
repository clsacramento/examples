node {
    
    environment {
        http_proxy = 'http://proxy.sdc.hp.com:8080/'
        https_proxy ='http://proxy.sdc.hp.com:8080/'
    }

    checkout scm

    sh "git rev-parse --short HEAD > commit-id"

    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    appName = "admin/gb-frontend"
    registryHost = "localhost:5000/"
    imageName = "${registryHost}${appName}:${tag}"
    env.BUILDIMG=imageName

    stage "Build"
    
        sh "docker build -t ${imageName} -f guestbook/php-redis/Dockerfile guestbook/php-redis"
    
    stage "Push"

        sh "docker push ${imageName}"

    stage "Deploy"

        sh "sed 's#localhost:5000/admin/gb-frontend:latest#'$BUILDIMG'#' guestbook/frontend-deployment.yaml | kubectl apply -f -"
        sh "kubectl rollout status deployment/frontend"
