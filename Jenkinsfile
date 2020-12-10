pipeline {
    agent {
        label "haimaxy-jnlp"
    }
    stages {
        stage('Clone Code') {
            steps {
                echo "1.Git Clone Code"
				checkout scm
            script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
			if (env.BRANCH_NAME != 'main') {
                build_tag = "${env.BRANCH_NAME}-${build_tag}"
        }
    }
        }
		}
        stage('Maven') {
            steps {
                echo "2.Maven Build Stage"
                sh 'mvn -B clean package -Dmaven.test.skip=true'
                sh 'mvn sonar:sonar -Dsonar.host.url=http://192.168.6.150:8050 -Dsonar.login=d6c8fb1706d3d5824db5ea1c9d9101d0b47801eb'
            }
        }
        stage('Image Build') {
            steps {
            echo "3.Image Build Stage"
            sh 'ls -l'
            sh "docker build -t firexuxiaoman/jenkins-demo:${build_tag} ."
            }
        }
        stage('Push') {
            steps {
            echo "4.Push Docker Image Stage"
            sh "docker login -u firexuxiaoman -p Pass123456789"
            sh "docker push firexuxiaoman/jenkins-demo:${build_tag}"
            }
        }
        stage('Deploy') {
		steps {
        echo "6. Deploy Stage"
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
        sh "sed -i 's/<BRANCH_NAME>/${env.BRANCH_NAME}/' k8s.yaml"
		if (env.BRANCH_NAME == 'main') {
            input "确认要部署线上环境吗？"
			}
        sh 'kubectl apply -f k8s.yaml --record'
        }
    }
	}
	}