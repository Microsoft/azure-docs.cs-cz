---
title: zahrnout soubor
description: zahrnout soubor
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 01/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0df38533afe97f010d1050c3ee2a4a69a54d4cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335101"
---
| Prostředek | Omezení |
| --- | --- |
| Datová propustnost |30 Gb/s<sup>1</sup> |
|Pravidla|10,000. Všechny typy pravidel v kombinaci.|
|Maximální pravidla DNAT|299|
|Minimální velikost azurefirewallsubnetu |/26|
|Rozsah portů v pravidlech sítě a aplikací|0-64,000. Probíhá práce na uvolnění tohoto omezení.|
|Veřejné IP adresy|Maximálně 100 (V současné době jsou porty SNAT přidány pouze pro prvních pět veřejných IP adres.)|
|Tabulka směrování|Ve výchozím nastavení má azurefirewallsubnet trasu 0.0.0.0/0 s hodnotou NextHopType nastavenou na **Internet**.<br><br>Azure Firewall musí mít přímé připojení k Internetu. Pokud se vaše síť AzureFirewallSubnet učí výchozí trasu do místní sítě prostřednictvím protokolu BGP, musíte ji přepsat udr 0.0.0.0/0 s hodnotou **NextHopType** nastavenou jako **Internet,** aby bylo zachováno přímé připojení k Internetu. Ve výchozím nastavení azure firewall nepodporuje vynucené tunelové propojení do místní sítě.<br><br>Pokud však vaše konfigurace vyžaduje vynucené tunelové propojení do místní sítě, bude ji společnost Microsoft podporovat případ od případu. Kontaktujte podporu, abychom mohli váš případ zkontrolovat. Pokud bude přijato, povolíme vaše předplatné a zajistíme, aby bylo zachováno požadované připojení k internetu brány firewall.|

<sup>1.</sup> Pokud potřebujete zvýšit tato omezení, obraťte se na podporu Azure.
