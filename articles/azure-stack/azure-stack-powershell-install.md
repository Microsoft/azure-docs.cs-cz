---
title: Instalace Powershellu pro Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat prostředí PowerShell pro Azure Stack.
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
ms.date: 07/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: e2785b0beeab042d4b1ad9a9eb5f545dbb58b8b9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38487497"
---
# <a name="install-powershell-for-azure-stack"></a>Instalace Powershellu pro Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Moduly prostředí Azure PowerShell kompatibilní služby Azure Stack jsou vyžadována pro práci s Azure Stack. V této příručce budeme vás provede kroky potřebné k instalaci prostředí PowerShell pro Azure Stack.

Tento článek obsahuje podrobné pokyny k instalaci prostředí PowerShell pro Azure Stack.

> [!Note]  
> Následující kroky vyžadují prostředí PowerShell 5.0. Pokud chcete zkontrolovat verzi, spusťte $PSVersionTable.PSVersion a porovnat **hlavní** verze.

Příkazy prostředí PowerShell pro Azure Stack jsou nainstalovány v galerii prostředí PowerShell. Následující postup slouží k ověření, zda PSGallery se zaregistruje jako úložiště, otevřete relaci Powershellu se zvýšenými oprávněními a spusťte následující příkaz:

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

Pokud úložiště není zaregistrovaný, otevřete relaci Powershellu se zvýšenými oprávněními a spusťte následující příkaz:

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> Tento krok vyžaduje přístup k Internetu. 

## <a name="uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>Odinstalovat stávající verze modulů Azure Stack Powershellu

Než začnete instalovat na požadovanou verzi, ujistěte se, že odinstalovat všechny dříve nainstalované moduly Azure Stack AzureRM Powershellu. Můžete je odinstalovat pomocí jedné z těchto dvou metod:

 - Chcete-li odinstalovat existující moduly AzureRM Powershellu, zavřete všechny aktivní relace prostředí PowerShell a spuštěním následujícího příkazu:

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - Odstranit všechny složky, které začínají znakem "Azure" z `C:\Program Files\WindowsPowerShell\Modules` a `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` složek. Odstranění těchto složek odebere všechny existující moduly Powershellu.

Následující části popisují kroky potřebné k instalaci prostředí PowerShell pro Azure Stack. Prostředí PowerShell můžete nainstalovat, ve službě Azure Stack, která je provozována v připojené, částečně připojeno nebo ve scénáři odpojené.

## <a name="install-the-azure-stack-powershell-modules-in-a-connected-scenario-with-internet-connectivity"></a>Instalace modulů Azure Stack Powershellu v případě propojené v (s připojením k Internetu)

Moduly AzureRM kompatibilní služby Azure Stack jsou nainstalovány prostřednictvím profilů verzí API. Vyžaduje Azure Stack **2017-03-09-profile** profilu verze rozhraní API, která je k dispozici nainstalováním modulu AzureRM.Bootstrapper. Další informace o profilech verze rozhraní API a rutin poskytovaných je, naleznete [Správa profilů verzí API](user/azure-stack-version-profiles.md). Kromě moduly AzureRM také byste měli nainstalovat moduly Azure Powershellu specifické pro zásobník. Spusťte následující skript prostředí PowerShell k instalaci těchto modulů na pracovní stanici vývoje:

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

K potvrzení instalace, spusťte následující příkaz:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Pokud je instalace úspěšná, zobrazí se moduly AzureRM a AzureStack ve výstupu.

## <a name="install-the-azure-stack-powershell-modules-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Instalace modulů Azure Stack Powershellu odpojeném nebo částečně připojeného scénáře (s omezenou připojení k Internetu)

V případě odpojené v musíte nejprve stáhnout modulů prostředí PowerShell do počítače, který má připojení k Internetu a je přenést na Azure Stack Development Kit pro instalaci.

> [!IMPORTANT]  
> Verze modulu Azure PowerShell zásobníku 1.3.0 obsahuje seznam rozbíjejících změn. K upgradu portálu 1.2.11 verze, najdete v článku [Průvodce migrací](https://aka.ms/azspowershellmigration).

1. Přihlaste se k počítači, kde máte připojení k Internetu a použijte tento skript stáhnout AzureRM a AzureStack balíčky do místního počítače:

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
  > Pokud používáte Azure Stack s aktualizací update 1804 nebo vyšší, změňte **requiredversion** pro parametr `1.2.11`. 

2. Zkopírujte stažených balíčků do zařízení USB.

3. Přihlaste se k pracovní stanici a zkopírujte balíčky ze zařízení USB do umístění na pracovní stanici.

4. Nyní musíte zaregistrovat toto umístění jako výchozí úložiště a nainstalujte moduly AzureRM a AzureStack z tohoto úložiště:

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

## <a name="configure-powershell-to-use-a-proxy-server"></a>Konfigurace Powershellu pro použití proxy serveru

Ve scénářích, které vyžadují přístup k Internetu proxy server je nutné nejprve nakonfigurovat prostředí PowerShell pro použití existujícího proxy serveru.

1. Otevřete řádku Powershellu se zvýšenými oprávněními.
2. Spusťte následující příkazy:

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>Další postup

 - [Stáhněte si nástroje pro Azure Stack z Githubu](azure-stack-powershell-download.md)
 - [Konfigurace prostředí PowerShell uživatele Azure stacku](user/azure-stack-powershell-configure-user.md)  
 - [Konfigurace prostředí PowerShell pro operátory Azure stacku](azure-stack-powershell-configure-admin.md) 
 - [Správa profilů verzí API ve službě Azure Stack](user/azure-stack-version-profiles.md)  
