---
title: Úvod k zachycení paketů v Azure Network Watcher | Dokumenty společnosti Microsoft
description: Tato stránka obsahuje přehled funkce zachycení paketů Sledování sítě
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 1f17463125cead64bd58a2d07e53eee4d3cfcd70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840804"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Úvod k zachycení proměnných paketů v Azure Network Watcher

Zachycení proměnné paketu Network Watcher umožňuje vytvářet relace sběru paketů pro sledování provozu do a z virtuálního počítače. Zachytávání paketů pomáhá diagnostikovat anomálie sítě reaktivně i proaktivně. Mezi další použití patří shromažďování síťových statistik, získávání informací o síťových vniknutích, ladění komunikace mezi klientem a serverem a mnoho dalšího.

Sběr paketů je rozšíření virtuálního počítače, které je vzdáleně spuštěno prostřednictvím sledovacího programu sítě. Tato funkce zmírňuje zátěž ručního spuštění sběru paketů na požadovaném virtuálním počítači, což šetří cenný čas. Sběr paketů lze spustit prostřednictvím portálu, powershellu, rozhraní CLI nebo rozhraní REST API. Jedním z příkladů, jak lze aktivovat zachytávání paketů, jsou výstrahy virtuálního počítače. Pro relaci sběru jsou k dispozici filtry, které zajišťují zachycení provozu, který chcete sledovat. Filtry jsou založeny na 5-n-tice (protokol, místní IP adresa, vzdálená IP adresa, místní port a vzdálený port) informace. Zachycená data jsou uložena na místním disku nebo v objektu blob úložiště. Existuje limit 10 relací zachytávání paketů na oblast na předplatné. Toto omezení platí jenom pro relace a nevztahuje se na uložené soubory pro digitalizaci paketů místně na virtuálním počítači nebo v účtu úložiště.

> [!IMPORTANT]
> Zachytávání paketů `AzureNetworkWatcherExtension`vyžaduje rozšíření virtuálního počítače . Pokud chcete nainstalovat rozšíření na virtuální počítač s Windows, navštivte [rozšíření virtuálního počítače Azure Network Watcher Agent pro Windows](../virtual-machines/windows/extensions-nwa.md) a pro virtuální počítač SIP na stránce Rozšíření [virtuálního počítače Azure Network Watcher Agent pro Linux](../virtual-machines/linux/extensions-nwa.md).

Chcete-li zredukovat zachycené informace pouze na požadované informace, jsou pro relaci sběru paketů k dispozici následující možnosti:

**Konfigurace zachycení**

|Vlastnost|Popis|
|---|---|
|**Maximální počet bajtů na paket (bajty)** | Počet bajtů z každého paketu, které jsou zachyceny, všechny bajty jsou zachyceny, pokud jsou ponechány prázdné. Počet bajtů z každého paketu, které jsou zachyceny, všechny bajty jsou zachyceny, pokud jsou ponechány prázdné. Pokud potřebujete pouze hlavičku IPv4 – uveďte 34 zde |
|**Maximální počet bajtů na relaci (bajty)** | Celkový počet bajtů v které jsou zachyceny, jakmile je dosaženo hodnoty relace končí.|
|**Časový limit (v sekundách)** | Nastaví časové omezení relace sběru paketů. Výchozí hodnota je 18000 sekund nebo 5 hodin.|

**Filtrování (volitelné)**

|Vlastnost|Popis|
|---|---|
|**Protokol** | Protokol pro filtrování sběru paketů. Dostupné hodnoty jsou TCP, UDP a All.|
|**Místní IP adresa** | Tato hodnota filtruje sběr paketů na pakety, u kterých místní adresa IP odpovídá této hodnotě filtru.|
|**Místní port** | Tato hodnota filtruje sběr paketů na pakety, u kterých místní port odpovídá této hodnotě filtru.|
|**Vzdálená IP adresa** | Tato hodnota filtruje sběr paketů na pakety, u kterých vzdálená adresa IP odpovídá této hodnotě filtru.|
|**Vzdálený port** | Tato hodnota filtruje sběr paketů na pakety, u kterých vzdálený port odpovídá této hodnotě filtru.|

### <a name="next-steps"></a>Další kroky

Zjistěte, jak můžete spravovat zachytávání paketů prostřednictvím portálu tak, že navštívíte [Spravovat zachytávání paketů na webu Azure portal](network-watcher-packet-capture-manage-portal.md) nebo s PowerShellem na stránce Správa [zachytávání paketů pomocí Prostředí PowerShell](network-watcher-packet-capture-manage-powershell.md).

Zjistěte, jak vytvořit proaktivní zachytávání paketů na základě upozornění virtuálních strojů na stránce [Vytvoření záznamu aktivovaného zachycení paketů](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













