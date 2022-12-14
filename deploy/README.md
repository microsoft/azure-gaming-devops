# Sample deployment pipeline using deploy.yml

1. Change default parameter values as necessary.
2. Provide values for each input variable.
3. Create GitHub Service connection to read `deploy.yml`

```yml
name: '0.0.$(Rev:r)'

trigger: none

# Provide a default value for each parameter
parameters:
  - name: azureSubscription
    default: 'ADO-Azure-Service-Connection'
  - name: ClusterName
    default: 'cluster-weu'
  - name: ResourceGroup
    default: 'helm-rg-WEU'
  - name: ResourceGroupMRGSuffix
    default: 'mrg'
  - name: Location
    default: 'westeurope'
  - name: valueFile
    default: 'values-pr.yml'

# Provide values for each input variable.
variables:
  ContainerRegistryName : 'sampleRegisteryName'
  ContainerRegistryRoot : 'oci://sampleRegisteryName.azurecr.io/helm'
  HelmChartName         : 'sample-helm-chart'
  helmNamespace         : 'helm-namespace'
  helmArguments         : '--create-namespace --debug --set config.image.repository=sampleRegisteryName.azurecr.io/container/storageapp'

resources:
  repositories:
    # A Service Connection to GitHub must be created in the ADO project to access the deployment template as the 'endpoint' value.
    # https://docs.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml#create-a-service-connection
    - repository: gamingdevops
      type: github
      name: microsoft/azure-gaming-devops
      ref: refs/heads/main
      endpoint: github.com

stages:
  - template: deploy/helm.yml@gamingdevops
    parameters:
      ClusterName           : ${{ parameters.ClusterName }}
      ResourceGroup         : ${{ parameters.ResourceGroup }}
      ResourceGroupMRGSuffix: ${{ parameters.ResourceGroupMRGSuffix }}
      Location              : ${{ parameters.Location }}
      azureSubscription     : ${{ parameters.azureSubscription }}
      valueFile             : ${{ parameters.valueFile }}
      ContainerRegistryName : $(ContainerRegistryName)
      ContainerRegistryRoot : $(ContainerRegistryRoot)
      HelmChartName         : $(HelmChartName)
      helmNamespace         : $(helmNamespace)
      helmArguments         : $(helmArguments)

```
