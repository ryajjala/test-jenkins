pipeline {
    agent any
    stages {
        stage('create zip file') {
            steps {
                script {
                    def filesToZip = [
                        "asp\\web\\code\\test1",
                        "asp\\web\\code\\test2",
                        "asp\\web\\code\\test3",
                        "asp\\web\\code\\test4"
                    ]

                    // Create the deploy directory if it doesn't exist
                    bat "if not exist deploy mkdir deploy"

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
