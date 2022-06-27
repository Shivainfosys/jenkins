pipeline {
    agent any
     tools {nodejs "14.15.5"}s
      /*environment {
         PATH='/usr/local/bin:/usr/bin:/bin'
      }*/
    stages{
       
        stage('Pull Branch Prod') {
        steps {
            git branch: 'master',
                credentialsId: '6JS3z3xzpUs3Nz6xJA8D',
                url: 'https://anik-digispice:6JS3z3xzpUs3Nz6xJA8D@bitbucket.org/anik-digispice/super-app-master.git'

            sh "ls -lat"
        }
    }

    stage('NPM Install') {
        steps{
            sh 'pwd'
            sh 'npm install'
            sh 'npm install i -g @angular/cli'
            sh 'node --version'
        }
        /*}*/
    }

    stage('Build') {
         steps{
        milestone(20)
        sh 'ng build --prod --aot --outputHashing=all'
         }
    }

    stage('Archive') {
         steps{
        sh 'tar -cvzf dist.tar.gz --strip-components=1 dist'
        archive 'dist.tar.gz'

         }
    }

    stage('Deploy') {
         steps{
        sh 'sudo scp dist.tar.gz digiusr@192.168.94.28:/tmp'
        sh "sudo sshpass -p 'digi@2022' ssh digiusr@192.168.94.28 '/var/www/html/mysh.sh'"
         }
    }
    }
}

