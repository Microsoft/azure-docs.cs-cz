---
title: Použití profilů verzí API s prostředím PowerShell ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o použití profilů verzí API s prostředím PowerShell ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: c6bee5c66661f59a6287d624bf5a55428ebbe56e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238111"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>Použití profilů verzí rozhraní API pro prostředí PowerShell ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Profilů verzí API poskytují způsob, jak spravovat verze rozdíly mezi Azure a Azure Stack. Profilu verze rozhraní API je sada moduly AzureRM Powershellu s konkrétní verzí rozhraní API. Každá Cloudová platforma obsahuje sadu podporovaných profilů verzí API. Například Azure Stack podporuje verzi konkrétní průkaz s datem profilu, jako **2018-03-01hybridní**, a podporuje Azure **nejnovější** profilu verze rozhraní API. Při instalaci profilu jsou nainstalovány moduly AzureRM Powershellu, které odpovídají zadaný profil.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Nainstalujte modul prostředí PowerShell vyžaduje použití profilů verzí API

**AzureRM.Bootstrapper** modul, který je dostupný v galerii prostředí PowerShell obsahuje rutiny Powershellu, které jsou nutné k práci pomocí profilů verzí API. Použijte následující rutinu k instalaci **AzureRM.Bootstrapper** modul:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="azure-stack-version-and-profile-versions"></a>Verze služby Azure Stack a verze profilu

V následující tabulce jsou uvedeny požadovaná verze rozhraní API profilu a správce zástupný název modulu prostředí PowerShell pro nejnovější verze služby Azure Stack. Pokud používáte tento článek před. 1808 s verzí, aktualizace verze profilu a moniker správnou hodnotu.

| Číslo verze. | Profilu verze rozhraní API | PS zástupný název modulu Správce |
| --- | --- | --- |
| 1811 nebo novější | 2018-03-01-hybrid | 1.6.0 |
| 1808 nebo novější | 2018-03-01-hybrid | 1.5.0 |
| 1804 nebo novější | 2017-03-09-profile | 1.4.0 |
| Verze starší než 1804 | 2017-03-09-profile | 1.2.11 |

> [!NOTE]  
> K upgradu portálu 1.2.11 verze, najdete v článku [Průvodce migrací](https://aka.ms/azpsh130migration).

## <a name="install-a-profile"></a>Instalace profilu

Použití **rutina Install-AzureRmProfile** rutinu s **2018-03-01hybridní** profilu verze rozhraní API pro instalaci požadované moduly AzureRM ve službě Azure Stack. S tímto profilem verze rozhraní API nejsou nainstalované moduly operátor Azure stacku. By měly být nainstalovány samostatně uvedené v kroku 3 [instalace Powershellu pro Azure Stack](../azure-stack-powershell-install.md) článku.

```PowerShell
Install-AzureRMProfile -Profile 2018-03-01-hybrid
```

## <a name="install-and-import-modules-in-a-profile"></a>Nainstalovat a importovat moduly v profilu

Použití **použijte-AzureRmProfile** k instalaci a importovat moduly, které jsou spojeny s profilem verze rozhraní API. V relaci Powershellu můžete importovat pouze v jednom profilu verze rozhraní API. Chcete-li importovat profil různé verze rozhraní API, musíte otevřít novou relaci Powershellu. **Použijte-AzureRMProfile** rutina provede následující úlohy:

1. Kontroluje, pokud jsou nainstalované moduly Powershellu, který je přidružený k profilu zadaná verze rozhraní API v aktuálním oboru.  
2. Stáhne a nainstaluje moduly, pokud ještě nejsou nainstalovány.
3. Importuje modulů do aktuální relace prostředí PowerShell.

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser -Force
```

Chcete-li nainstalovat a importovat vybrané moduly AzureRM profilu verze rozhraní API, spusťte **použijte-AzureRMProfile** rutinu s **modulu** parametr:

```PowerShell
# Installs and imports the compute, storage and network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Získání nainstalovaných profily

Použití **Rutina Get-AzureRmProfile** rutiny pro získání seznamu dostupných profilů verzí API:

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```

## <a name="update-profiles"></a>Aktualizovat profily

Použití **rutina Update-AzureRmProfile** rutina pro aktualizaci na nejnovější verzi modulů, které jsou k dispozici v PSGallery moduly v profilu verze rozhraní API. Doporučuje se, že vždy spustit **rutina Update-AzureRmProfile** rutiny v nové relaci Powershellu, aby nedocházelo ke konfliktům, při importu modulů. **Rutina Update-AzureRmProfile** spustí následující úlohy:

1. Kontroluje se, pokud jsou nainstalované nejnovější verze modulů v daném profilu verze rozhraní API pro aktuální obor.  
2. Zobrazí výzvu k instalaci, pokud požadavky nejsou nainstalovány.  
3. Nainstaluje a importuje aktualizovaných modulů do aktuální relace prostředí PowerShell.  

```PowerShell
Update-AzureRmProfile -Profile 2018-03-01-hybrid
```

<!-- To remove the previously installed versions of the modules before updating to the latest available version, use the Update-AzureRmProfile cmdlet along with the **-RemovePreviousVersions** parameter:

```PowerShell 
Update-AzureRmProfile -Profile 2018-03-01-hybrid -RemovePreviousVersions
``` -->

Tato rutina spustí následující úlohy:  

1. Kontroluje se, pokud jsou nainstalované nejnovější verze modulů v daném profilu verze rozhraní API pro aktuální obor.  
2. Odebere starší verze modulů z profilu aktuální verze rozhraní API a v aktuální relaci Powershellu.  
3. Zobrazí výzvu k instalaci nejnovější verze.  
4. Nainstaluje a importuje aktualizovaných modulů do aktuální relace prostředí PowerShell.  

## <a name="uninstall-profiles"></a>Odinstalace profily

Použití **Uninstall-AzureRmProfile** rutiny odinstalace zadaného profilu verze rozhraní API.

```PowerShell
Uninstall-AzureRmProfile -Profile  2018-03-01-hybrid
```

## <a name="next-steps"></a>Další postup

* [Instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md)
* [Konfigurace prostředí PowerShell uživatele Azure stacku](azure-stack-powershell-configure-user.md)  
