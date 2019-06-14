---
title: Úvod k zachytávání paketů ve službě Azure Network Watcher | Dokumentace Microsoftu
description: Tato stránka obsahuje základní informace o funkci zachytávání paketů Network Watcher
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 3a81afaa-ecd9-4004-b68e-69ab56913356
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 8ef9da86377ac6f1b012cb0ebfd9d6866bc0c620
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061586"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Úvod do proměnné zachytávání paketů ve službě Azure Network Watcher

Network Watcher proměnné zachytávání paketů umožňuje vytvořit relace zachycení paketů sledujte provoz do a z virtuálního počítače. Zachytávání paketů pomáhá diagnostikovat sítě anomálie reaktivně a proaktivně. Mezi další použití patří shromažďování statistických údajů sítě získat informace o síti vniknutí, chcete-li ladit komunikaci klienta se serverem a spoustu dalších věcí.

Zachytávání paketů se rozšíření virtuálního počítače, která je spuštěna vzdáleně prostřednictvím Network Watcher. Tato schopnost snižuje zátěž ručně spustit zachytávání paketů na požadovaný virtuální počítač, což šetří cenný čas. Zachytávání paketů se dá spouštět na portálu, Powershellu, rozhraní příkazového řádku nebo rozhraní REST API. Jeden příklad, jak se dá spouštět zachytávání paketů se virtuální počítač výstrahy. Filtry jsou k dispozici pro relace zachytávání zajistit, že zaznamenávat provoz, který chcete monitorovat. Filtry jsou založeny na 5-řazené kolekce členů (protokol, místní IP adresa, Vzdálená IP adresa, místní port a vzdálených portů) informace. Zachycená data se ukládají v místním disku nebo objekt blob úložiště. Existuje limit 10 relace zachycení paketů na oblast a předplatné. Toto omezení se vztahuje pouze na relace a neplatí pro soubory uložené paketů zachycení místně na virtuálním počítači nebo v účtu úložiště.

> [!IMPORTANT]
> Zachytávání paketů vyžaduje rozšíření virtuálního počítače `AzureNetworkWatcherExtension`. Instalaci rozšíření na virtuálním počítači s Windows najdete [rozšíření virtuálního počítače Azure Network Watcher Agent pro Windows](../virtual-machines/windows/extensions-nwa.md) a pro virtuální počítač s Linuxem, navštivte [rozšíření virtuálního počítače Azure Network Watcher Agent pro Linux](../virtual-machines/linux/extensions-nwa.md).

Informace, které zaznamenáte pouze informací, které chcete omezit, následující možnosti jsou k dispozici pro relace zachytávání paketů:

**Zachycení konfigurace**

|Vlastnost|Popis|
|---|---|
|**Maximální počet bajtů na paket (bajty)** | Počet bajtů z jednotlivých paketů, které jsou zachyceny, jsou zachyceny všechny bajty, pokud je ponecháno prázdné. Počet bajtů z jednotlivých paketů, které jsou zachyceny, jsou zachyceny všechny bajty, pokud je ponecháno prázdné. Pokud potřebujete pouze záhlaví protokolu IPv4 – označuje 34 zde |
|**Maximální počet bajtů na relaci (bajty)** | Celkový počet bajtů, které jsou zachyceny hodnota po dosažení konce relace.|
|**Časový limit (sekundy)** | Nastaví dobu omezení paketu relace zachycení. Výchozí hodnota je 18000 sekund nebo 5 hodin.|

**Filtrování (nepovinné)**

|Vlastnost|Popis|
|---|---|
|**Protokol** | Protokol pro filtrování pro zachycení paketů. Dostupné hodnoty jsou TCP, UDP a všechny.|
|**Místní IP adresa** | Tato hodnota filtry zachytávání paketů na pakety, kde místní IP adresa odpovídá této hodnotě filtru.|
|**Místní port** | Tuto hodnotu filtry zachytávání paketů na pakety, kde na místní port odpovídá této hodnotě filtru.|
|**Vzdálená IP adresa** | Tuto hodnotu filtry paketů zachycení paketů, kde vzdálené IP odpovídá této hodnotě filtru.|
|**Vzdálený port** | Tuto hodnotu filtry paketů zachycení paketů, kde vzdálený port odpovídá této hodnotě filtru.|

### <a name="next-steps"></a>Další postup

Zjistěte, jak můžete spravovat zachytávání paketů prostřednictvím portálu návštěvou [Správa zachytávání paketů na webu Azure Portal](network-watcher-packet-capture-manage-portal.md) nebo pomocí Powershellu návštěvou [Správa zachytávání paketů pomocí prostředí PowerShell](network-watcher-packet-capture-manage-powershell.md).

Zjistěte, jak vytvořit zachytávání proaktivní paketů, které jsou založeny na výstrahách virtuální počítač si [vytvořit zachytávání paketů upozornění aktivovaných](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













