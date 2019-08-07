---
title: Vytvoření fondu hostitelů ve verzi Preview pro virtuální počítače s Windows pro ověření aktualizací služby – Azure
description: Jak vytvořit fond ověřovacích hostitelů pro monitorování aktualizací služby před výstupem aktualizací do produkčního prostředí.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: helohr
ms.openlocfilehash: 732192e481f88c0cd3c78e9dac43970eb5be72e6
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840517"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Kurz: Vytvoření fondu hostitelů pro ověření aktualizací služeb

Fondy hostitelů jsou kolekce jednoho nebo více identických virtuálních počítačů v prostředích klienta ve verzi Preview virtuálních počítačů s Windows. Před nasazením fondů hostitelů do provozního prostředí důrazně doporučujeme vytvořit fond hostitelů ověřování. Aktualizace se používají jako první k ověření fondů hostitelů, takže můžete sledovat aktualizace služby, než je zavedete do produkčního prostředí. Bez hostitelského fondu pro ověřování nesmíte zjišťovat změny, které zavádějí chyby, což by mohlo vést k výpadkům uživatelů v produkčním prostředí.

Pokud chcete zajistit, aby vaše aplikace pracovaly s nejnovějšími aktualizacemi, fond hostitelů ověření by měl být podobně jako fondy hostitelů ve vašem produkčním prostředí. Uživatelé by se měli často připojovat k fondu ověřování pro ověřování stejně jako k produkčnímu fondu hostitelů. Pokud máte ve fondu hostitelů automatizované testování, měli byste zahrnout automatizované testování do fondu ověřovacích hostitelů.

Problémy v hostitelském fondu ověřování můžete ladit buď pomocí [diagnostické funkce](diagnostics-role-service.md) , nebo článků pro [řešení potíží s virtuálním počítačem s Windows](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview).

>[!NOTE]
> Pro otestování všech budoucích aktualizací doporučujeme opustit fond hostitelů ověřování na místě.

Než začnete, [Stáhněte a importujte modul PowerShell virtuálního klienta Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview), pokud jste to ještě neudělali.

## <a name="create-your-host-pool"></a>Vytvoření fondu hostitelů

Fond hostitelů můžete vytvořit podle pokynů uvedených v některém z těchto článků:
- [Kurz: Vytvoření fondu hostitelů pomocí Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Vytvoření fondu hostitelů pomocí šablony Azure Resource Manager](create-host-pools-arm-template.md)
- [Vytvoření fondu hostitelů pomocí prostředí PowerShell](create-host-pools-powershell.md)

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
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Aktualizovat plán

Ve verzi Preview se aktualizace služby vyskytují přibližně měsíčně tempo. V případě závažných problémů budou důležité aktualizace k dispozici na častých tempo.

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili fond hostitelů pro ověření, si můžete přečíst, jak nasadit a připojit se k nástroji pro správu pro správu prostředků virtuálních ploch Microsoftu.

> [!div class="nextstepaction"]
> [Kurz nasazení nástroje pro správu](./manage-resources-using-ui.md)
