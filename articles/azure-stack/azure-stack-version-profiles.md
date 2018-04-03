---
title: "Pomocí profilů verze rozhraní API v Azure zásobníku | Microsoft Docs"
description: "Další informace o profilech verze rozhraní API v Azure zásobníku."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 68f4250c2a2a6bed1a1e21dc444e93cc87b6f59b
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Správa profilů verze rozhraní API v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Profily rozhraní API verze poskytují způsob, jak spravovat verze rozdíly mezi Azure a Azure zásobníku. Profilem verze rozhraní API je sada modulů Powershellu AzureRM s konkrétní verze rozhraní API. Každá platforma cloudu má sadu profilů podporované verze API. Například zásobník Azure podporuje verze konkrétní datem profilu, jako **2017-03-09profil**, a podporuje Azure **nejnovější** profil verze rozhraní API. Při instalaci profilu jsou nainstalovány na moduly Powershellu AzureRM, které odpovídají zadaný profil.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Nainstalujte modul prostředí PowerShell vyžaduje použití profilů verze rozhraní API

**AzureRM.Bootstrapper** modul, který je k dispozici prostřednictvím Galerie prostředí PowerShell poskytuje rutiny prostředí PowerShell, které jsou nutné k práci pomocí profilů verze rozhraní API. Nainstalovat modul AzureRM.Bootstrapper použijte následující rutinu:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```
Modul AzureRM.Bootstrapper je ve verzi preview; Podrobnosti a funkce se mohou změnit. Pokud chcete stáhnout a nainstalovat nejnovější verzi tento modul z Galerie prostředí PowerShell, spusťte následující rutinu:

```PowerShell
Update-Module -Name "AzureRm.BootStrapper"
```

## <a name="install-a-profile"></a>Instalace profilu

Použití **instalace AzureRmProfile** rutiny s **2017-03-09profil** profil verze rozhraní API pro instalaci modulů AzureRM vyžadovanou zásobník Azure. Všimněte si, že nejsou nainstalované moduly Azure zásobníku operátor s tímto profilem verze rozhraní API, a že by měly být nainstalovány samostatně uvedené v kroku 3 [instalaci prostředí PowerShell pro Azure zásobníku](azure-stack-powershell-install.md) článku.

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>Instalace a naimportovat moduly v profilu

Použití **použití AzureRmProfile** rutiny k instalaci a naimportovat moduly, které jsou spojeny s profilem verze rozhraní API. V relaci prostředí PowerShell lze importovat jenom jeden profil verze rozhraní API. K importu jiný profil verze rozhraní API, musíte otevřít novou relaci prostředí PowerShell. Spuštění rutiny AzureRMProfile použijte následující úlohy:  
1. Zkontroluje, jestli jsou v aktuálním oboru nainstalovat moduly Powershellu, které jsou přidružené k zadaný profil verze rozhraní API.  
2. Stáhne a nainstaluje moduly, pokud již nejsou nainstalovány.   
3. Moduly naimportuje do aktuální relace prostředí PowerShell. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

K instalaci a naimportovat moduly vybrané AzureRM z profilu verze rozhraní API, spusťte rutinu použití AzureRMProfile s **modulu** parametr:

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Získejte nainstalované profily

Použití **Get-AzureRmProfile** rutiny k získání seznamu dostupných profilů verze rozhraní API: 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```
## <a name="update-profiles"></a>Aktualizovat profily

Použití **aktualizace AzureRmProfile** rutiny aktualizovat na nejnovější verzi modulů, které jsou k dispozici v PSGallery moduly v profilu verze rozhraní API. Doporučuje se vždy spustit **aktualizace AzureRmProfile** rutiny v nové relaci prostředí PowerShell, aby nedocházelo ke konfliktům, při importu modulů. Rutina aktualizace AzureRmProfile spustí následující úlohy:

1. Zkontroluje, jestli jsou v dané profilu verze rozhraní API pro aktuální obor nainstalovat nejnovější verzi modulů.  
2. Zobrazí výzvu k instalaci, pokud již nejsou nainstalovány.  
3. Nainstaluje a naimportuje aktualizované moduly do aktuální relace prostředí PowerShell.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

Odebrat dříve nainstalovaných verzí moduly před aktualizací na nejnovější dostupnou verzi, použijte rutinu Update-AzureRmProfile spolu s **- RemovePreviousVersions** parametr:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

Tato rutina se spustí následující úlohy:  

1. Zkontroluje, jestli jsou v dané profilu verze rozhraní API pro aktuální obor nainstalovat nejnovější verzi modulů.  
2. Odebere starší verze modulů z profilu aktuální verze rozhraní API a v aktuální relaci prostředí PowerShell.  
4. Zobrazí výzvu k instalaci na nejnovější verzi.  
5. Nainstaluje a naimportuje aktualizované moduly do aktuální relace prostředí PowerShell.  
 
## <a name="uninstall-profiles"></a>Odinstalujte profily

Použití **odinstalace AzureRmProfile** rutiny odinstalace zadaný profil verze rozhraní API.

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>Další kroky
* [Instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md)
* [Konfigurace prostředí PowerShell Azure zásobník uživatele](user/azure-stack-powershell-configure-user.md)  
