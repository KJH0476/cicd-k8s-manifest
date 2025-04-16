pipeline {
    agent any

    stages {
        stage('깃허브 Pull') {
            steps {
                git branch: 'main',
                credentialsId: 'GitHub-Access',
                url: 'https://github.com/KJH0476/cicd-k8s-manifest.git'
            }
        }
        stage('이미지 태그 변경') {
            steps {
                sh """
                    cd $baseDir
                    sed -i "s/^\\(\\s*tag:\\s*\\).*$/\\1\$tag/" values.yaml
                """
            }
        }
        stage('Helm 차트 수정 후 배포') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'GitHub-Access', passwordVariable: 'gitPassword', usernameVariable: 'gitUsername'), string(credentialsId: 'github_email', variable: 'gitEmail')]) {
                    sh """
                        git config user.email $gitEmail
                        git config user.name $gitUsername
                        git add -A
                        git commit -m '[jenkins] update image tag = $dockerRepo:$tag'
                        git push https://github.com/${gitUsername}/cicd-k8s-manifest.git
                    """
                }
            }
        }
    }
}