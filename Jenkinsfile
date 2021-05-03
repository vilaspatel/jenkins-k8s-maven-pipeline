pipeline {
  agent {
    kubernetes {
      label 'promo-app'  // all your pods will be named with this prefix, followed by a unique id
      idleMinutes 5  // how long the pod will live after no jobs have run on it
      yamlFile 'build-pod.yaml'  // path to the pod definition relative to the root of our project 
      defaultContainer 'maven'  // define a default container if more than a few stages use it, will default to jnlp container
    }
  }
  environment {
    sonar_login     = credentials('sonar_login')
  }
  stages {
    stage('Build') {
      steps {  // no container directive is needed as the maven container is the default
        sh "echo 'Stage Build started'"
        sh "mvn clean package"   
      }
    }
    stage('sonar-scanner') {
      steps { 
        container('sonar-scanner') {  
        //sh "ls -lh"
        sh "echo 'Stage sonar-scanner started'"
        sh "sonar-scanner -Dsonar.login=$sonar_login -Dsonar.host.url=http://52.190.40.168 -Dsonar.projectKey=sonarqube-test -Dsonar.sources=. -Dsonar.java.binaries=target/demo-1.0-SNAPSHOT.jar -X"   
      }
    }
    }  
    stage('Nexus') {
      steps {  // no container directive is needed as the maven container is the default
        sh "echo 'Stage Nexus started'"
        sh "cp /opt/settings.xml /root/.m2/settings.xml"
        sh "mvn deploy:deploy-file -DrepositoryId=vilas -DgroupId=com.mycompany -DartifactId=demo -Dversion=1.0-SNAPSHOT -Durl=http://40.88.192.216/repository/vilas/ -Dfile=target/demo-1.0-SNAPSHOT.jar -X"   
      }
    }
  }
}
