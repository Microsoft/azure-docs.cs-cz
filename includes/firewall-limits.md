---
title: zahrnout soubor
description: zahrnout soubor
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 07/30/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 59699d493880034ad1d26a56c63a9ed8401ef371
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87507641"
---
| Prostředek | Omezení |
| --- | --- |
| Datová propustnost |30 GB/s<sup>1</sup> |
|Pravidla|10 000. Všechny typy pravidel jsou kombinované.|
|Maximální počet pravidel DNAT|298 pro jednu veřejnou IP adresu.<br>Všechny další veřejné IP adresy přispívají k dostupným portům SNAT, ale omezují počet dostupných pravidel DNAT. Například dvě veřejné IP adresy umožňují pravidla 297 DNAT. Pokud je protokol pravidla nakonfigurovaný pro TCP i UDP, počítá se jako dvě pravidla.|
|Minimální velikost AzureFirewallSubnet |za 26|
|Rozsah portů v pravidlech sítě a aplikace|1 - 65535|
|Veřejné IP adresy|250 maximum. Všechny veřejné IP adresy se dají použít v pravidlech DNAT a všechny přispívají k dostupným portům SNAT.|
|IP adresy ve skupinách IP adres|Maximálně 100 skupin IP adres na bránu firewall.<br>Maximální počet 5000 pro jednotlivé IP adresy nebo předpony IP adres na každou skupinu IP adres.
|Tabulka směrování|Ve výchozím nastavení má AzureFirewallSubnet trasu 0.0.0.0/0 s hodnotou typem nastavenou na **Internet**.<br><br>Služba Azure Firewall musí mít přímé připojení k internetu. Pokud vaše AzureFirewallSubnet zjišťuje výchozí trasu k místní síti přes protokol BGP, musíte přepsat to s hodnotou 0.0.0.0/0 UDR s hodnotou **typem** nastavenou jako **Internet** pro zachování přímého připojení k Internetu. Ve výchozím nastavení Azure Firewall nepodporuje vynucené tunelování v místní síti.<br><br>Pokud však vaše konfigurace vyžaduje vynucené tunelování v místní síti, společnost Microsoft je bude podporovat na základě případu. Obraťte se na podporu, abychom mohli zkontrolovat váš případ. V případě přijetí umožníme vašemu předplatnému a zajistíte, aby bylo zachováno požadované připojení k Internetu z brány firewall.|

<sup>1</sup> Pokud potřebujete tato omezení zvýšit, obraťte se na podporu Azure.
