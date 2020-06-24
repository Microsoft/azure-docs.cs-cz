---
title: zahrnout soubor
description: zahrnout soubor
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 06/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: fe223030b98f95036f01cf69babdeb8a9a84dc2d
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242093"
---
| Prostředek | Omezení |
| --- | --- |
| Propustnost dat |30 GB/s<sup>1</sup> |
|Pravidla|10 000. Všechny typy pravidel jsou kombinované.|
|Maximální počet pravidel DNAT|298 pro jednu veřejnou IP adresu.<br>Všechny další veřejné IP adresy přispívají k dostupným portům SNAT, ale omezují počet dostupných pravidel DNAT. Například dvě veřejné IP adresy umožňují pravidla 297 DNAT. Pokud je protokol pravidla nakonfigurovaný pro TCP i UDP, počítá se jako dvě pravidla.|
|Minimální velikost AzureFirewallSubnet |za 26|
|Rozsah portů v pravidlech sítě a aplikace|1 - 65535|
|Veřejné IP adresy|250 maximum. Všechny veřejné IP adresy se dají použít v pravidlech DNAT a všechny přispívají k dostupným portům SNAT.|
|IP adresy ve skupinách IP adres|50 skupin IP adres nebo méně: maximální počet IP adres pro každou instanci brány firewall je 5000.<br>51-100 IP adres: 500 jednotlivé IP adresy na každou instanci brány firewall.<br><br>Další informace najdete [v části skupiny IP adres (Preview) v tématu Azure firewall](../articles/firewall/ip-groups.md#ip-address-limits)
|Tabulka směrování|Ve výchozím nastavení má AzureFirewallSubnet trasu 0.0.0.0/0 s hodnotou typem nastavenou na **Internet**.<br><br>Azure Firewall musí mít přímé připojení k Internetu. Pokud vaše AzureFirewallSubnet zjišťuje výchozí trasu k místní síti přes protokol BGP, musíte přepsat to s hodnotou 0.0.0.0/0 UDR s hodnotou **typem** nastavenou jako **Internet** pro zachování přímého připojení k Internetu. Ve výchozím nastavení Azure Firewall nepodporuje vynucené tunelování v místní síti.<br><br>Pokud však vaše konfigurace vyžaduje vynucené tunelování v místní síti, společnost Microsoft je bude podporovat na základě případu. Obraťte se na podporu, abychom mohli zkontrolovat váš případ. V případě přijetí umožníme vašemu předplatnému a zajistíte, aby bylo zachováno požadované připojení k Internetu z brány firewall.|

<sup>1</sup> Pokud potřebujete tato omezení zvýšit, obraťte se na podporu Azure.
