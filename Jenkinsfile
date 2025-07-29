pipeline {
    agent {
        node {
            label "macos"
        }
    }
    options {
        office365ConnectorWebhooks([[
            startNotification: false,
            notifySuccess: true,
            notifyAborted: true,
            notifyNotBuilt: true,
            notifyUnstable: true,
            notifyFailure: true,
            notifyBackToNormal: true,
            notifyRepeatedFailure: true,
            adaptiveCards: true
        ]])
    }
    triggers {
        pollSCM('H/10 * * * *')
    }
    stages {
        stage('Build Package')
        {
            steps
            {
                pwsh '''
                    $csprojPath = $env:WORKSPACE+ "/TOCropView/TOCropView.csproj"
                    $CsProjs = $csprojPath
                    $RootFolder = $env:WORKSPACE + "/TOCropView/"
                    $SlnPath = $env:WORKSPACE + "/TOCropView/TOCropView.sln"

                    & $env:ProjectBuilderScriptPathApple
                '''
            }
        }
        stage('Upload Package')
        {
            environment {
                GITHUB_NUGET_KEY = credentials("github-nuget-key")
            }
            steps
            {
                pwsh '''
                    $csprojPath = $env:WORKSPACE+ "/TOCropView/TOCropView.csproj"
                    $ReleaseFolder = $env:WORKSPACE + "/TOCropView/bin/Release/"
                    $SlnPath = $env:WORKSPACE + "/TOCropView/TOCropView.sln"

                    & $env:NugetUploaderScriptPathApple
                '''
            }
        }
    }
    post {
        success
        {
            cleanWs();
        }
    }
}