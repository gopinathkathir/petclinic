node {
    
    stage 'Checkout'
    git "https://github.com/gopinathkathir/petclinic.git"

    env.PATH = "${tool 'Docker'}/bin:${env.PATH}"
    
    stage 'Build application war file'
    // Build petclinic in a Maven3+JDK8 Docker container
    docker.image('maven:3-jdk-8').inside('-v /.m2:/root/.m2') {
        sh 'mvn -B package -DskipTests'
    }

    stage 'Build application Docker image'
    def appImg = docker.build("mysample/petclinic")

    stage 'Push to Local Registory'
    docker.withRegistry('http://172.16.204.230:5000') {
        appImg.push();
    }

    stage 'Run app on Kubernetes'
    withKubernetes( serverUrl: 'http://172.16.204.230:8080') {
          sh 'kubectl run petclinic --image=172.16.204.230:8080/mysample/petclinic --port=8080'
    }

    // ... Do some tests on deployed application web UI

}
