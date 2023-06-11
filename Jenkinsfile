pipeline{
	agent any
	stages{
		stage('create zip file'){
			steps{
				script{
					def filesToZip = ["asp\\web\\code\\test1",
					"asp\\web\\code\\test2",
					"asp\\web\\code\\test3",
					"asp\\web\\code\\test4"]
					
					bat "if not exist deploy mkdir deploy"
					echo "${filesToZip}"
					// Copy each file to the deploy folder
                    filesToZip.each { file ->
                        script {
                            bat("xcopy /Y /I ${env.WORKSPACE}\\\"${file}\" \"deploy\"")
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
