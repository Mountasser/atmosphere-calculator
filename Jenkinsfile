node {
    env.JAVA_HOME="${tool 'JDK8'}"
    
    stage 'Build'
    git url: 'https://github.com/Mountasser/atmosphere-calculator.git'
    def mvnHome = tool "maven-3.2.2"
    sh "${mvnHome}/bin/mvn clean package"
    step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])

    stage 'Build Docker image'
    def image = docker.build('jcsirot/atmo-calc:snapshot', '.')

    stage 'Acceptance Tests'
    image.withRun('-p 8181:8181') {c ->
        sh "${mvnHome}/bin/mvn verify"
    }
    step([$class: 'JUnitResultArchiver', testResults: '**/target/failsafe-reports/TEST-*.xml'])

    stage 'Push Docker image'
    docker.withRegistry("https://registry.hub.docker.com", "docker-registry") {
        image.push()
    }
}
