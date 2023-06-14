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
                        if (line =~ /^TFS.*/) {
                            // Split each line into parts
                            def parts = line.split(' ')
                            def filePath = parts[2].trim()

                            // Extract only the file name with path
                            def fileNameWithPath = filePath.substring(filePath.indexOf('/', 1) + 1)

                            // Add the file name with path to the filesToZip list
                            filesToZip.add(fileNameWithPath)
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
                }
            }
        }
    }
}
