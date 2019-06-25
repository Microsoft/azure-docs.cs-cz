---
title: zahrnout soubor
description: zahrnout soubor
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 5/3/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 8709d4d903bd31ff94d04ec61e226857e4190407
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175382"
---
| Resource | Výchozí omezení |
| --- | --- |
| Propustnost dat |30 Gbps<sup>1</sup> |
|Pravidla|10 000, všechna pravidla kombinovat typy.|
|Minimální velikost AzureFirewallSubnet |/26|
|Rozsah portů v pravidlech sítě a aplikace|0-64,000. Práce se toto omezení zmírnit.|
|Směrovací tabulky|Ve výchozím nastavení, má AzureFirewallSubnet trasy 0.0.0.0/0 nastavená na hodnotu NextHopType **Internet**.<br><br>Azure brány Firewall musí mít přímé připojení k Internetu. Pokud vaše AzureFirewallSubnet učí výchozí trasu k vaší místní síti přes protokol BGP, je nutné to přepsat s UDR 0.0.0.0/0 s **NextHopType** hodnota nastavená na **Internet** udržovat s přímým přístupem Připojení k Internetu. Ve výchozím nastavení brána Firewall služby Azure nepodporuje vynuceného tunelování k místní síti.<br><br>Však vyžaduje-li vaše konfigurace vynuceného tunelování k místní síti, Microsoft bude podporovat v případ od případu. Takže si můžete přečíst váš případ, obraťte se na podporu. Pokud přijat, vytvoříme seznamu povolených IP adres vaše předplatné a ujistěte se, že se zachová připojení k Internetu vyžaduje bránu firewall.|

<sup>1</sup>Pokud potřebujete tyto limity zvýšit, obraťte se na podporu Azure.
