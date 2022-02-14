pipeline {
  
  agent any

  environment {
    MSBUILD = "C:\\Program Files\\Microsoft Visual Studio\\2022\\Community\\Msbuild\\Current\\Bin\\MSBuild.exe"
    CONFIG = 'Release'
    PLATFORM = 'x86'
  }
  
  stages {

    stage('Update manifest version') {
      steps {
          powershell '''
            $manifest = "01_VisualStudio\\WinForms.App\\WinForms.Packaging\\Package.appxmanifest"     
            [xml]$xmlDoc = Get-Content $manifest
            $version = $xmlDoc.Package.Identity.Version
            $trimmedVersion = $version -replace '.[0-9]+$', '.'
            $xmlDoc.Package.Identity.Version = $trimmedVersion + ${env:BUILD_NUMBER}
            $xmlDoc.Save($manifest)
          '''
      }
    }
    stage('Build') {
      steps {
        bat "dotnet restore 01_VisualStudio\\WinForms.App\\WinForms.App\\WinForms.App.csproj"

        bat "\"${MSBUILD}\" 01_VisualStudio\\WinForms.App\\WinForms.app.sln /p:Configuration=${env.CONFIG} /p:AppxBundlePlatforms=${env.PLATFORM}  /p:AppxBundle=Never /p:UapAppxPackageBuildMode=Sideloading  /p:AppxPackageSigningEnabled=false"
        
      }
      post{
          always {
           archiveArtifacts artifacts: '**/*.msix', followSymlinks: false
          }
      }
    }
  }
}