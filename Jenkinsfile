pipeline {
  agent any
  stages {
    stage('Pull') {
      steps {
        git(url: 'https://github.com/ssrksiva/testpipeline9.git', branch: 'master', poll: true)
      }
    }

    stage('Check Java') {
      steps {
        sh 'java -version'
      }
    }

    stage('Clean') {
      steps {
        sh 'chmod +x mvnw'
        sh './mvnw -ntp clean -P-webpack'
      }
    }

    stage('install tools') {
      steps {
        sh './mvnw -ntp com.github.eirslett:frontend-maven-plugin:install-node-and-npm -DnodeVersion=v10.16.0 -DnpmVersion=6.9.0'
      }
    }

    stage('npm install') {
      steps {
        sh './mvnw -ntp com.github.eirslett:frontend-maven-plugin:npm'
      }
    }

    stage('backend test') {
      steps {
        sh './mvnw -ntp verify -P-webpack'
      }
    }

    stage('front end') {
      steps {
        sh './mvnw -ntp com.github.eirslett:frontend-maven-plugin:npm -Dfrontend.npm.arguments=\'run test\''
      }
    }

    stage('packaging') {
      steps {
        sh './mvnw -ntp verify -P-webpack -Pprod -DskipTests'
        archiveArtifacts(fingerprint: true, artifacts: '**/target/*.jar')
      }
    }

    stage('Sonar') {
      steps {
        withSonarQubeEnv('My SonarQube Server') {
          sh './mvnw -ntp sonar:sonar'
        }

      }
    }
	stage('Deliver for Development Environment') {
      
      steps {
	    sh './mvnw -ntp verify -P-webpack -Pprod -DskipTests && ./mvnw wildfly:deploy-only -Pstandalone-mode -Dwildfly.hostname=wildfly1.wildfly-khw2r.10.4.48.114.xip.io -Dwildfly.port=9990 -Dwildfly.username=admin -Dwildfly.password=admindev12345'
        
        }

      }
}
    
}