pipeline {
    agent any
    stages {
        stage('create zip file') {
            steps {
                script {
                    // Read the manifests.txt file
                    def manifestFile = bat(returnStdout: true, script: 'type Manifests\\manifests.txt').trim()
                    def lines = manifestFile.split('\n')

                    // Create the deploy directory if it doesn't exist
                    bat "if not exist deploy mkdir deploy"

                    def filesToZip = []
                    for (line in lines) {
                        echo "Line: ${line}" // Debug line

                        if (line =~ /^TFS.*\/.*$/) {
                            // Split each line into parts
                            def parts = line.split(' ')
                            echo "Parts: ${parts}" // Debug line
                            if (parts.size() >= 2) {
                                def filePath = parts[1].trim()
                                def fileNameWithPath = filePath.substring(filePath.indexOf('/') + 1)
                                fileNameWithPath = fileNameWithPath.replaceAll('/', '\\\\') // Replace '/' with '\\'
                                fileNameWithPath = fileNameWithPath.replace('classic\\web\\', '') // Remove 'classic\web'
                                echo "fileNameWithPath: ${fileNameWithPath}" //Debug Line
                                // Add the file name with path to the filesToZip list
                                filesToZip.add(fileNameWithPath)
                            }
                        }
                    }

                    for (file in filesToZip) {
                        try {
                            def relativePath = file.substring(0, file.lastIndexOf('\\'))
                            def fileName = file.substring(file.lastIndexOf('\\') + 1)
                            def sourcePath = "${env.WORKSPACE}\\${relativePath}"
                            def destinationPath = "${env.WORKSPACE}\\deploy\\${relativePath}"

                            echo "Copying file: ${file}"
                            echo "Source path: ${sourcePath}"
                            echo "Destination path: ${destinationPath}"

                            bat "robocopy \"${sourcePath}\" \"${destinationPath}\" \"${fileName}\" /E /S"

                            echo "Copy completed for file: ${file}"
                        } catch (Exception e) {
                            echo "Error copying file: ${file}"
                            echo e.toString()
                        }
                    }

                    // List files in the deploy folder
                    bat "dir deploy"

                    // Create the zip file
                    bat "powershell Compress-Archive -Path deploy -DestinationPath ${env.WORKSPACE}\\myartifact.zip"
                    archiveArtifacts artifacts: 'myartifact.zip'
                }
            }
        }
		stage('Upload to Artifactory') {
            steps {
                script {
                    def currentDate = new Date().format('yyyyMMdd')
                    def buildNumber = env.BUILD_NUMBER
                    def branchName = env.GIT_BRANCH_NAME
                    def version = branchName =~ /release_(\d+\.\d+\.\d+\.\d+)/ ? branchName[0][1] : ""

                    def renamedArtifact = "cdosp_ASP_${currentDate}.${buildNumber}_v${version}.zip"

                    windowsArtifactUpload url: 'https://artifactory.company.com/artifactory',
                            spec: [
                                    uploadSpec: [
                                            // Source path of the artifact
                                            sourcePath: "${env.WORKSPACE}\\app.zip",
                                            // Target path in the Artifactory repository
                                            targetPath: "webapp/General/cdosuite-nuget-snapshot-locl/builds/bau-classic-ASP-upper-ci/release/latest/${renamedArtifact}"
                                    ]
                            ]
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
