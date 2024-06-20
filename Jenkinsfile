pipeline {
    agent any
 
    stages {
        stage("Clean") {
            steps {
                deleteDir()
            }
        }
 
        stage("Clone") {
            steps {
                git branch: "main",
                    url: "https://github.com/waihonchong/jenkins-sample.git"
            }
        }
        stage("Build") {
            steps {
                script {
                    dir ("src\\JenkinsSample") {
                        bat "dotnet build"
                    }
                }
            }
        }
 
        stage("Unit Test") {
            steps {
                script {
                    dir ("src\\JenkinsSample\\WebApp.UnitTest") {
                        bat "dotnet test"
                    }
                }
            }
        }
        stage("Package") {
            steps {
                script {
                    dir ("src\\JenkinsSample\\WebApp") {
                        bat "dotnet publish --configuration Release --runtime win81-x64 /p:EnvironmentName=Production --self-contained false --output \"${WORKSPACE}\\publish\""
                    }
                }
            }
        }
        stage("Deploy") {
            steps {
                script {
                    try {
                        echo "Stopping App Pool"
                        bat "%systemroot%\\system32\\inetsrv\\appcmd stop apppool /apppool.name:DefaultAppPool"
                    }
                    catch (Exception err) {
                        echo err.getMessage()
                    }
                    echo "Cleaning the app folder"
                    bat "del \"C:\\inetpub\\wwwroot\\jenkins-sample-webapp\\**\" /S /Q"
                    echo "Copying the published files to the app folder"
                    bat "xcopy \"${WORKSPACE}\\publish\\**\" \"C:/inetpub/wwwroot/jenkins-sample-webapp\" /E /Q"
                    echo "Starting App Pool"
                    bat "%systemroot%\\system32\\inetsrv\\appcmd start apppool /apppool.name:DefaultAppPool"
                }
            }
        }
    }
}
