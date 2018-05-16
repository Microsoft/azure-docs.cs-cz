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
ms.date: 05/11/2018
ms.author: mabrigg
ms.openlocfilehash: 39bce286c756660cd8755358cf98f2c8d35ce351
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Ukázkové aplikace, která používá klíče a tajné klíče uložené v trezoru klíčů

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Postupujte podle kroků v tomto článku ke spuštění ukázkové aplikace (HelloKeyVault), která načte klíče a tajné klíče z trezoru klíčů v zásobníku Azure.

## <a name="prerequisites"></a>Požadavky

Můžete nainstalovat následující předpoklady v zásobníku Azure [Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo ze systému Windows externí klienta Pokud jste [připojení prostřednictvím VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Nainstalujte [modulů prostředí Azure PowerShell kompatibilní s Azure zásobníku](azure-stack-powershell-install.md).
* Stažení [nástroje potřebné pro práci s Azure zásobníku](azure-stack-powershell-download.md).

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Vytvoření a získat trezoru klíčů a nastavení aplikace

Příprava pro ukázkovou aplikaci:

* Vytvoření trezoru klíčů v zásobníku Azure.
* Zaregistrujte aplikaci v Azure Active Directory (Azure AD).

Pomocí portálu Azure nebo prostředí PowerShell můžete připravit pro ukázkovou aplikaci. Tento článek ukazuje postup vytvoření trezoru klíčů a zaregistrovat aplikaci pomocí prostředí PowerShell.

>[!NOTE]
>Skript prostředí PowerShell ve výchozím nastavení vytvoří novou aplikaci ve službě Active Directory. Však můžete registrovat jedním z existující aplikace.

 Před spuštěním následujícího skriptu, musí zadat hodnoty pro `aadTenantName` a `applicationPassword` proměnné. Pokud nezadáte hodnotu `applicationPassword`, tento skript generuje náhodné heslo.

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
Add-AzureRmAccount `
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

# Create a new resource group and a key vault in that resource group.
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

Další snímek obrazovky ukazuje výstup ze skriptu použít k vytvoření trezoru klíčů:

![Klíče trezoru se přístupové klávesy](media/azure-stack-kv-sample-app/settingsoutput.png)

Poznamenejte si **VaultUrl**, **AuthClientId**, a **AuthClientSecret** hodnot vrácených předchozí skript. Tyto hodnoty použijete ke spuštění aplikace HelloKeyVault.

## <a name="download-and-configure-the-sample-application"></a>Stáhnout a nakonfigurovat ukázkovou aplikaci

Stažení ukázky klíče trezoru z Azure [Key Vault klienta ukázky](https://www.microsoft.com/en-us/download/details.aspx?id=45343) stránky. Extrahujte obsah souboru .zip, na pracovní stanici. Existují dvě aplikace ve složce ukázky, tento článek používá HelloKeyVault.

Načíst ukázková HelloKeyVault:

* Vyhledejte **Microsoft.Azure.KeyVault.Samples** > **ukázky** > **HelloKeyVault** složky.
* Otevřete aplikaci HelloKeyVault v sadě Visual Studio.

### <a name="configure-the-sample-application"></a>Nakonfigurujte ukázkovou aplikaci

V sadě Visual Studio:

* Otevřete soubor HelloKeyVault\App.config a procházet a najít &lt; **appSettings** &gt; element.
* Aktualizace **VaultUrl**, **AuthClientId**, a **AuthClientSecret** klíče s hodnot vrácených použité k vytvoření trezoru klíčů. (Ve výchozím nastavení, je soubor App.config zástupný symbol pro *AuthCertThumbprint*. Nahraďte tento zástupný symbol *AuthClientSecret*.)

  ![Nastavení aplikací](media/azure-stack-kv-sample-app/appconfig.png)

* Znovu sestavte řešení.

## <a name="run-the-application"></a>Spuštění aplikace

Při spuštění HelloKeyVault aplikace přihlášení do služby Azure AD a pak použije AuthClientSecret token k ověření do trezoru klíčů v zásobníku Azure.

Můžete použít HelloKeyVault vzorku, který se:

* Provádění základních operací, jako je vytváření, šifrování, zabalení a odstranění na klíče a tajné klíče.
* Předat parametry, jako například *šifrování* a *dešifrovat* k HelloKeyVault a použít zadané změny trezoru klíčů.

## <a name="next-steps"></a>Další postup

[Nasazení virtuálního počítače s heslem Key Vaultu](azure-stack-kv-deploy-vm-with-secret.md)

[Nasadit virtuální počítač s certifikátem Key Vault](azure-stack-kv-push-secret-into-vm.md)
