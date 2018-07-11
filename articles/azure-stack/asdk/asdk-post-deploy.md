---
title: Odeslání konfigurace nasazení pro Azure Stack Development Kit (ASDK) | Dokumentace Microsoftu
description: Popisuje změny doporučené konfigurace po instalaci Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 23d99c498c139da3a145a1df230f419b4591b256
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38598437"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Po dokončení instalace ASDK úlohy konfigurace

Po [instalaci Azure Stack Development Kit (ASDK)](asdk-install.md), budete muset provést některé doporučené konfigurace po instalaci změny.

## <a name="install-azure-stack-powershell"></a>Instalace Azure Stack PowerShellu

Moduly prostředí Azure PowerShell kompatibilní služby Azure Stack jsou vyžadována pro práci s Azure Stack.

Příkazy prostředí PowerShell pro Azure Stack jsou nainstalovány v galerii prostředí PowerShell. Registrace PSGallery úložiště, otevřete relaci Powershellu se zvýšenými oprávněními a spusťte následující příkaz:

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

Profilů verzí API můžete použít k určení kompatibilní moduly AzureRM Azure Stack.  Profilů verzí API poskytují způsob, jak spravovat verze rozdíly mezi Azure a Azure Stack. Profilu verze rozhraní API je sada moduly AzureRM Powershellu s konkrétní verzí rozhraní API. **AzureRM.Bootstrapper** modul, který je dostupný v galerii prostředí PowerShell obsahuje rutiny Powershellu, které jsou nutné k práci pomocí profilů verzí API.

Nejnovější modul Azure Stack Powershellu můžete nainstalovat s nebo bez připojení k Internetu na hostitelském počítači ASDK:

> [!IMPORTANT]
> Než začnete instalovat na požadovanou verzi, ujistěte se, že jste [odinstalujte všechny existující moduly Azure Powershellu](.\.\azure-stack-powershell-install.md#uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **Připojení k Internetu** od ASDK hostitelského počítače. Spusťte následující skript prostředí PowerShell k instalaci těchto modulů na instalaci development kit:

  ``` PowerShell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  # Install Azure Stack Module Version 1.3.0. If running a pre-1804 version of Azure Stack, change the -RequiredVersion value to 1.2.11.
  Install-Module -Name AzureStack -RequiredVersion 1.3.0 

  ```

  Pokud je instalace úspěšná, zobrazí se moduly AzureRM a AzureStack ve výstupu.

- **Bez připojení k Internetu** od ASDK hostitelského počítače. Ve scénáři odpojené napřed musíte stáhnout moduly Powershellu k počítači, který má připojení k Internetu pomocí následujících příkazů prostředí PowerShell:

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
  # Install Azure Stack Module Version 1.3.0. If running a pre-1804 version of Azure Stack, change the -RequiredVersion value to 1.2.11.  
    -RequiredVersion 1.3.0
  ```

  V dalším kroku zkopírujte stažených balíčků do počítače ASDK a zaregistrujte se umístění jako výchozí úložiště a nainstalujte moduly AzureRM a AzureStack z tohoto úložiště:

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

## <a name="download-the-azure-stack-tools"></a>Stáhněte si nástroje Azure Stack

[Nástroje AzureStack](https://github.com/Azure/AzureStack-Tools) je úložiště GitHub, který je hostitelem moduly Powershellu pro správu a nasazování prostředků do služby Azure Stack. Pokud chcete získat tyto nástroje, naklonujte úložiště GitHub nebo stažení složce AzureStack nástroje spuštěním následujícího skriptu:

  ```PowerShell
  # Change directory to the root directory. 
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip `
    -DestinationPath . `
    -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Ověření instalace ASDK
Aby bylo zajištěno, že ASDK nasazení bylo úspěšné, můžete použít rutinu Test-AzureStack pomocí následujících kroků:

1. Přihlaste se jako AzureStack\AzureStackAdmin na hostitelském počítači ASDK.
2. Otevřete PowerShell jako správce (ne prostředí PowerShell ISE).
3. Spusťte: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Spusťte: `Test-AzureStack`

Testy trvat několik minut. Pokud byla instalace úspěšná, zobrazí výstup vypadá podobně jako:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Pokud došlo k chybě, použijte postup řešení potíží zobrazíte nápovědu.

## <a name="activate-the-administrator-and-tenant-portals"></a>Aktivace portálů správce a tenanta
Po nasazení, které používají službu Azure AD je nutné aktivovat i Azure Stack správce a tenanta portály. Tato aktivace vyjádří souhlas poskytuje správná oprávnění (uvedené na stránce souhlas) pro všechny uživatele adresáře portálu Azure Stack a Azure Resource Manageru.

- U portálu správce, přejděte na https://adminportal.local.azurestack.external/guest/signup, přečtěte si informace a pak klikněte na tlačítko **přijmout**. Po přijetí, můžete přidat správce služby, kteří nejsou také správci tenanta adresáře.

- Portál pro klienty, přejděte do https://portal.local.azurestack.external/guest/signup, přečtěte si informace a pak klikněte na tlačítko **přijmout**. Po přijetí, můžete uživatele v adresáři přihlásit na portál pro klienty. 

> [!NOTE] 
> Pokud nemáte aktivaci portály, pouze adresáře správce přihlásit a používat na portálech. Pokud jiný uživatel přihlásí, zobrazí se chyba s oznámením, že správce nebyla udělena oprávnění ostatním uživatelům. Když správce nepatří do požadovaného adresáře služby Azure Stack je zaregistrovaná a nativně, musí být adresář služby Azure Stack připojeno k aktivační adrese URL. Například pokud Azure Stack je zaregistrovaná a může fabrikam.onmicrosoft.com a uživatele s rolí správce je admin@contoso.com, přejděte na https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com aktivovat na portálu. 

## <a name="reset-the-password-expiration-policy"></a>Resetovat zásady vypršení platnosti hesla 
Pokud chcete mít jistotu, že heslo pro hostitele development kit platnost pasu nevyprší před vám zkušební období skončí, postupujte podle těchto kroků po nasazení ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Změna zásad vypršení platnosti hesla z prostředí Powershell:
Z konzole Powershellu se zvýšenými oprávněními spusťte příkaz:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Změna zásad vypršení platnosti hesla ručně:
1. Na hostiteli development kit, otevřete **Správa zásad skupiny** (pro správu zásad skupiny. Konzoly MMC) a přejděte do **Správa zásad skupiny** – **doménová struktura: azurestack.local** – **domén** – **azurestack.local**.
2. Klikněte pravým tlačítkem na **výchozí zásady domény** a klikněte na tlačítko **upravit**.
3. Přejděte v skupiny zásad správy editoru **konfigurace počítače** – **zásady** – **nastavení Windows** – **nastavení zabezpečení**– **Zásady účtů** – **zásady pro hesla**.
4. V pravém podokně klikněte dvakrát na **maximální stáří hesla**.
5. V **maximální stáří hesla vlastnosti** dialogovém okně Změnit **heslo vyprší za** hodnota, která se **180**a potom klikněte na tlačítko **OK**.

![Konzola pro správu zásad skupiny](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>Další postup
[Zaregistrujte ASDK v Azure](asdk-register.md)
