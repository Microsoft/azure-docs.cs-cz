---
title: Aktualizace služby fondu hostitelů virtuální plochy Windows – Azure
description: Jak vytvořit fond hostitelů ověření pro sledování aktualizací služeb před zavedením aktualizací do produkčního prostředí.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2b51213dfc6d7e55f76e78b92d12111f84736be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79365385"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Kurz: Vytvoření fondu hostitelů pro ověření aktualizací služeb

Fondy hostitelů jsou kolekce jednoho nebo více identických virtuálních počítačů v prostředích klienta Windows Virtual Desktop. Před nasazením fondů hostitelů do produkčního prostředí důrazně doporučujeme vytvořit fond hostitelů ověření. Aktualizace se použijí nejprve na fondy hostitelů ověření, což vám umožní sledovat aktualizace služeb před jejich zavedením do produkčního prostředí. Bez fondu hostitelů ověření nemusí dojít ke změnám, které zavádějí chyby, což může mít za následek prostoje pro uživatele ve vašem produkčním prostředí.

Chcete-li zajistit, aby vaše aplikace fungovaly s nejnovějšími aktualizacemi, měl by být fond hostitelů ověření co nejvíce podobný hostitelským fondům ve vašem produkčním prostředí. Uživatelé by se měli k fondu hostitelů ověření připojovat stejně často jako k fondu hostitelů výroby. Pokud máte automatizované testování ve fondu hostitelů, měli byste zahrnout automatizované testování ve fondu hostitelů ověření.

Problémy ve fondu hostitelů ověření můžete ladit pomocí [funkce diagnostiky](diagnostics-role-service.md) nebo [článků o řešení potíží s windows virtuální plochou](troubleshoot-set-up-overview.md).

>[!NOTE]
> Doporučujeme ponechat fond hostitelů ověření na místě a otestovat všechny budoucí aktualizace.

Než začnete, [stáhněte a importujte modul Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/), pokud jste tak ještě neučinili. Poté spusťte následující rutinu a přihlaste se ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Vytvoření hostitelského fondu

Fond hostitelů můžete vytvořit podle pokynů v některém z těchto článků:
- [Kurz: Vytvoření fondu hostitelů s Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Vytvoření fondu hostitelů pomocí šablony Azure Resource Manageru](create-host-pools-arm-template.md)
- [Vytvoření fondu hostitelů pomocí PowerShellu](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definujte svůj fond hostitelů jako fond hostitelů ověření

Spusťte následující rutiny prostředí PowerShell a definujte nový fond hostitelů jako fond hostitelů ověření. Nahraďte hodnoty v uvozovkách hodnotami relevantními pro vaši relaci:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Spusťte následující rutinu prostředí PowerShell a ověřte, zda byla nastavena vlastnost ověření. Nahraďte hodnoty v uvozovkách hodnotami relevantními pro vaši relaci.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

Výsledky z rutiny by měly vypadat podobně jako tento výstup:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Plán aktualizace

Aktualizace služeb se dějí měsíčně. Pokud se jedná o závažné problémy, budou kritické aktualizace poskytovány častějším tempem.

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili fond hostitelů ověření pravosti, můžete se dozvědět, jak pomocí služby Azure Service Health sledovat nasazení virtuální plochy Windows. 

> [!div class="nextstepaction"]
> [Nastavení upozornění služeb](./set-up-service-alerts.md)
