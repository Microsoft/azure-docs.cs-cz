---
title: Povolit aplikacím načíst tajné klíče Azure zásobníku Key Vault | Microsoft Docs
description: Pomocí ukázkové aplikace pro práci s Azure zásobníku Key Vault
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2017
ms.author: mabrigg
ms.openlocfilehash: c32667f240c9e825a82b1e9623c672b00ac999ed
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Ukázkovou aplikaci, která používá klíče a tajné klíče uložené v trezoru klíčů

V tomto článku jsme ukazují, jak spouštět ukázkovou aplikaci (HelloKeyVault), která načte klíče a tajné klíče z trezoru klíčů v zásobníku Azure.

## <a name="prerequisites"></a>Požadavky 

Spusťte následující předpoklady, některý z [Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo ze systému Windows externí klienta Pokud jste [připojení prostřednictvím VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Nainstalujte [modulů prostředí Azure PowerShell kompatibilní s Azure zásobníku](azure-stack-powershell-install.md).  
* Stažení [nástroje potřebné pro práci s Azure zásobníku](azure-stack-powershell-download.md). 

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Vytvoření a získat trezoru klíčů a nastavení aplikace

Doporučujeme nejprve vytvoření trezoru klíčů v zásobníku Azure a zaregistrovat aplikaci v Azure Active Directory (Azure AD). Můžete vytvořit a registrovat trezorů klíčů, a to pomocí portálu Azure nebo Powershellu. Tento článek ukazuje způsob prostředí PowerShell k provedení úlohy. Ve výchozím nastavení tento skript prostředí PowerShell vytvoří novou aplikaci ve službě Active Directory. Však také můžete jeden z existující aplikace. Nezapomeňte zadat hodnotu pro `aadTenantName` a `applicationPassword` proměnné. Pokud nezadáte hodnotu `applicationPassword` proměnné, tento skript generuje náhodné heslo. 

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local' 

# Password for the application. If not specified, this script will generate a random password during app creation.
$applicationPassword = '' 
                         
# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "PLEASE FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience "https://graph.windows.net/"

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Connect-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `
  
$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey
    
# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "http://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault within that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location   

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host

``` 

Následující snímek obrazovky ukazuje výstup předchozí skriptu:

![Konfigurace aplikace](media/azure-stack-kv-sample-app/settingsoutput.png)

Poznamenejte si **VaultUrl**, **AuthClientId**, a **AuthClientSecret** hodnot vrácených předchozí skript. Tyto hodnoty použijete ke spuštění aplikace HelloKeyVault.

## <a name="download-and-run-the-sample-application"></a>Stáhněte a spusťte ukázkové aplikace

Stažení ukázky klíče trezoru z Azure [Key Vault klienta ukázky](https://www.microsoft.com/en-us/download/details.aspx?id=45343) stránky. Extrahujte obsah souboru .zip, který na stanici vývoje. Existují dvě ukázky ve složce Ukázky. V tomto článku používáme HellpKeyVault vzorku. Vyhledejte **Microsoft.Azure.KeyVault.Samples** > **ukázky** > **HelloKeyVault** složky a otevřete aplikaci HelloKeyVault v sadě Visual Studio. 

Otevřete soubor HelloKeyVault\App.config a nahraďte hodnoty <appSettings> element s **VaultUrl**, **AuthClientId**, a **AuthClientSecret** hodnoty vrácená skriptem předchozí. Všimněte si, že ve výchozím nastavení souboru App.config obsahuje zástupný symbol pro *AuthCertThumbprint*, ale použít *AuthClientSecret* místo. Po nastavení nahradíte, znovu sestavte řešení a spusťte aplikaci.

![Nastavení aplikací](media/azure-stack-kv-sample-app/appconfig.png)
 
Aplikace přihlášení do služby Azure AD a pak používá tento token k ověření v zásobníku Azure key vault. Aplikace provede operací, jako je vytvářet, šifrování, zabalení a odstraňovat na klíčů a tajných klíčů služby key vault. Můžete také předat konkrétní parametry, jako *šifrování* a *dešifrovat* aplikaci, která zajišťuje, že aplikace provede pouze operace u trezoru. 


## <a name="next-steps"></a>Další postup
[Nasazení virtuálního počítače s heslem Key Vaultu](azure-stack-kv-deploy-vm-with-secret.md)

[Nasadit virtuální počítač s certifikátem Key Vault](azure-stack-kv-push-secret-into-vm.md)



