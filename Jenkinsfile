node('haimaxy-jnlp') {
    stage('Clone') {
        echo "1.Clone Stage"
		checkout scm
        script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
			if (env.BRANCH_NAME != 'main') {
                build_tag = "${env.BRANCH_NAME}-${build_tag}"
        }
    }
	}
    stage('Maven Build') {
                echo "2.Maven2 Build Stage"
                sh 'cd /root/.m2/'
                sh 'whereis .m2'
                sh 'whoami'
                sh 'mvn -B clean package -Dmaven.test.skip=true'
            }

    stage('Build') {
        echo "3.Build Docker Image Stage"
    sh "ls -l"
    sh "docker build -t firexuxiaoman/jenkins-demo:${build_tag} ."
    }
    stage('Push') {
        echo "4.Push Docker Image Stage"
            sh "docker login -u firexuxiaoman -p Pass123456789"
            sh "docker push firexuxiaoman/jenkins-demo:${build_tag}"
        }
		stage('Deploy') {
        echo "5. Deploy Stage"
        if (env.BRANCH_NAME == 'main') {
            input "确认要部署线上环境吗？"
        }
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
        sh "sed -i 's/<BRANCH_NAME>/${env.BRANCH_NAME}/' k8s.yaml"
        sh "kubectl apply -f k8s.yaml --record"
    }
    }
