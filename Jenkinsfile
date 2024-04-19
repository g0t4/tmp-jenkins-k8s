podTemplate(
yaml: '''
spec:
    terminationGracePeriodSeconds: 5
''',
containers: [
  containerTemplate(name: 'mavenz', image: 'maven', command: 'sleep', args: 'infinity')
],
volumes: [persistentVolumeClaim(claimName: 'maven-local-repo', mountPath: '/root/.m2/repository')]
) {
    node(POD_LABEL){
        stage("checkout") {
            sh 'pwd'
            sh 'ls -al'
            git branch: 'forked-start', url: 'https://github.com/g0t4/tmp-jenkins-k8s'
        }
        stage("build") {
            container("mavenz"){
                sh 'ls -al /root/.m2/ || true'
                sh 'env && ls -al'
                sh './mvnw package'
            }
        }
        stage("capture"){
            archiveArtifacts '**/target/*.jar'
            junit '**/target/surefire-reports/TEST*.xml'
            //jacoco() // https://plugins.jenkins.io/jacoco/
            recordCoverage(tools: [[parser: 'JACOCO']], id: 'jacoco', name: 'JaCoCo Coverage') // https://plugins.jenkins.io/coverage/
        }
    }
}
