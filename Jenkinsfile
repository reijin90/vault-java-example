def projectProperties = [
    [$class: 'BuildDiscarderProperty',strategy: [$class: 'LogRotator', numToKeepStr: '3']],
]
properties(projectProperties)
pipeline {
  agent any
  //tools {
      //maven 'maven-3.2.5'
      //jdk 'jdk1780'
  //}
  stages { 
    stage('Cleanup') {
      steps {
        withMaven(maven: 'maven-3.2.5') {
          sh 'mvn clean'
        }
        
      }
    }
    stage('Test') {
      steps {
        withMaven(maven: 'maven-3.2.5') {
          sh 'echo Crazy good test'
        }
        
      }
    }
    stage('Compile') {
      steps {
        withMaven(maven: 'maven-3.2.5') {
          sh 'mvn compile'
        }
        
      }
    }
    stage('Package') {
      steps {
        withMaven(maven: 'maven-3.2.5') {
          sh 'mvn package -U'
        }
        
      }
    }
    stage('Notify') {
      steps {
        echo 'Build Successful!'
      }
    }
    stage('Integration Tests') {
      steps {
      sh 'curl -o vault.zip https://releases.hashicorp.com/vault/1.1.3/vault_1.1.3_linux_amd64.zip ; yes | unzip vault.zip'
        withCredentials([string(credentialsId: 'vault-access-token-string', variable: 'VAULT_TOKEN')]) {
        sh '''
          set -x
          #curl https://raw.githubusercontent.com/ncorrare/vault-java-example/master/ca.crt > ca.crt
          #export VAULT_CACERT=$(pwd)/ca.crt
          export ROLE_ID=67bbcf2a-f7fb-3b41-f57e-88a34d9253e7
          export VAULT_ADDR=http://192.168.56.105:8200
          export SECRET_ID=$(./vault write -field=secret_id -f auth/approle/role/java-example/secret-id)
          export VAULT_TOKEN=$(./vault write -field=token auth/approle/login role_id=${ROLE_ID} secret_id=${SECRET_ID})
          #keytool -import -trustcacerts -file ca.crt -alias CorrarelloCA -keystore cacerts -noprompt -keypass changeit -storepass changeit
          java -D"javax.net.ssl.trustStore=./cacerts" -jar target/java-client-example-1.0-SNAPSHOT-jar-with-dependencies.jar 
        '''
        }
      }
    }
  }
  post { 
    always { 
        cleanWs()
    }
  }
  environment {
    mvnHome = 'maven-3.2.5'
  }
}
