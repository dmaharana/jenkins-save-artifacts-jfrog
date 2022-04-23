node('master') {
	file_name = "hello.txt"
	artifact_path = "${JOB_BASE_NAME}_${BUILD_NUMBER}"

	try {
		stage('build') {
			sh "echo 'Hello World!' > ${file_name}"
		}

		stage('archive') {
			echo "Artifact path: ${artifact_path}"
			sh 'ls -rtl'
			// archiveArtifacts(artifacts: '**/*.txt', followSymlinks: false)
			PutObject(artifact_path, file_name)
			sh "rm ${file_name}"

			GetObject(artifact_path, file_name)
			sh 'ls -rtl'

		}
	}
	catch(Exception e) {
		error "Job failed"
	} finally {
		DeleteObject(artifact_path, file_name)

		cleanWs(cleanWhenNotBuilt: false,
                    deleteDirs: true,
                    disableDeferredWipeout: true,
                    notFailBuild: true)
	}


}


/*
	Artifact PUT, GET & DELETE methods
*/

void PutObject(file_path, file_name) {
	expected_response_code = '201'
	token = credentials('JFROG_JENKINS_ARCHIVE_TOKEN')

	httpRequest customHeaders: [[maskValue: true, name: 'X-JFrog-Art-Api', value: token]], httpMode: 'PUT', ignoreSslErrors: true, responseHandle: 'NONE', uploadFile: file_name, url: "${JV_ARTIFACT_REPO}/${file_path}/${file_name}", validResponseCodes: expected_response_code, wrapAsMultipart: false
}

void GetObject(file_path, file_name) {
	expected_response_code = '200'
	token = credentials('JFROG_JENKINS_ARCHIVE_TOKEN')

	httpRequest customHeaders: [[maskValue: true, name: 'X-JFrog-Art-Api', value: token]], ignoreSslErrors: true, responseHandle: 'NONE', url: "${JV_ARTIFACT_REPO}/${file_path}/${file_name}", validResponseCodes: expected_response_code, outputFile: file_name, wrapAsMultipart: false
}

void DeleteObject(file_path, file_name) {
	expected_response_code = '204'
	token = credentials('JFROG_JENKINS_ARCHIVE_TOKEN')

	httpRequest customHeaders: [[maskValue: true, name: 'X-JFrog-Art-Api', value: token]], httpMode: 'DELETE', ignoreSslErrors: true, responseHandle: 'NONE', url: "${JV_ARTIFACT_REPO}/${file_path}", validResponseCodes: expected_response_code, wrapAsMultipart: false
}
