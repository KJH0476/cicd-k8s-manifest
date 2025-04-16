pipeline {
    agent any
    parameters {
        string(name: 'baseDir', defaultValue: '.', description: 'Base directory')
        string(name: 'tag', defaultValue: 'latest', description: 'Image tag')
        string(name: 'dockerRepo', defaultValue: 'dockerRepo', description: 'Docker repository')
    }

    stages {
        stage('깃허브 Pull') {
            steps {
                echo "${params.baseDir}"
                echo "${params.tag}"
                echo "${params.dockerRepo}"
                git branch: 'main',
                credentialsId: 'GitHub-Access',
                url: 'https://github.com/KJH0476/cicd-k8s-manifest.git'
            }
        }
        stage('이미지 태그 변경') {
            steps {
                sh """
                    cd ${params.baseDir}
                    sed -i "s/^\\(\\s*tag:\\s*\\).*[\\\$]/\\1${params.tag}/" values.yaml
                    cat values.yaml
                """
            }
        }
        stage('Helm 차트 수정 후 배포') {
            steps {
                withCredentials([
                    usernamePassword(credentialsId: 'GitHub-Access', passwordVariable: 'gitPassword', usernameVariable: 'gitUsername'),
                    string(credentialsId: 'github_email', variable: 'gitEmail')
                ]) {
                    sh """
                        git config user.email ${gitEmail}
                        git config user.name ${gitUsername}
                        git add -A
                        git commit -m '[jenkins] update image tag = ${params.dockerRepo}:${params.tag}'
                        git push https://github.com/${gitUsername}/cicd-k8s-manifest.git
                    """
                }
            }
        }
    }
}