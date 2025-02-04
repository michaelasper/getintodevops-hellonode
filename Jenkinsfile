node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */

        app = docker.build("kllrshrk/getintodevops-hellonode")
    }

    stage('Twistlock Scan') {
        /* We test our image with a simple smoke test:
         * Run a curl inside the newly-build Docker image */
        
        twistlockScan ca: '', cert: '', compliancePolicy: 'warn', containerized: false, dockerAddress: 'unix:///var/run/docker.sock', gracePeriodDays: 0, ignoreImageBuildTime: false, image: 'kllrshrk*', key: '', logLevel: 'true', policy: 'warn', requirePackageUpdate: false, timeout: 10
    }
    
    stage('Twistlock Publish') {
        twistlockPublish ca: '', cert: '', dockerAddress: 'unix:///var/run/docker.sock', image: 'kllrshrk*', key: '', logLevel: 'true', timeout: 10
    }

    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}
