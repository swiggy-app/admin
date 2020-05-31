@Library('pipeline-library-demo')_
def parallelJob( String item1){
    def testList = ["a", "b", "c", "d"]
    def branches = [:] 
    for (int i = 0; i < 10 ; i++) {
       int index=i, branch = i+1
       stage ("branch_${branch}"){ 
            branches["branch_${branch}"] = { 
                node ('maven-label'){
                    sh "echo 'node: ${NODE_NAME},  index: ${index}, i: ${i}, testListVal: " + testList[index] + "'"
                
                        writeFile file: "/tmp/branch_${branch}.txt", text: "This file is useful, need to archive it."
                }
            }
      }
}

parallel branches
}

def sayHello1(String textmessage){
       echo "HI $textmessage"
}
node("maven-label") {
   def mvnHome
  
   stage("custom-method"){
       sayHello1 "Swiggy DevOps team"
   }
   stage("shared-lib"){
       sayHello "devops"
   }
   stage('Preparation') { 
      git branch: 'dev', url: 'https://github.com/swiggy-app/admin.git'
      mvnHome = tool 'maven'
   }
   stage('Build') {
      
      withEnv(["MVN_HOME=$mvnHome"]) {
         if (isUnix()) {
            sh '"$MVN_HOME/bin/mvn" -Dmaven.test.failure.ignore clean install'
         } else {
            bat(/"%MVN_HOME%\bin\mvn" -Dmaven.test.failure.ignore clean package/)
         }
      }
   }
   stage('Results') {
      junit '**/target/surefire-reports/TEST-*.xml'
      archiveArtifacts 'target/*.jar'
   }
   stage("parallel1"){
       parallelJob "array"
   } 
   
}
