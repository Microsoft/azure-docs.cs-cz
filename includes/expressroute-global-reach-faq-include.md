---
title: zahrnout soubor
description: zahrnout soubor
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "67174743"
---
### <a name="what-is-expressroute-global-reach"></a>Co je ExpressRoute Global Reach?

ExpressRoute Global Reach je služba Azure, která připojuje vaše místní sítě prostřednictvím služby ExpressRoute prostřednictvím globální sítě Microsoftu. Pokud máte například soukromé datové centrum v Kalifornii připojené k ExpressRoute v karbidu sedlu a jiném privátním datovém centru v Texas připojení k ExpressRoute v Dallasu, s ExpressRoute Global Reach, můžete propojit svá privátní datová centra společně prostřednictvím dvou připojení ExpressRoute a provoz napříč datovým centrem prostřednictvím sítě Microsoftu.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Návody povolit nebo zakázat Global Reach ExpressRoute?

Povolíte ExpressRoute Global Reach připojením okruhů ExpressRoute. Vypnutím této funkce odpojíte okruhy. Podívejte se na [konfiguraci](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Potřebuji ExpressRoute Premium pro ExpressRoute Global Reach?

Pokud jsou vaše okruhy ExpressRoute ve stejné geopolitické oblasti, nepotřebujete ExpressRoute Premium, aby je vzájemně připojovaly. Pokud jsou dva okruhy ExpressRoute v různých geopolitických oblastech, potřebujete ExpressRoute Premium pro oba okruhy, aby bylo možné mezi nimi povolit konektivitu. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Jak se mi bude účtovat ExpressRoute Global Reach?

ExpressRoute umožňuje připojení z vaší místní sítě k cloudovým službám Microsoftu. ExpressRoute Global Reach umožňuje propojení mezi vašimi místními sítěmi prostřednictvím stávajících okruhů ExpressRoute a využitím globální sítě Microsoftu. ExpressRoute Global Reach se fakturuje odděleně od stávající služby ExpressRoute. Pro povolení této funkce na každém okruhu ExpressRoute je k dispozici poplatek za doplněk. Přenosy mezi vašimi místními sítěmi, které jsou povolené službou ExpressRoute Global Reach, se budou účtovat jako výstupní míra ve zdroji a za příchozí sazbu v cíli. Sazby jsou založené na zóně, ve které se okruhy nacházejí.

### <a name="where-is-expressroute-global-reach-supported"></a>Kde je ExpressRoute Global Reach podporována?

ExpressRoute Global Reach se podporují ve [vybraných zemích nebo oblastech nebo místech](../articles/expressroute/expressroute-global-reach.md). Okruhy ExpressRoute je potřeba vytvořit v umístěních partnerských vztahů v těchto zemích nebo oblastech nebo místech.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Mám více než dvě místní sítě, které jsou připojené k okruhu ExpressRoute. Můžu povolit Global Reach ExpressRoute k propojení všech místních sítí?

Ano, můžete, pokud jsou okruhy v podporovaných zemích nebo oblastech. Je potřeba propojit dva okruhy ExpressRoute současně. Pokud chcete vytvořit plně propojenou síť, musíte vytvořit výčet všech párů okruhů a konfiguraci opakovat. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Můžu povolit ExpressRoute Global Reach mezi dvěma ExpressRoute okruhy ve stejném umístění partnerského vztahu?

No. Dva okruhy musí být z různých umístění partnerského vztahu. Pokud má Metro v podporované zemi nebo oblasti více než jedno umístění partnerského vztahu ExpressRoute, můžete se spojit mezi ExpressRoute okruhy vytvořenými v různých umístěních partnerských vztahů v této službě Metro. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Pokud je ExpressRoute Global Reach mezi okruhem X a okruh Y a mezi okruhem Y a okruh Z, budou místní sítě připojené k okruhu X a okruhu Z vzájemně propojeny přes síť Microsoftu?

No. Pokud chcete povolit připojení mezi dvěma vašimi místními sítěmi, musíte explicitně propojit odpovídající okruhy ExpressRoute. V předchozím příkladu je třeba spojit okruh X a okruh Z. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Jakou propustnost sítě můžu očekávat mezi místními sítěmi po povolení ExpressRoute Global Reach?

Propustnost sítě mezi vašimi místními sítěmi, která je povolená ExpressRoute Global Reach, je omezené menším ze dvou okruhů ExpressRoute. Provoz z místního prostředí do Azure a místní provoz sdílí stejný okruh a podléhá stejnému limitu šířky pásma. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>S Global Reach ExpressRoute, jaké jsou limity počtu tras, které můžu inzerovat, a počet tras, které budu dostávat?

Počet tras, které můžete inzerovat Microsoftu na privátním partnerském vztahu Azure, na okruhu Standard a na úrovni Premium zůstane na 4000 nebo 10000. Počet tras, které obdržíte od Microsoftu v rámci privátního partnerského vztahu Azure, bude celkový počet tras vašich virtuálních sítí Azure a tras z vašich dalších místních sítí, které jsou připojené přes ExpressRoute Global Reach. Ujistěte se prosím, že jste na místním směrovači nastavili odpovídající maximální limit předpony. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Jaká je smlouva SLA pro ExpressRoute Global Reach?

ExpressRoute Global Reach poskytne smlouvou SLA o stejné [dostupnosti](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) jako standardní službu ExpressRoute.
