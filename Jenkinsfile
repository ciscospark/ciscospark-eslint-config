ansiColor('xterm') {
  timestamps {
    timeout(10) {
      node('SPARK_JS_SDK_VALIDATING'){

        def cleanup = { ->
          // cleanup can't be a stage because it'll throw off the stage-view on the
          // job's main page
        
          // ARCHIVE RELEVANT TEST REPORTS
        
          if (currentBuild.result != 'SUCCESS') {
            withCredentials([usernamePassword(
              credentialsId: '386d3445-b855-40e4-999a-dc5801336a69',
              passwordVariable: 'GAUNTLET_PASSWORD',
              usernameVariable: 'GAUNTLET_USERNAME'
            )]) {
              sh "curl -i --user ${GAUNTLET_USERNAME}:${GAUNTLET_PASSWORD} -X PUT 'https://gauntlet.wbx2.com/api/queues/ciscospark-eslint-config/master?componentTestStatus=failure&commitId=${GIT_COMMIT}'"
            }
          }
        }
        try {
          stage('checkout') {
            checkout scm
          }

          stage('install') {
            withCredentials([
              string(credentialsId: 'NPM_TOKEN', variable: 'NPM_TOKEN')
            ]) {
              sh 'npm install'
            }
          }

          stage('test') {
            sh 'ls'
            echo 'it works'
          }

          cleanup()
        } 
        catch (err) {
          // Sometimes an exception can get thrown without changing the build result
          // from success. If we reach this point and the result is not UNSTABLE, then
          // we need to make sure it's FAILURE
          if (currentBuild.result != 'UNSTABLE') {
            currentBuild.result = 'FAILURE'
          }
          cleanup()
          throw error
        }
      }
    }
  }
}