---
title: Instalace prostředí PowerShell pro Azure zásobníku | Microsoft Docs
description: Informace o instalaci prostředí PowerShell pro Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 5/17/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: cb672c8e378db80707db1b0cf77a3196e36b1eb5
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="install-powershell-for-azure-stack"></a>Instalace prostředí PowerShell pro Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Azure zásobníku kompatibilní prostředí Azure PowerShell moduly jsou nutné k práci s Azure zásobníku. V této příručce budeme vás provede kroky potřebné k instalaci prostředí PowerShell pro Azure zásobníku.

Tento článek obsahuje podrobné pokyny k instalaci prostředí PowerShell pro Azure zásobníku.

> [!Note]
> Následující kroky vyžadují prostředí PowerShell 5.0. Chcete-li zkontrolovat vaší verzí, spusťte $PSVersionTable.PSVersion a porovnat **hlavní** verze.

Příkazy prostředí PowerShell pro Azure zásobníku jsou nainstalovány v galerii prostředí PowerShell. Následující postup slouží k ověření, pokud PSGallery je registrován jako úložiště, otevřete relaci prostředí PowerShell zvýšenými oprávněními a spusťte následující příkaz:

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

Pokud úložiště není registrované, otevřete relaci prostředí PowerShell zvýšenými oprávněními a spusťte následující příkaz:

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> Tento krok vyžaduje přístup k Internetu. 

## <a name="uninstall-existing-versions-of-powershell"></a>Odinstalujte stávající verze prostředí PowerShell

Před instalací požadovanou verzi, ujistěte se, abyste odinstalovali všechny dříve nainstalované moduly Powershellu zásobník Azure. Můžete je odinstalovat pomocí jedné z následujících dvou metod:

 - Chcete-li odinstalovat stávající moduly Powershellu, zavřete všechny aktivní relace prostředí PowerShell a spusťte následující příkaz:

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - Odstranit všechny složky, které začínají "Azure" z `C:\Program Files\WindowsPowerShell\Modules` a `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` složek. Odstranění těchto složek odebere všechny existující moduly Powershellu.

Následující části popisují kroky potřebné k instalaci prostředí PowerShell pro Azure zásobníku. Prostředí PowerShell můžete nainstalovat v zásobníku Azure, která je provozována v připojené, částečně připojen nebo ve scénáři odpojené.

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Instalace prostředí PowerShell ve scénáři připojené (s připojením k Internetu)

Prostřednictvím rozhraní API verze profily jsou nainstalované Azure AzureRM moduly kompatibilní zásobníku. Vyžaduje Azure zásobníku **2017-03-09profil** profil verze rozhraní API, který je k dispozici po instalaci modulu AzureRM.Bootstrapper. Další informace o rozhraní API verze profily a rutiny poskytované jejich, naleznete [spravovat profily verze rozhraní API](user/azure-stack-version-profiles.md). Kromě AzureRM moduly nainstalujete také moduly Azure PowerShell specifické pro zásobníku. Spusťte následující skript prostředí PowerShell k instalaci těchto modulů na pracovní stanici:

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version then 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

Pokud chcete potvrdit instalaci, spusťte následující příkaz:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Pokud je instalace úspěšná, moduly AzureRM a AzureStack se zobrazí ve výstupu.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Nainstalujte prostředí PowerShell odpojený nebo částečně připojené scénář (s omezenou připojení k Internetu)

Ve scénáři odpojené musíte nejprve stáhnout moduly Powershellu pro počítač, který má připojení k Internetu a potom přenést do Azure zásobníku Development Kit pro instalaci.

> [!IMPORTANT]  
> Verze modulu Azure PowerShell zásobníku 1.3.0 obsahuje seznam nejnovější změny. K upgradu z 1.2.11 verze, najdete v článku [příručka k migraci](https://aka.ms/azspowershellmigration).

1. Přihlaste se k počítači, kde máte připojení k Internetu a pomocí následujícího skriptu stahování AzureRM a AzureStack balíčky do svého místního počítače:

   ```PowerShell  
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.3.0 
   ```

  > [!Important]  
  > Pokud používáte Azure zásobníku s aktualizací 1804 nebo vyšší, změnit **requiredversion** hodnota parametru pro `1.2.11`. 

2. Zkopírujte stažených balíčků přes USB zařízení.

3. Přihlaste se k pracovní stanici a zkopírujte balíčky ze zařízení USB do umístění na pracovní stanici.

4. Nyní je nutné zaregistrovat toto umístění jako výchozí úložiště a nainstalovat moduly AzureRM a AzureStack z tohoto úložiště:

   ```PowerShell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="configure-powershell-to-use-a-proxy-server"></a>Konfigurace prostředí PowerShell k používání proxy serveru

Scénáře, které vyžadují připojení proxy serveru pro přístup k Internetu musíte nejdřív nakonfigurovat prostředí PowerShell, chcete-li použít existující server proxy.

1. Otevřete prostředí PowerShell řádku se zvýšenými oprávněními.
2. Spusťte následující příkazy:

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>Další postup

 - [Stažení nástroje Azure zásobníku z Githubu](azure-stack-powershell-download.md)
 - [Konfigurace prostředí PowerShell Azure zásobník uživatele](user/azure-stack-powershell-configure-user.md)  
 - [Konfigurace prostředí PowerShell Azure zásobníku operátor](azure-stack-powershell-configure-admin.md) 
 - [Správa profilů verze rozhraní API v Azure zásobníku](user/azure-stack-version-profiles.md)  
