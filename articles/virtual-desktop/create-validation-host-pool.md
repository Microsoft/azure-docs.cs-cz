---
title: Vytvoření fondu ve verzi preview hostitele virtuální plochy Windows k ověření aktualizace služby – Azure
description: Postup vytvoření fondu ověření hostitele ke sledování služby aktualizací před zavedením aktualizace do produkčního prostředí.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: v-chjenk
ms.openlocfilehash: c9b2a593a6943fe2e9577acc61b1d5a7bcd98607
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070665"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Kurz: Vytvoření fondu hostitelů pro ověření aktualizací služeb

Hostitel fondy jsou kolekce jednoho nebo víc stejných virtuálních počítačů v prostředí klienta Windows virtuální plochy, ve verzi Preview. Před nasazením hostitele fondy do produkčního prostředí, důrazně doporučujeme že vytvořit fond ověřování hostitele. Aktualizace se použijí nejprve pro ověření hostitele fondy, umožňuje monitorování služby aktualizací před jejich zavádět do produkčního prostředí. Bez ověřování hostitele fondu nemusí zjišťovat změny, které představují chyby, které by mohly vést k výpadku pro uživatele v produkčním prostředí.

Aby vaše aplikace pracují s nejnovějšími aktualizacemi, by měl být podobný fondy hostitele v produkčním prostředí nejvíce fondu ověřování hostitele. Uživatelé by tak často, připojit k fondu ověření hostitele, stejně jako do fondu produkční hostitele. Pokud automatizované testování ve vašem fondu hostitele by měl obsahovat, automatizovaného testování ve fondu ověřování hostitele.

Můžete ladit problémy ve fondu ověření hostitele s oběma [funkce Diagnostika](diagnostics-role-service.md) nebo [článků o řešení potíží virtuální plochy Windows](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview).

>[!NOTE]
> Doporučujeme ponechat fondu ověření hostitele v místo, kde můžete testovat všechny budoucí aktualizace.

Než začnete, [stáhněte a naimportujte modul Powershellu virtuální plochy Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview), pokud jste tak již neučinili.

## <a name="create-your-host-pool"></a>Vytvoření fondu hostitele

Můžete vytvořit fond hostitele podle pokynů v některém z těchto článků:
- [Kurz: Vytvoření fondu hostitele pomocí webu Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Vytvoření fondu hostitelů pomocí šablony Azure Resource Manageru](create-host-pools-arm-template.md)
- [Vytvoření hostitele fondu pomocí Powershellu](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definovat svůj fond hostitele jako hostitele fond ověření

Spusťte následující rutiny Powershellu k definování nového fondu hostitele jako hostitele fond ověření. Nahraďte hodnoty v uvozovkách podle hodnoty, které jsou relevantní pro vaši relaci:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Spusťte následující rutinu prostředí PowerShell pro potvrzení, jestli je nastavená vlastnost ověření. Nahraďte hodnoty v uvozovkách podle hodnoty, které jsou relevantní pro vaši relaci.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Výsledky z rutiny by měly vypadat podobně jako tento výstup:

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

## <a name="update-schedule"></a>Aktualizace plánu

Ve verzi preview služba aktualizují přibližně měsíční čím dál. Pokud jsou hlavní problémy, důležité aktualizace budou poskytovat častější čím dál.

## <a name="next-steps"></a>Další postup

Teď, když jste vytvořili fond ověření hostitele, se dozvíte, jak nasadit a připojit se k nástroj pro správu pro správu prostředků služby Microsoft virtuálního klienta.

> [!div class="nextstepaction"]
> [Kurz nástroj pro správu nasazení](./manage-resources-using-ui.md)
