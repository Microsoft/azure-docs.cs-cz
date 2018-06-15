---
title: Automatizované skript pro vytvoření aplikace webového portálu Service Manager pro připojení s konektorem správy IT služby v Azure | Microsoft Docs
description: Vytvoření aplikace webového portálu Service Manager pomocí automatizované skriptu připojit s konektorem správy IT služby ve službě Azure a centrálně monitorovat a spravovat ITSM pracovní položky.
services: log-analytics
documentationcenter: ''
author: JYOTHIRMAISURI
manager: riyazp
editor: ''
ms.assetid: 879e819f-d880-41c8-9775-a30907e42059
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: v-jysur
ms.openlocfilehash: 75c61894d5562f4bb0cb45fd8500bd9cf0f2bf8f
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32772122"
---
# <a name="create-service-manager-web-app-using-the-automated-script"></a>Vytvoření aplikace webového portálu Service Manager pomocí automatizované skriptu

Pomocí následujícího skriptu k vytvoření webové aplikace pro instanci portálu Service Manager. Zde jsou další informace o připojení k portálu Service Manager: [portálu Service Manager webové aplikace](log-analytics-itsmc-connections.md#create-and-deploy-service-manager-web-app-service)

Spusťte skript tím, že poskytuje následující požadované podrobnosti:

- Podrobnosti o předplatném Azure
- Název skupiny prostředků
- Umístění
- Podrobnosti o serveru portálu Service Manager (název serveru, domény, uživatelské jméno a heslo)
- Předpona názvu lokality pro vaši webovou aplikaci
- ServiceBus Namespace.

Skript se vytvoří webovou aplikaci pomocí názvu, který jste zadali (spolu s několika další řetězce, aby byla zajištěna jedinečnost). Vygeneruje **adresa URL webové aplikace**, **ID klienta**, a **tajný klíč klienta**.

Uložit tyto hodnoty budete potřebovat tyto hodnoty při vytváření připojení ke konektoru služby správy IT.

## <a name="prerequisites"></a>Požadavky

 Windows Management Framework 5.0 nebo novější.
Windows 10 má 5.1 ve výchozím nastavení. Si můžete stáhnout rozhraní z [sem](https://www.microsoft.com/download/details.aspx?id=53347):

Pomocí následujícího skriptu:

```
####################################
# User Configuration Section Begins
####################################

# Subscription name in Azure account. Check in Azure Portal.
$azureSubscriptionName = ""

# Resource group name for resource deployment. Could be an existing resource group or a new one to be created.
$resourceGroupName = ""

# Location for existing resource group or new resource group deployment
################################### List of available regions #################################################
# centralus,eastasia,southeastasia,eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,
# northeurope,westeurope,japaneast,japanwest,brazilsouth,australiasoutheast,australiaeast,westindia,southindia,
# centralindia,canadacentral,canadaeast,uksouth,ukwest.
###############################################################################################################
$location = ""

# Service Manager Authentication Settings
$serverName = ""
$domain = ""
$username = ""
$password = ""


# Azure site Name Prefix. Default is "smoc". It can be configured to any desired value.
$siteNamePrefix = ""

# Service Bus namespace. Please provide an already existing service bus namespace.
# If it doesn't exist, a new one will be created with name $siteName + "sbn" which can also be later reused for any other hybrid connections.
$serviceName = ""

##################################
# User Configuration Section Ends
##################################

################
# Installations
################

# Allowing the execution of the script for current user.  
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

Write-Host "Checking for required modules..."
if(!(Get-PackageProvider -Name NuGet))
{
   Write-Host "Installing NuGet Package Provider..."
   Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Scope CurrentUser -Force -WarningAction SilentlyContinue
}
$module = Get-Module -ListAvailable -Name AzureRM

if(!$module -or ($module[0].Version.Major -lt 4))
{
    Write-Host "Installing AzureRm Module..."  
    try
    {
        # In case of Win 10 Anniversary update
        Install-Module AzureRM -MinimumVersion 4.1.0 -Scope CurrentUser -Force -WarningAction SilentlyContinue -AllowClobber
    }
    catch
    {
        Install-Module AzureRM -MinimumVersion 4.1.0 -Scope CurrentUser -Force -WarningAction SilentlyContinue
    }

}

Write-Host "Requirement check complete!!"

#############
# Parameters
#############

$errorActionPreference = "Stop"

$templateUri = "https://raw.githubusercontent.com/SystemCenterServiceManager/SMOMSConnector/master/azuredeploy.json"

if(!$siteNamePrefix)
{
    $siteNamePrefix = "smoc"
}

Connect-AzureRmAccount

$context = Set-AzureRmContext -SubscriptionName $azureSubscriptionName -WarningAction SilentlyContinue

$resourceProvider = Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

if(!$resourceProvider -or $resourceProvider[0].RegistrationState -ne "Registered")
{
    try
    {
        Write-Host "Registering Microsoft.Web Resource Provider"
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Web
    }
    catch
    {
        Write-Host "Failed to Register Microsoft.Web Resource Provider. Please register it in Azure Portal."
        exit
    }   
}
do
{
    $rand = Get-Random -Maximum 32000

    $siteName = $siteNamePrefix + $rand

    $resource = Find-AzureRmResource -ResourceNameContains $siteName -ResourceType Microsoft.Web/sites

}while($resource)

$azureSite = "https://"+$siteName+".azurewebsites.net"

##############
# MAIN Begins
##############

# Web App Deployment
####################

$tenant = $context.Tenant.Id
if(!$tenant)
{
    #For backward compatibility with older versions
    $tenant = $context.Tenant.TenantId
}
try
{
    Get-AzureRmResourceGroup -Name $resourceGroupName
}
catch
{
    New-AzureRmResourceGroup -Location $location -Name $resourceGroupName
}

Write-Output "Web App Deployment in progress...."

New-AzureRmResourceGroupDeployment -TemplateUri $templateUri -siteName $siteName -ResourceGroupName $resourceGroupName

Write-Output "Web App Deployed successfully!!"

# AAD Authentication
####################

Add-Type -AssemblyName System.Web

$clientSecret = [System.Web.Security.Membership]::GeneratePassword(30,2).ToString()

$clientSecret = $clientSecret | ConvertTo-SecureString -AsPlainText -Force

try
{

    Write-Host "Creating AzureAD application..."

    $adApp = New-AzureRmADApplication -DisplayName $siteName -HomePage $azureSite -IdentifierUris $azureSite -Password $clientSecret

    Write-Host "AzureAD application created succesfully!!"
}
catch
{
    # Delete the deployed web app if Azure AD application fails
    Remove-AzureRmResource -ResourceGroupName $resourceGroupName -ResourceName $siteName -ResourceType Microsoft.Web/sites -Force

    Write-Host "Failure occured in Azure AD application....Try again!!"

    exit

}

$clientId = $adApp.ApplicationId

$servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $clientId

# Web App Configuration
#######################
try
{

    Write-Host "Configuring deployed Web-App..."
    $webApp = Get-AzureRMWebAppSlot -ResourceGroupName $resourceGroupName -Name $siteName -Slot production -WarningAction SilentlyContinue

    $appSettingList = $webApp.SiteConfig.AppSettings

    $appSettings = @{}
    ForEach ($item in $appSettingList) {
        $appSettings[$item.Name] = $item.Value
    }
    $appSettings['ida:Tenant'] = $tenant
    $appSettings['ida:Audience'] = $azureSite
    $appSettings['ida:ServerName'] = $serverName
    $appSettings['ida:Domain'] = $domain
    $appSettings['ida:Username'] = $userName

    $connStrings = @{}
    $kvp = @{"Type"="Custom"; "Value"=$password}
    $connStrings['ida:Password'] = $kvp

    Set-AzureRMWebAppSlot -ResourceGroupName $resourceGroupName -Name $siteName -AppSettings $appSettings -ConnectionStrings $connStrings -Slot production -WarningAction SilentlyContinue

}
catch
{
    Write-Host "Web App configuration failed. Please ensure all values are provided in Service Manager Authentication Settings in User Configuration Section"

    # Delete the AzureRm AD Application if confiuration fails
    Remove-AzureRmADApplication -ObjectId $adApp.ObjectId -Force

    # Delete the deployed web app if configuration fails
    Remove-AzureRmResource -ResourceGroupName $resourceGroupName -ResourceName $siteName -ResourceType Microsoft.Web/sites -Force

    exit
}


# Relay Namespace
###################

if(!$serviceName)
{
    $serviceName = $siteName + "sbn"
}

$resourceProvider = Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Relay

if(!$resourceProvider -or $resourceProvider[0].RegistrationState -ne "Registered")
{
    try
    {
        Write-Host "Registering Microsoft.Relay Resource Provider"
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Relay
    }
    catch
    {
        Write-Host "Failed to Register Microsoft.Relay Resource Provider. Please register it in Azure Portal."
    }   
}

$resource = Find-AzureRmResource -ResourceNameContains $serviceName -ResourceType Microsoft.Relay/namespaces

if(!$resource)
{
    $serviceName = $siteName + "sbn"
    $properties = @{
        "sku" = @{
            "name"= "Standard"
            "tier"= "Standard"
            "capacity"= 1
         }
    }
    try
    {
        Write-Host "Creating Service Bus namespace..."
        New-AzureRmResource -ResourceName $serviceName -Location $location -PropertyObject $properties -ResourceGroupName $resourceGroupName -ResourceType Microsoft.Relay/namespaces -ApiVersion 2016-07-01 -Force
    }
    catch
    {
        $err = $TRUE
        Write-Host "Creation of Service Bus Namespace failed...Please create it manually from Azure Portal.`n"
    }

}

Write-Host "Note: Please Configure Hybrid connection in the Networking section of the web application in Azure Portal to link to the on-premises system.`n"
Write-Host "App Details"
Write-Host "============"
Write-Host "App Name:"  $siteName
Write-Host "Client Id:"  $clientId
Write-Host "Client Secret:"  $clientSecret
Write-Host "URI:"  $azureSite
if(!$err)
{
    Write-Host "ServiceBus Namespace:"  $serviceName  
}

```
## <a name="next-steps"></a>Další postup
[Konfigurace hybridní připojení](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).
