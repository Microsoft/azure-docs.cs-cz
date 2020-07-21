---
title: Aktualizace služby fondu hostitelů virtuálních počítačů s Windows (2019) – Azure
description: Naučte se, jak vytvořit fond hostitelů pro ověření, abyste mohli monitorovat aktualizace služby před výstupem aktualizací do produkčního prostředí.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 806c3396d9188ea6abc5f779a26d99247d802ebe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527576"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-fall-2019-release"></a>Kurz: Vytvoření fondu hostitelů pro ověření aktualizací služby (vydání 2019)

>[!IMPORTANT]
>Tento obsah se vztahuje na verzi 2019, která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objekty virtuálních klientů Windows, které byly zavedeny v aktualizaci jarní 2020, přečtěte si [Tento článek](../create-validation-host-pool.md).

Fondy hostitelů jsou kolekce jednoho nebo více identických virtuálních počítačů v prostředích klienta virtuálních počítačů s Windows. Před nasazením fondů hostitelů do provozního prostředí důrazně doporučujeme vytvořit fond hostitelů ověřování. Aktualizace se používají jako první k ověření fondů hostitelů, takže můžete sledovat aktualizace služby, než je zavedete do produkčního prostředí. Bez hostitelského fondu pro ověřování nesmíte zjišťovat změny, které zavádějí chyby, což by mohlo vést k výpadkům uživatelů v produkčním prostředí.

Pokud chcete zajistit, aby vaše aplikace pracovaly s nejnovějšími aktualizacemi, fond hostitelů ověření by měl být podobně jako fondy hostitelů ve vašem produkčním prostředí. Uživatelé by se měli často připojovat k fondu ověřování pro ověřování stejně jako k produkčnímu fondu hostitelů. Pokud máte ve fondu hostitelů automatizované testování, měli byste zahrnout automatizované testování do fondu ověřovacích hostitelů.

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
