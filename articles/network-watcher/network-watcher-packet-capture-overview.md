---
title: Seznámení s zachytáváním paketů v Azure Network Watcher | Microsoft Docs
description: Tato stránka poskytuje přehled funkce zachytávání paketů Network Watcher.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76840804"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Seznámení s proměnným zachytávání paketů v Azure Network Watcher

Network Watcher variabilní zachytávání paketů umožňuje vytvářet relace zachytávání paketů pro sledování provozu do a z virtuálního počítače. Zachytávání paketů pomáhá diagnostikovat anomálie sítě interaktivně a aktivně. Mezi další použití patří shromažďování statistik sítě a získání informací o neoprávněných vniknutích k síti, k ladění komunikace mezi klientem a serverem a mnohem více.

Zachytávání paketů je rozšíření virtuálního počítače, které se vzdáleně spouští prostřednictvím Network Watcher. Tato schopnost usnadňuje zatížení při ručním zachytávání paketů na požadovaném virtuálním počítači, který šetří cenné časy. Zachytávání paketů se dá aktivovat prostřednictvím portálu, PowerShellu, CLI nebo REST API. Jedním z příkladů, jak se dá zachytávání paketů aktivovat, jsou výstrahy virtuálního počítače. K dispozici jsou filtry pro relaci zachycení, aby bylo možné zachytit provoz, který chcete monitorovat. Filtry jsou založené na 5-tice (protokol, místní IP adresa, vzdálená IP adresa, místní port a vzdálený port). Zachycená data jsou uložená na místním disku nebo v objektu BLOB úložiště. Limit 10 relací zachytávání paketů je omezen na jednu oblast a předplatné. Toto omezení platí jenom pro relace a nevztahují se na uložené soubory zachytávání paketů místně na virtuálním počítači nebo v účtu úložiště.

> [!IMPORTANT]
> Zachytávání paketů vyžaduje rozšíření virtuálního počítače `AzureNetworkWatcherExtension` . Pokud chcete nainstalovat rozšíření na virtuální počítač s Windows, přejděte na web [azure Network Watcher Agent Virtual Machine Extension for Windows](../virtual-machines/windows/extensions-nwa.md) a pro Linux VM, navštivte [rozšíření Azure Network Watcher Agent Virtual Machine pro Linux](../virtual-machines/linux/extensions-nwa.md).

Chcete-li omezit informace, které zachytíte pouze na požadované informace, jsou k dispozici následující možnosti pro relaci zachytávání paketů:

**Konfigurace zachycení**

|Vlastnost|Popis|
|---|---|
|**Maximální počet bajtů na paket (bajty)** | Počet bajtů z každého zaznamenaného paketu, pokud je ponecháno prázdné, jsou zachyceny všechny bajty. Počet bajtů z každého zaznamenaného paketu, pokud je ponecháno prázdné, jsou zachyceny všechny bajty. Pokud potřebujete jenom hlavičku IPv4 – uveďte 34 tady. |
|**Maximální počet bajtů na relaci (bajty)** | Celkový počet bajtů, které jsou zachyceny, jakmile je hodnota dosažena na konci relace.|
|**Časový limit (sekundy)** | Nastaví časové omezení relace zachytávání paketů. Výchozí hodnota je 18000 sekund nebo 5 hodin.|

**Filtrování (volitelné)**

|Vlastnost|Popis|
|---|---|
|**Protokol** | Protokol, který se má vyfiltrovat pro zachytávání paketů. Dostupné hodnoty jsou TCP, UDP a All.|
|**Místní IP adresa** | Tato hodnota filtruje zachytávání paketů do paketů, u kterých místní IP adresa odpovídá této hodnotě filtru.|
|**Místní port** | Tato hodnota filtruje zachytávání paketů do paketů, kde místní port odpovídá této hodnotě filtru.|
|**Vzdálená IP adresa** | Tato hodnota filtruje zachytávání paketů do paketů, ve kterých Vzdálená IP adresa odpovídá této hodnotě filtru.|
|**Vzdálený port** | Tato hodnota filtruje zachytávání paketů do paketů, ve kterých vzdálený port odpovídá hodnotě tohoto filtru.|

### <a name="next-steps"></a>Další kroky

Přečtěte si, jak spravovat zachycení paketů prostřednictvím portálu návštěvou [Spravovat zachytávání paketů v Azure Portal](network-watcher-packet-capture-manage-portal.md) nebo pomocí PowerShellu, a to návštěvou [správy zachytávání paketů pomocí PowerShellu](network-watcher-packet-capture-manage-powershell.md).

Přečtěte si, jak vytvořit proaktivní zachycení paketů na základě výstrah virtuálních počítačů, a to návštěvou [Vytvoření výstrahy aktivované při zachytávání paketů](network-watcher-alert-triggered-packet-capture.md) .

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













