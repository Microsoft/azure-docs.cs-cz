---
title: zahrnout soubor
description: zahrnout soubor
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/03/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 4972a80ec7c481ac7bc8860c005c8576ce37a090
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758562"
---
| Prostředek | Omezení |
| --- | --- |
| Datová propustnost |30 Gb/s<sup>1</sup> |
|Pravidla|10,000. Všechny typy pravidel v kombinaci.|
|Maximální pravidla DNAT|298<br>Pokud je protokol pravidla nakonfigurován pro protokol TCP i UDP, počítá se jako dvě pravidla.|
|Minimální velikost azurefirewallsubnetu |/26|
|Rozsah portů v pravidlech sítě a aplikací|0-64,000. Probíhá práce na uvolnění tohoto omezení.|
|Veřejné IP adresy|Maximálně 100 (V současné době jsou porty SNAT přidány pouze pro prvních pět veřejných IP adres.)|
|IP adresy skupin IP|50 skupin IP nebo méně: maximálně 5000 jednotlivých IP adres na každou instanci brány firewall.<br>51 - 100 SKUPIN IP: 500 jednotlivých IP adres na každou instanci brány firewall.<br><br>Další informace najdete [v tématu SKUPINY IP (preview) v Azure Firewall](../articles/firewall/ip-groups.md#ip-address-limits)
|Tabulka směrování|Ve výchozím nastavení má azurefirewallsubnet trasu 0.0.0.0/0 s hodnotou NextHopType nastavenou na **Internet**.<br><br>Azure Firewall musí mít přímé připojení k Internetu. Pokud se vaše síť AzureFirewallSubnet učí výchozí trasu do místní sítě prostřednictvím protokolu BGP, musíte ji přepsat udr 0.0.0.0/0 s hodnotou **NextHopType** nastavenou jako **Internet,** aby bylo zachováno přímé připojení k Internetu. Ve výchozím nastavení azure firewall nepodporuje vynucené tunelové propojení do místní sítě.<br><br>Pokud však vaše konfigurace vyžaduje vynucené tunelové propojení do místní sítě, bude ji společnost Microsoft podporovat případ od případu. Kontaktujte podporu, abychom mohli váš případ zkontrolovat. Pokud bude přijato, povolíme vaše předplatné a zajistíme, aby bylo zachováno požadované připojení k internetu brány firewall.|

<sup>1.</sup> Pokud potřebujete zvýšit tato omezení, obraťte se na podporu Azure.
