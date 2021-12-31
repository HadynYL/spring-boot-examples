pipeline {
  agent any
  tools {
    maven 'maven-3.6.3'
    jdk 'OpenJDK11U-jdk_x64_linux_hotspot_11.0.11_9'
  }
  environment {
    dockerImage = null
  }
  stages {
    stage('Deploy JAR') {
      steps {
        sh 'mvn clean deploy -DaltDeploymentRepository=leaderrun::default::https://devops.leaderrun.com/nexus/repository/maven-snapshots'
      }
    }
    stage('Build Image') {
      steps {
        script {
          def pom = readMavenPom()
          sh """
            cd ${pom.artifactId}-serv
            mkdir -p target/dependency
            cd target/dependency
            jar -xf ../*.jar
          """
          dockerImage = docker.build "leaderrun/${pom.artifactId}:${pom.version}", "${env.WORKSPACE}/${pom.artifactId}-serv"
        }
      }
    }
    // stage('Push Image') {
    //   steps {
    //     script {
    //       docker.withRegistry('https://devops.leaderrun.com/v2', 'docker-registry') {
    //         dockerImage.push()
    //       }
    //     }
    //   }
    // }
  }
}
