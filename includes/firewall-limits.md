---
title: zahrnout soubor
description: zahrnout soubor
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/07/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: fb95301d4faa958a677c42bc8092ac52a7c95c8b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80813711"
---
| Prostředek | Omezení |
| --- | --- |
| Datová propustnost |30 Gb/s<sup>1</sup> |
|Pravidla|10,000. Všechny typy pravidel v kombinaci.|
|Maximální pravidla DNAT|298<br>Pokud je protokol pravidla nakonfigurován pro protokol TCP i UDP, počítá se jako dvě pravidla.|
|Minimální velikost azurefirewallsubnetu |/26|
|Rozsah portů v pravidlech sítě a aplikací|1 - 65535|
|Veřejné IP adresy|Maximálně 100 (V současné době jsou porty SNAT přidány pouze pro prvních pět veřejných IP adres.)|
|IP adresy skupin IP|50 skupin IP nebo méně: maximálně 5000 jednotlivých IP adres na každou instanci brány firewall.<br>51 - 100 SKUPIN IP: 500 jednotlivých IP adres na každou instanci brány firewall.<br><br>Další informace najdete [v tématu SKUPINY IP (preview) v Azure Firewall](../articles/firewall/ip-groups.md#ip-address-limits)
|Tabulka směrování|Ve výchozím nastavení má azurefirewallsubnet trasu 0.0.0.0/0 s hodnotou NextHopType nastavenou na **Internet**.<br><br>Azure Firewall musí mít přímé připojení k Internetu. Pokud se vaše síť AzureFirewallSubnet učí výchozí trasu do místní sítě prostřednictvím protokolu BGP, musíte ji přepsat udr 0.0.0.0/0 s hodnotou **NextHopType** nastavenou jako **Internet,** aby bylo zachováno přímé připojení k Internetu. Ve výchozím nastavení azure firewall nepodporuje vynucené tunelové propojení do místní sítě.<br><br>Pokud však vaše konfigurace vyžaduje vynucené tunelové propojení do místní sítě, bude ji společnost Microsoft podporovat případ od případu. Kontaktujte podporu, abychom mohli váš případ zkontrolovat. Pokud bude přijato, povolíme vaše předplatné a zajistíme, aby bylo zachováno požadované připojení k internetu brány firewall.|

<sup>1.</sup> Pokud potřebujete zvýšit tato omezení, obraťte se na podporu Azure.
