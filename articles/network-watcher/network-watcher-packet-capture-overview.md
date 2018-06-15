---
title: Úvod do zachytáváním paketů v sledovací proces sítě Azure | Microsoft Docs
description: Tato stránka obsahuje přehled funkce zachytávání paketů sledovací proces sítě
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 3a81afaa-ecd9-4004-b68e-69ab56913356
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 152cc8fb61aa6115c7b5863e4d798db9e7aa5b7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864291"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Úvod do proměnné paketu zachycení v sledovací proces sítě Azure

Zachycení dat ze sítě sledovacích procesů proměnné paketů umožňuje vytvářet relace zachytávání paketů sledovat provoz do a z virtuálního počítače. Pomáhá zachytávání paketů při diagnostice sítě anomálií reaktivně a proactivity. Jiné účely zahrnují shromažďování statistiku sítě, získá informace o síti vniknutí, k ladění komunikaci klienta se serverem a mnoho dalšího.

Zachytáváním paketů je rozšíření virtuálního počítače, který se spustil vzdáleně přes sledovací proces sítě. Tato funkce snižuje zátěž spuštěných zachytáváním paketů ručně na požadovaný virtuální počítač, který úspora času. Zachytáváním paketů můžete spustit prostřednictvím portálu, prostředí PowerShell, rozhraní příkazového řádku nebo REST API. Je jeden příklad, jak můžete spustit zachytáváním paketů s výstrahami virtuálního počítače. Filtry jsou k dispozici pro relaci zachytávání zajistit, že zaznamenávat provoz, který chcete monitorovat. Filtry jsou založené na 5-n-tice (protokol, místní IP adresu, vzdálené IP adresy, místního portu a vzdáleného portu) informace. Zachycená data se ukládají v místním disku nebo objekt blob úložiště. Existuje omezení 10 zachytávání relací paketů na oblast na předplatné. Toto omezení se vztahuje pouze na relací a nebude použitelná pro soubory uložené paketu zaznamenat místně na virtuálním počítači nebo v účtu úložiště.

> [!IMPORTANT]
> Rozšíření virtuálního počítače vyžaduje zachytáváním paketů `AzureNetworkWatcherExtension`. Instalaci rozšíření na virtuální počítač s Windows najdete v článku [rozšíření virtuálního počítače Azure sítě sledovacích procesů agenta pro Windows](../virtual-machines/windows/extensions-nwa.md) a u virtuálního počítače s Linuxem, navštivte [rozšíření virtuálního počítače Azure sítě sledovacích procesů agenta pro Linux](../virtual-machines/linux/extensions-nwa.md).

Pokud chcete informace, které zaznamenáte na informace, které chcete zkrátit, jsou následující možnosti k dispozici pro relaci zachytávání paketů:

**Zachycení konfigurace**

|Vlastnost|Popis|
|---|---|
|**Maximální počet bajtů paketu (bajty)** | Počet bajtů z jednotlivých paketů, které jsou zachyceny, všechny bajty zachyceny, pokud je ponecháno prázdné. Počet bajtů z jednotlivých paketů, které jsou zachyceny, všechny bajty zachyceny, pokud je ponecháno prázdné. Pokud budete potřebovat pouze hlavičce IPv4 – označuje 34 sem |
|**Maximální počet bajtů za relace (bajty)** | Celkový počet bajtů v tom, že se zaznamená, hodnota v případě dosažení neukončí relace.|
|**Časový limit (sekundy)** | Sady času omezení paketu zaznamenání relace. Výchozí hodnota je 18000 sekund nebo 5 hodin.|

**Filtrování (volitelné)**

|Vlastnost|Popis|
|---|---|
|**Protokol** | Protokol pro filtrování pro zachytávání paketů. Dostupné hodnoty jsou TCP, UDP a všechny.|
|**Místní IP adresu** | Tato hodnota filtruje zachytáváním paketů na pakety, kde místní IP adresa odpovídá této hodnotě filtru.|
|**Místního portu** | Tato hodnota filtruje zachytáváním paketů na pakety, kde místního portu odpovídá této hodnotě filtru.|
|**Vzdálené IP adresy** | Tato hodnota filtruje zachytáváním paketů na pakety, kde vzdálené IP odpovídá této hodnotě filtru.|
|**Vzdálený port** | Tato hodnota filtruje zachytáváním paketů na pakety, kde vzdálený port odpovídá této hodnotě filtru.|

### <a name="next-steps"></a>Další kroky

Zjistěte, jak můžete spravovat zachycení paketů prostřednictvím portálu navštivte stránky [spravovat zachytáváním paketů na portálu Azure](network-watcher-packet-capture-manage-portal.md) nebo pomocí prostředí PowerShell, navštivte stránky [spravovat pomocí prostředí PowerShell zachytávání paketů](network-watcher-packet-capture-manage-powershell.md).

Naučte se vytvářet proaktivní paketu zachycení založeny na výstrahách virtuálního počítače, navštivte stránky [vytvořit zaznamenání výstrahy spouštěná paketu](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













