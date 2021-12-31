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
        sh 'mvn clean deploy -DaltDeploymentRepository=rdc-snapshots::default::https://packages.aliyun.com/maven/repository/2040963-snapshot-adtACS/
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
          dockerImage = docker.build "alemer/${pom.artifactId}:${pom.version}", "${env.WORKSPACE}/${pom.artifactId}-serv"
        }
      }
    }
    stage('Push Image') {
      steps {
        script {
          docker.withRegistry('https://registry.cn-hangzhou.aliyuncs.com/', '7860bb7e-4d64-4d20-9947-85d5e92564c7') {
            dockerImage.push()
          }
        }
      }
    }
  }
}
