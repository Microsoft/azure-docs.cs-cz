---
title: Aktualizace služby fondu hostitelů virtuálních počítačů s Windows – Azure
description: Jak vytvořit fond ověřovacích hostitelů pro monitorování aktualizací služby před výstupem aktualizací do produkčního prostředí.
author: Heidilohr
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ead4c0aa7d8d71642fd8a4635edbabcafee5b6c2
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563241"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Kurz: Vytvoření fondu hostitelů pro ověření aktualizací služby

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).

Fondy hostitelů jsou kolekce jednoho nebo více identických virtuálních počítačů v prostředí virtuálních počítačů s Windows. Důrazně doporučujeme vytvořit fond hostitelů ověřování, ve kterém se aktualizace služby používají jako první. To vám umožní monitorovat aktualizace služby předtím, než je služba použije pro prostředí Standard nebo bez ověřování. Bez hostitelského fondu pro ověřování nesmíte zjišťovat změny, které zavádějí chyby, což by mohlo vést k výpadkům uživatelů ve vašem standardním prostředí.

Pokud chcete zajistit, aby vaše aplikace pracovaly s nejnovějšími aktualizacemi, fond hostitelů ověření by měl být podobný fondům hostitelů ve vašem prostředí bez ověřování. Uživatelé by se měli často připojit k fondu ověřování pro ověřování, protože to dělají do fondu hostitelů úrovně Standard. Pokud máte ve fondu hostitelů automatizované testování, měli byste zahrnout automatizované testování do fondu ověřovacích hostitelů.

Problémy v hostitelském fondu ověřování můžete ladit buď pomocí [diagnostické funkce](diagnostics-role-service.md) , nebo článků pro [řešení potíží s virtuálním počítačem s Windows](troubleshoot-set-up-overview.md).

>[!NOTE]
> Pro otestování všech budoucích aktualizací doporučujeme opustit fond hostitelů ověřování na místě.

>[!IMPORTANT]
>Virtuální plocha Windows s integrací správy prostředků Azure v současné době má potíže při povolování a zakazování ověřovacích prostředí. Tento článek aktualizujeme, až problém vyřešíme.

## <a name="prerequisites"></a>Předpoklady

Než začnete, postupujte podle pokynů v části [nastavení modulu Azure Virtual Desktop PowerShell](powershell-module.md) a nastavte modul prostředí PowerShell a přihlaste se k Azure.

## <a name="create-your-host-pool"></a>Vytvoření fondu hostitelů

Fond hostitelů můžete vytvořit podle pokynů uvedených v některém z těchto článků:
- [Kurz: Vytvoření fondu hostitelů pomocí Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Vytvoření fondu hostitelů pomocí PowerShellu](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Zadejte fond hostitelů jako fond pro ověření ověřování.

Spusťte následující rutiny PowerShellu k definování nového fondu hostitelů jako fondu hostitelů ověření. Hodnoty v závorkách nahraďte hodnotami, které jsou relevantní pro vaši relaci:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

Spuštěním následující rutiny prostředí PowerShell potvrďte, že byla nastavena vlastnost ověření. Hodnoty v závorkách nahraďte hodnotami, které jsou relevantní pro vaši relaci.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

Výsledky rutiny by měly vypadat podobně jako tento výstup:

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="enable-your-validation-environment-with-the-azure-portal"></a>Povolení prostředí pro ověřování pomocí Azure Portal

K povolení prostředí ověřování můžete použít taky Azure Portal.

Chcete-li použít Azure Portal ke konfiguraci fondu ověřovacích hostitelů:

1. Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com>.
2. Vyhledejte a vyberte **virtuální počítač s Windows**.
3. Na stránce virtuální počítač s Windows vyberte **fondy hostitelů**.
4. Vyberte název fondu hostitelů, který chcete upravit.
5. Vyberte **Vlastnosti**.
6. V poli prostředí ověřování vyberte **Ano** , pokud chcete povolit prostředí ověřování.
7. Vyberte **Uložit**. Tím se použijí nová nastavení.

## <a name="update-schedule"></a>Aktualizovat plán

Aktualizace služby nastávají měsíčně. Pokud dojde k zásadním problémům, budou důležité aktualizace k dispozici při častější rychlosti.

Pokud jsou k dispozici nějaké aktualizace služby, ujistěte se, že máte alespoň malou skupinu uživatelů, kteří si každý den přihlašujei a ověřují prostředí. Doporučujeme, abyste pravidelně navštívili náš [Web TechCommunity](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) a provedli jakékoli příspěvky s WVDUPdate, abyste mohli průběžně informovat o aktualizacích služby.

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili fond hostitelů pro ověření, můžete zjistit, jak použít Azure Service Health k monitorování nasazení virtuálních klientů Windows.

> [!div class="nextstepaction"]
> [Nastavení upozornění služeb](./set-up-service-alerts.md)
