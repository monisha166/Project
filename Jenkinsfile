pipeline {
  agent any
  stages {
   stage('clone project') {
      steps {
           git branch:'master' , url:'https://github.com/monisha166/Project.git'
       }
   }
   stage('clean project') {
      steps {
           sh 'mvn clean'
       }
   }
   stage('compile') {
      steps {
           sh 'mvn compile'
       }
   }
   stage('test') {
      steps {
           sh 'mvn test'
       }
   }
   stage('build') {
      steps {
           sh 'mvn clean install'
       }
   }
  }
}
