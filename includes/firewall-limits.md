---
title: zahrnout soubor
description: zahrnout soubor
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 3/25/2019
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 368a7cf1d372d79f062affe9b206d070afe5c2f7
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505765"
---
| Prostředek | Výchozí omezení |
| --- | --- |
| Propustnost dat |30 Gbps<sup>1</sup> |
|Pravidla|10 000, všechna pravidla kombinovat typy.|
|Minimální velikost AzureFirewallSubnet |/26|
|Rozsah portů v pravidlech sítě a aplikace|0-64,000. Práce se toto omezení zmírnit.|
|Tabulka směrování|Ve výchozím nastavení, má AzureFirewallSubnet trasy 0.0.0.0/0 nastavená na hodnotu NextHopType **Internet**.<br><br>Pokud povolíte vynuceného tunelování k místnímu přes ExpressRoute nebo VPN Gateway, budete muset explicitně nakonfigurovat 0.0.0.0/0 trasy definované uživatelem (UDR) se sadou NextHopType hodnotu jako Internet a přidružte jej k vaší AzureFirewallSubnet. Tím se přepíše potenciální výchozí bránu inzerování protokolu BGP zpět k místní síti. Pokud vaše organizace vyžaduje, aby vynucené tunelování za Firewall služby Azure pro směrování výchozí brány zpět prostřednictvím místní sítě, obraťte se na podporu. Můžeme vytvořit bílou listinu udržuje vaše předplatné, abyste zajistili vyžaduje připojení k Internetu bránu firewall.|

<sup>1</sup>Pokud potřebujete tyto limity zvýšit, obraťte se na podporu Azure.
