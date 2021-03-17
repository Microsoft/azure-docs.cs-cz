---
title: Výběr cesty Azure u více odkazů poskytovatele internetových služeb
titleSuffix: Azure Virtual WAN
description: Další informace o výběru cest Azure a virtuální síti WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: f24696c0db3155a59106e1361b01454b9ac16a20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267750"
---
# <a name="azure-path-selection-across-multiple-isp-links"></a>Výběr cesty Azure u více odkazů poskytovatele internetových služeb

Azure Virtual WAN poskytuje uživateli možnost zahrnout informace o propojení na lokalitu VPN a povolit scénáře, ve kterých řešení zařízení VPN/SD-WAN může programovat zásady pro řízení provozu napříč různými odkazy na Azure. Tato možnost se nazývá **výběr cesty Azure**.

## <a name="architecture"></a>Architektura

Abychom porozuměli tomu, jak funguje výběr cest Azure, použijte příklad sítě VPN typu virtuální sítě WAN a připojení typu Site-to-site.

Síť VPN představuje místní zařízení SD-WAN/VPN s informacemi, jako je veřejná IP adresa, model zařízení a název atd. Aktuální místní lokalita sítě VPN může obsahovat více odkazů poskytovatele internetových služeb, které mohou být také obsaženy v informacích o lokalitě VPN sítě WAN. To vám umožní zobrazit informace o propojení v Azure.

Připojení Site-to-site IPsec, které přichází do sítě VPN virtuální sítě WAN, končí na instancích služby VPN Gateway uvnitř virtuálního rozbočovače. Připojení typu Site-to-site představuje konektivitu mezi lokalitou VPN a bránou Azure VPN Gateway. Skládá se z jednoho nebo více připojení propojení. Každé připojení propojení se skládá ze dvou tunelů, které končí na jedinečné instanci Azure Virtual WAN VPN Gateway. V rámci připojení typu Site-to-site je možné nastavit až čtyři připojení propojení. to umožňuje mít až osm tunelů v rámci připojení typu Site-to-site. Azure podporuje až 2000 tunelů, které končí v rámci jedné virtuální sítě WAN VPN Gateway.

:::image type="content" source="./media/path-selection-multiple-links/multi-link-site.png" alt-text="Diagram s více odkazy":::

Na tomto obrázku je znázorněno více odkazů na pracovišti, které se připojuje k Azure Virtual WAN. V tomto diagramu:

* Existují dva odkazy poskytovatele internetových služeb na místní větvi (zařízení VPN/SD-WAN). Každý odkaz poskytovatele internetových služeb odpovídá připojení propojení.

* Předpokládalo se, že místní zařízení VPN/SD-WAN místního zákazníka – manažer podporuje protokol IPsec IKEv1 nebo IKEv2.

* Každé připojení typu Site-to-site k virtuální síti WAN ze sítě Azure se skládá z připojení přímo v rámci sebe. Připojení podporuje až čtyři propojení připojení. Azure účtuje poplatek za jednotku připojení pro připojení k virtuální síti WAN. Za připojení odkazů se neúčtují žádné poplatky.

* Každé připojení propojení se pak skládá ze dvou tunelů IPsec, které mohou končit dvěma různými instancemi virtuální sítě WAN VPN Gateway. Brány se nastavují jako brány aktivní-aktivní a mají odolnost proti chybám. Každé připojení propojení musí mít jedinečnou IP adresu a IP adresu partnerského vztahu protokolu BGP. V diagramu může tunel 0 skončit na instanci 0 a tunel 1 může skončit na instanci 1.

* Zařízení větví, která poskytují výběr cest, můžou v řešení správy větví povolit příslušné zásady pro řízení provozu napříč několika odkazy na Azure. Například odkaz poskytovatele internetových služeb je možné použít pro přenosy s vyšší prioritou a odkaz na poskytovatele internetových služeb (ISP 2) lze použít jako zálohu.

* Je důležité si uvědomit, že síť VPN s virtuálním ROZBOČOVAČem používá v rámci všech ukončovacích tunelech ECMP (EQUAL cost multi-Path Routing).

## <a name="next-steps"></a>Další kroky

Podívejte se na [Nejčastější dotazy k Azure](virtual-wan-faq.md).