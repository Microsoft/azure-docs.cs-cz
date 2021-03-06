---
title: Aktualizace služby fondu hostitelů virtuálních počítačů (Classic) – Azure
description: Naučte se vytvořit fond hostitelů ověření ve virtuální ploše Windows (Classic), abyste mohli aktualizace služby monitorovat před výstupem aktualizací do produkčního prostředí.
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: ad79ad31678f698c0f034b39bab1e3a19a48d3f2
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444968"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-in-windows-virtual-desktop-classic"></a>Kurz: Vytvoření fondu hostitelů pro ověření aktualizací služby ve virtuální ploše Windows (Classic)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../create-validation-host-pool.md).

Fondy hostitelů jsou kolekce jednoho nebo více identických virtuálních počítačů v prostředích klienta virtuálních počítačů s Windows. Doporučujeme vytvořit fond hostitelů ověřování, ve kterém se aktualizace služby používají jako první. To vám umožní monitorovat aktualizace služby předtím, než je služba použije pro prostředí Standard nebo bez ověřování. Bez hostitelského fondu pro ověřování nesmíte zjišťovat změny, které zavádějí chyby, což by mohlo vést k výpadkům uživatelů v produkčním prostředí.

Pokud chcete zajistit, aby vaše aplikace pracovaly s nejnovějšími aktualizacemi, fond hostitelů ověření by měl být podobný fondům hostitelů ve vašem prostředí bez ověřování. Uživatelé by se měli často připojit k fondu ověřování pro ověřování, protože to dělají do fondu hostitelů úrovně Standard. Pokud máte ve fondu hostitelů automatizované testování, měli byste zahrnout automatizované testování do fondu ověřovacích hostitelů.

Problémy v hostitelském fondu ověřování můžete ladit buď pomocí [diagnostické funkce](diagnostics-role-service-2019.md) , nebo článků pro [řešení potíží s virtuálním počítačem s Windows](troubleshoot-set-up-overview-2019.md).

>[!NOTE]
> Pro otestování všech budoucích aktualizací doporučujeme opustit fond hostitelů ověřování na místě.

Než začnete, [Stáhněte a importujte modul PowerShell virtuálního klienta Windows](/powershell/windows-virtual-desktop/overview/), pokud jste to ještě neudělali. Potom spuštěním následující rutiny se přihlaste ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Vytvoření fondu hostitelů

Fond hostitelů můžete vytvořit podle pokynů uvedených v některém z těchto článků:
- [Kurz: Vytvoření fondu hostitelů pomocí Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
- [Vytvoření fondu hostitelů pomocí šablony Azure Resource Manageru](create-host-pools-arm-template.md)
- [Vytvoření fondu hostitelů pomocí PowerShellu](create-host-pools-powershell-2019.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Zadejte fond hostitelů jako fond pro ověření ověřování.

Spusťte následující rutiny PowerShellu k definování nového fondu hostitelů jako fondu hostitelů ověření. Hodnoty v uvozovkách nahraďte hodnotami, které jsou relevantní pro vaši relaci:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Spuštěním následující rutiny prostředí PowerShell potvrďte, že byla nastavena vlastnost ověření. Hodnoty v uvozovkách nahraďte hodnotami, které jsou relevantní pro vaši relaci.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

Výsledky rutiny by měly vypadat podobně jako tento výstup:

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

## <a name="update-schedule"></a>Aktualizovat plán

Aktualizace služby nastávají měsíčně. Pokud dojde k zásadním problémům, budou důležité aktualizace k dispozici při častější rychlosti.

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili fond hostitelů pro ověření, můžete zjistit, jak použít Azure Service Health k monitorování nasazení virtuálních klientů Windows.

> [!div class="nextstepaction"]
> [Nastavení upozornění služeb](set-up-service-alerts-2019.md)
