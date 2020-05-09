---
title: zahrnout soubor
description: zahrnout soubor
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 05/06/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 5a5f831e72da6bedaf12d3ed82e79f0250a09062
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82876009"
---
| Prostředek | Omezení |
| --- | --- |
| Datová propustnost |30 GB/s<sup>1</sup> |
|Pravidla|10 000. Všechny typy pravidel jsou kombinované.|
|Maximální počet pravidel DNAT|298<br>Pokud je protokol pravidla nakonfigurovaný pro TCP i UDP, počítá se jako dvě pravidla.|
|Minimální velikost AzureFirewallSubnet |za 26|
|Rozsah portů v pravidlech sítě a aplikace|1 - 65535|
|Veřejné IP adresy|250 pro port DNAT i SNAT.|
|IP adresy skupin IP adres|50 skupin IP adres nebo méně: maximální počet IP adres pro každou instanci brány firewall je 5000.<br>51-100 IP adres: 500 jednotlivé IP adresy na každou instanci brány firewall.<br><br>Další informace najdete [v části skupiny IP adres (Preview) v tématu Azure firewall](../articles/firewall/ip-groups.md#ip-address-limits)
|Tabulka směrování|Ve výchozím nastavení má AzureFirewallSubnet trasu 0.0.0.0/0 s hodnotou typem nastavenou na **Internet**.<br><br>Azure Firewall musí mít přímé připojení k Internetu. Pokud vaše AzureFirewallSubnet zjišťuje výchozí trasu k místní síti přes protokol BGP, musíte přepsat to s hodnotou 0.0.0.0/0 UDR s hodnotou **typem** nastavenou jako **Internet** pro zachování přímého připojení k Internetu. Ve výchozím nastavení Azure Firewall nepodporuje vynucené tunelování v místní síti.<br><br>Pokud však vaše konfigurace vyžaduje vynucené tunelování v místní síti, společnost Microsoft je bude podporovat na základě případu. Obraťte se na podporu, abychom mohli zkontrolovat váš případ. V případě přijetí umožníme vašemu předplatnému a zajistíte, aby bylo zachováno požadované připojení k Internetu z brány firewall.|

<sup>1</sup> Pokud potřebujete tato omezení zvýšit, obraťte se na podporu Azure.
