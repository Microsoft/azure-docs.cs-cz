---
title: Povolit aplikacím k načítání tajných kódů služby Key Vault Azure Stack | Dokumentace Microsoftu
description: Použití ukázkové aplikace pro práci s Azure Stack Key Vault
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sethm
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: 7ca02b35cd8f302b856b2d7fcbfb5bac304f1a00
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358630"
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Ukázkovou aplikaci, která používá klíči a tajnými kódy uloženými v trezoru klíčů

*Týká se Azure Stack integrované systémy a Azure Stack Development Kit*

Postupujte podle kroků v tomto článku a spuštění ukázkové aplikace **HelloKeyVault** , který načte klíče a tajné klíče z klíče trezoru ve službě Azure Stack.

## <a name="prerequisites"></a>Požadavky

Můžete nainstalovat z následující požadavky [Azure Stack Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo z Windows na základě externí klienta máte [připojené prostřednictvím sítě VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Nainstalujte [moduly Azure Powershellu kompatibilní s Azure Stack](azure-stack-powershell-install.md).
* Ve službě [Azure Stack development Kit by měl být blobEndpoint](azure-stack-powershell-download.md) .

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Vytvořte a získejte služby key vault a nastavení aplikace

Příprava pro ukázkovou aplikaci:

* Vytvoření trezoru klíčů ve službě Azure Stack.
* Zaregistrujte aplikaci v Azure Active Directory (Azure AD).

Příprava pro ukázkovou aplikaci, můžete použít na webu Azure portal nebo Powershellu. Tento článek ukazuje, jak vytvořit trezor klíčů a registrace aplikace pomocí Powershellu.

> [!NOTE]
> Ve výchozím nastavení skript Powershellu vytvoří novou aplikaci ve službě Active Directory. Však můžete zaregistrovat jedno z existující aplikace.

Před spuštěním následujícího skriptu, ujistěte se, že zadáte hodnoty pro `aadTenantName` a `applicationPassword` proměnné. Pokud nezadáte hodnotu `applicationPassword`, tento skript generuje náhodné heslo.

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script generates a random password during app creation.
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
$aadTenantName = "FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

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

Následující obrázek ukazuje výstup ze skriptu použitý k vytvoření služby key vault:

![Trezor klíčů pomocí přístupových klíčů](media/azure-stack-key-vault-sample-app/settingsoutput.png)

Poznamenejte si, **VaultUrl**, **AuthClientId**, a **AuthClientSecret** hodnoty vrácené předchozím scénáři. Tyto hodnoty použijete ke spuštění **HelloKeyVault** aplikace.

## <a name="download-and-configure-the-sample-application"></a>Stáhnout a nakonfigurovat ukázkovou aplikaci

Stáhněte si ukázky trezoru klíčů Azure [ukázky klienta služby Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) stránky. Extrahujte obsah souboru .zip na pracovní stanici vývoje. Existují dvě aplikace ve složce samples. Tento článek používá **HelloKeyVault**.

Načíst **HelloKeyVault** vzorku:

1. Přejděte **Microsoft.Azure.KeyVault.Samples**, **ukázky**, **HelloKeyVault** složky.
2. Otevřít **HelloKeyVault** aplikace v sadě Visual Studio.

### <a name="configure-the-sample-application"></a>Nakonfigurovat ukázkovou aplikaci

V sadě Visual Studio:

1. Otevřete soubor HelloKeyVault\App.config a najít `<appSettings>` elementu.
2. Aktualizace **VaultUrl**, **AuthClientId**, a **AuthClientSecret** klíče pomocí hodnot vrácených se používají k vytvoření služby key vault. Ve výchozím souboru App.config je zástupný symbol pro `AuthCertThumbprint`. Nahraďte tento zástupný text `AuthClientSecret`.

   ![Nastavení aplikací](media/azure-stack-key-vault-sample-app/appconfig.png)

3. Znovu sestavte řešení.

## <a name="run-the-application"></a>Spuštění aplikace

Při spuštění **HelloKeyVault**, aplikace přihlásí ke službě Azure AD a potom použije `AuthClientSecret` token pro ověření do trezoru klíčů ve službě Azure Stack.

Můžete použít **HelloKeyVault** ukázku na:

* Provádění základních operací, jako je vytváření, šifrování, zabalení a odstranit na klíče a tajné kódy.
* Předání parametrů, jako `encrypt` a `decrypt` k **HelloKeyVault**a použít zadané změny do trezoru klíčů.

## <a name="next-steps"></a>Další postup

* [Nasazení virtuálního počítače s heslem Key Vaultu](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Nasazení virtuálního počítače s certifikátem Key Vaultu](azure-stack-key-vault-push-secret-into-vm.md)
