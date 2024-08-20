# demo-explore24

Build a Powershell Module at VMware Explore

## Create a Module Manifest

```pwsh
$version = "1.0.2"
$path = "C:\Users\david\Documents\Github\demo-explore24\$version"
$manifestParams = @{
  Path = "$path\demo-explore24.psd1"
  RootModule = "$path\demo-explore24.psm1"
  Description = "Module for Explore 2024"
  Author = "David Stamen"
  ModuleVersion = $version
  FunctionsToExport = "HelloWorld"
}
```

### Create New Manifest

```pwsh
New-ModuleManifest @manifestParams
```

### Update Existing Manifest

```pwsh
Update-ModuleManifest @manifestParams
```

## Configuration Authentication & Secrets

```pwsh
$username = "dstamen@gmail.com"
$galleryToken = ""
$accessToken = ""
$patToken = $accessToken | ConvertTo-SecureString -AsPlainText -Force
$credsAzureArtifact = New-Object System.Management.Automation.PSCredential("$username", $patToken)
```

## Register Azure Artifact Repository

```pwsh
$url = "https://pkgs.dev.azure.com/dstamen/demo-explore24/_packaging/demofeed/nuget/v2"
$parameters = @{
  Name = "AzureArtifacts"
  SourceLocation = $url
  PublishLocation = $url
  InstallationPolicy = 'Trusted'
  Credential = $credsAzureArtifact
}
Register-PSRepository @parameters
```

## Publish Module to Azure Artifact Repository

```pwsh
$publishParams = @{
    Path        = $path
    Repository  = "AzureArtifacts"
    NugetApiKey = $accessToken
    Force       = $true
    Verbose     = $false
}
Publish-Module @publishParams
```

## Publish Module to the Powershell Gallery

```pwsh
$galleryParams = @{
    Path        = $path
    Repository  = "PSGallery"
    NugetApiKey = $galleryToken
    Force       = $true
    Verbose     = $false
}
Publish-Module @galleryParams
```


## Install Module from Azure Artifact Repository

```pwsh
Install-package demo-explore24 -Credential $credsAzureArtifact
```

## Update Module from Azure Artifact Repository

```pwsh
Update-Module demo-explore24 -Credential $credsAzureArtifact
```
