---
title: Konfigurace nasazení pro Azure zásobníku Development Kit (ASDK) příspěvků | Microsoft Docs
description: Popisuje změny doporučené konfigurace po instalaci Azure zásobníku Development Kit (ASDK).
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 4b58f3496b25e4fc04761b9df6e27f8313b35fe9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="post-asdk-installation-configuration-tasks"></a>Po dokončení instalace ASDK úlohy konfigurace
Po [instalaci ASDK](asdk-install.md), existuje několik změny doporučené konfigurace po instalaci. 

## <a name="install-azure-stack-powershell"></a>Instalace Azure Stack PowerShellu 
Azure zásobníku kompatibilní prostředí Azure PowerShell moduly jsou nutné k práci s Azure zásobníku.

Příkazy prostředí PowerShell pro Azure zásobníku jsou nainstalovány v galerii prostředí PowerShell. Pokud chcete zaregistrovat PSGallery úložiště, otevřete relaci prostředí PowerShell zvýšenými oprávněními a spusťte následující příkaz:

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

 Prostřednictvím rozhraní API verze profily jsou nainstalované Azure AzureRM moduly kompatibilní zásobníku. Azure zásobníku vyžaduje profil verze rozhraní API 2017-03-09profil, který je k dispozici po instalaci modulu AzureRM.Bootstrapper. 
 
 S nebo bez připojení k Internetu k hostitelskému počítači ASDK můžete nainstalovat nejnovější modul Azure PowerShell zásobníku:

> [!IMPORTANT]
> Před instalací požadovanou verzi, ujistěte se, že jste [odinstalovat všechny existující modulů prostředí Azure PowerShell](.\.\azure-stack-powershell-install.md#uninstall-existing-versions-of-powershell).

- **S připojením k Internetu** od ASDK hostitelského počítače. Spusťte následující skript prostředí PowerShell k instalaci těchto modulů na instalaci development kit:

  ``` PowerShell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```
  Pokud je instalace úspěšná, moduly AzureRM a AzureStack se zobrazí ve výstupu.

- **Bez připojení k Internetu** od ASDK hostitelského počítače. Ve scénáři odpojené musíte nejprve stáhnout moduly Powershellu pro počítač, který má připojení k Internetu pomocí těchto příkazů prostředí PowerShell:

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
    -RequiredVersion 1.2.11
  ```
  V dalším kroku zkopírujte stažený balíčky do počítače, ASDK a zaregistrujte umístění jako výchozí úložiště a nainstalujte moduly AzureRM a AzureStack z tohoto úložiště:

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

## <a name="download-the-azure-stack-tools"></a>Stažení nástroje Azure zásobníku
[Nástroje AzureStack](https://github.com/Azure/AzureStack-Tools) je úložiště GitHub, který je hostitelem moduly Powershellu pro správu a nasazení prostředků do protokolů Azure. Pokud chcete získat tyto nástroje, naklonujte úložiště GitHub nebo stáhnout složce AzureStack nástroje spuštěním následujícího skriptu:

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
Aby se zajistilo, že ASDK nasazení proběhlo úspěšně, můžete použít rutinu Test-AzureStack pomocí následujících kroků:

1. Přihlaste se jako AzureStack\CloudAdmin na hostitelském počítači ASDK.
2. Otevřete PowerShell jako správce (není prostředí PowerShell ISE).
3. Spusťte: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Spusťte: `Test-AzureStack`

Testy trvat několik minut na dokončení. Pokud byla instalace úspěšná, zobrazí výstup vypadá podobně jako:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Pokud došlo k chybě, postup řešení potíží získat nápovědu.

## <a name="activate-the-administrator-and-tenant-portals"></a>Aktivovat správce a klienta portálech
Po nasazení, které používají Azure AD je nutné aktivovat obou zásobník Azure správce a klienta portálů. Tato aktivace souhlasí s uvedením zásobník Azure portal a Azure Resource Manager správná oprávnění (uvedené na stránce souhlas) pro všechny uživatele adresáři.

- Pro správce portálu, přejděte na https://adminportal.local.azurestack.external/guest/signup, přečtěte si informace a pak klikněte na tlačítko **přijmout**. Po přijetí, můžete přidat správce služby, kteří nejsou také správci klientů adresáře.

- U klienta portálu, přejděte na https://portal.local.azurestack.external/guest/signup, přečtěte si informace a pak klikněte na tlačítko **přijmout**. Po přijetí, můžete uživatele v adresáři přihlásit na portál pro klienty. 

> [!NOTE] 
> Pokud nejsou aktivovány portálů, pouze správce adresáře může přihlásit a používat portálů. Pokud jiný uživatel přihlásí, zobrazí chybu, která sděluje, že správce nebyla udělena oprávnění ostatním uživatelům. Když správce nepatří do adresáře, který je zaregistrován v Azure zásobníku nativně, musí být adresáři Azure zásobníku připojeno k adrese URL pro aktivaci. Například pokud zásobník Azure je registrován fabrikam.onmicrosoft.com a uživatel s oprávněními správce je admin@contoso.com, přejděte na https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com aktivovat na portálu. 

## <a name="reset-the-password-expiration-policy"></a>Resetování zásad vypršení platnosti hesla 
Pokud chcete mít jistotu, že se heslo pro hostitele development kit nevyprší před ukončením svého zkušební období, postupujte podle těchto kroků po nasazení ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Změna zásad vypršení platnosti hesla z prostředí Powershell:
Z prostředí Powershell konzolu se zvýšenými oprávněními spusťte příkaz:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Změna zásad vypršení platnosti hesla ručně:
1. Na hostiteli development kit, otevřete **Správa zásad skupiny** (pro správu zásad skupiny. MMC) a přejděte do **Správa zásad skupiny** – **doménové struktury: azurestack.local** – **domén** – **azurestack.local**.
2. Klikněte pravým tlačítkem na **výchozí zásady domény** a klikněte na tlačítko **upravit**.
3. V editoru zásad skupiny správy, přejděte na **konfigurace počítače** – **zásady** – **nastavení systému Windows** – **nastavení zabezpečení**– **Zásady účtů** – **zásady hesel**.
4. V pravém podokně klikněte dvakrát na **maximální stáří hesla**.
5. V **maximální stáří hesla vlastnosti** dialogové okno, změny **heslo vyprší za** hodnotu **180**a potom klikněte na **OK**.

![Konzola pro správu zásad skupiny](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>Další postup
[Registrace ASDK s Azure](asdk-register.md)
