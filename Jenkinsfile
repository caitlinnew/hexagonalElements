@Library('jenkins-cloud-lib') _ // See https://github.boschdevcloud.com/eBike/devops-jenkins-cloud-lib for documentation.

node { sendToMattermostOnError('#cloud-api-notifications') {

    cleanWs()
    checkout scm

    def buildConfig = createBuildConfig([
        team       : 'cloud-api',
        project    : 'specs',
        branchName : env.BRANCH_NAME,
        buildNumber: env.BUILD_NUMBER
    ])

    pullDockerHubImages(images: [
        'node:16-stretch',
    ])

    pullBuildImages(buildConfig: buildConfig)

    withCredentials([usernamePassword(
            credentialsId: 'eb-artifactory-api-key',
            usernameVariable: 'BDC_NPM_USER',
            passwordVariable: 'BDC_NPM_PASS')]) {
        
        withDockerContainer(image: "node:16-stretch") {                                
            stage('OAS lint') {
                sh "curl -u ${BDC_NPM_USER}:${BDC_NPM_PASS} https://artifactory.bes.ebike.dev/artifactory/api/npm/ebike-digital-npm/auth/boschebike -o ~/.npmrc"
                sh "npm ci"                               
                sh "npm run oas-lint"
            }
        }
    }

    // This will create the release version based on package.json release configuration.
    releasedVersion = semanticRelease(buildConfig: buildConfig)
}}