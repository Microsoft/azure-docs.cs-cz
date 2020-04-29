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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80813711"
---
| Prostředek | Omezení |
| --- | --- |
| Datová propustnost |30 GB/s<sup>1</sup> |
|Pravidla|10 000. Všechny typy pravidel jsou kombinované.|
|Maximální počet pravidel DNAT|298<br>Pokud je protokol pravidla nakonfigurovaný pro TCP i UDP, počítá se jako dvě pravidla.|
|Minimální velikost AzureFirewallSubnet |za 26|
|Rozsah portů v pravidlech sítě a aplikace|1 - 65535|
|Veřejné IP adresy|100 maximum (v současnosti jsou porty SNAT přidány pouze pro prvních pět veřejných IP adres.)|
|IP adresy skupin IP adres|50 skupin IP adres nebo méně: maximální počet IP adres pro každou instanci brány firewall je 5000.<br>51-100 IP adres: 500 jednotlivé IP adresy na každou instanci brány firewall.<br><br>Další informace najdete [v části skupiny IP adres (Preview) v tématu Azure firewall](../articles/firewall/ip-groups.md#ip-address-limits)
|Tabulka směrování|Ve výchozím nastavení má AzureFirewallSubnet trasu 0.0.0.0/0 s hodnotou typem nastavenou na **Internet**.<br><br>Azure Firewall musí mít přímé připojení k Internetu. Pokud vaše AzureFirewallSubnet zjišťuje výchozí trasu k místní síti přes protokol BGP, musíte přepsat to s hodnotou 0.0.0.0/0 UDR s hodnotou **typem** nastavenou jako **Internet** pro zachování přímého připojení k Internetu. Ve výchozím nastavení Azure Firewall nepodporuje vynucené tunelování v místní síti.<br><br>Pokud však vaše konfigurace vyžaduje vynucené tunelování v místní síti, společnost Microsoft je bude podporovat na základě případu. Obraťte se na podporu, abychom mohli zkontrolovat váš případ. V případě přijetí umožníme vašemu předplatnému a zajistíte, aby bylo zachováno požadované připojení k Internetu z brány firewall.|

<sup>1</sup> Pokud potřebujete tato omezení zvýšit, obraťte se na podporu Azure.
