---
title: zahrnout soubor
description: zahrnout soubor
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 10/23/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4a3581b3d61c5ad17352c2865fc059704c1eece1
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "50035230"
---
### <a name="what-is-expressroute-global-reach"></a>Co je globální dosah ExpressRoute?

Globální dosah ExpressRoute je služba Azure, která se připojuje vaše místní sítě prostřednictvím služby ExpressRoute prostřednictvím globální sítě od Microsoftu. Například pokud máte privátní datového centra v Kalifornii připojená k ExpressRoute v Silicon Valley a jiného privátního datového centra v Texas připojená k ExpressRoute v Dallasu, s ExpressRoute globální dosah, můžete připojit privátních datových centrech dohromady přes dvě připojení ExpressRoute a vaší napříč center přenos dat prochází přes páteřní síť Microsoftu.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Jak povolit nebo zakázat globální dosah ExpressRoute?

Můžete povolit globální dosah ExpressRoute propojováním okruhů ExpressRoute. Když se odpojíte od obvody zakážete funkci. Zobrazit [konfigurace](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Potřebuji pro ExpressRoute globální dosah ExpressRoute Premium?

Pokud vaše okruhy ExpressRoute jsou ve stejné geopolitické oblasti, není nutné ExpressRoute Premium je propojit. Pokud dva okruhy ExpressRoute jsou v různých geopolitických oblastí, musíte pro oba okruhy ExpressRoute Premium chcete-li povolit připojení mezi nimi. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Jak se mi účtovat globální dosah ExpressRoute?

ExpressRoute umožňuje připojení z vaší místní sítě ke cloudovým službám Microsoftu. Globální dosah ExpressRoute umožňuje propojení mezi vlastní místní sítě prostřednictvím stávajících okruhů ExpressRoute, využívá globální síti Microsoftu. Globální dosah ExpressRoute se fakturovat nezávisle na existující službu ExpressRoute. Existuje nějaký poplatek doplněk pro povolení této funkce na každý okruh ExpressRoute. Provoz mezi vaší místní sítě, zajišťuje globální dosah ExpressRoute se vám účtovat objemem odchozího přenosu dat ve zdroji a rychlost příchozího přenosu dat v cílovém umístění. Sazby jsou založeny na zóně, ve kterém obvody nacházejí. V tématu <pricing page>

### <a name="where-is-expressroute-global-reach-supported"></a>Kde se globální dosah ExpressRoute podporuje?

Globální dosah ExpressRoute se podporuje v těchto zemích. Okruhy ExpressRoute musíte vytvořit v umístění partnerského vztahu v těchto zemích.

* Austrálie
* Francie
* Hongkong
* Irsko
* Japonsko
* Nizozemsko
* Spojené království
* Spojené státy

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Mám více než dvěma místními sítěmi, každý připojený k okruhu ExpressRoute. Můžete povolit globální dosah ExpressRoute je možné propojit všechny moje sítích na pracovišti?

Ano, je to možné, dokud obvody nacházejí v podporované země. Je potřeba současně připojit dva okruhy ExpressRoute. Vytvoření plně hustá sítě, budete muset vytvořit výčet všech dvojice okruhů a konfiguraci opakujte. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Můžete povolit globální dosah ExpressRoute mezi dva okruhy ExpressRoute ve stejném umístění partnerského vztahu?

Ne. Dva okruhy musí pocházet z různých umístění partnerského vztahu. Pokud metro v podporované země má více než jeden umístění partnerského vztahu ExpressRoute, můžete se připojit společně okruhy ExpressRoute vytvořené v různých umístění partnerského vztahu v tomto metro. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Pokud globální dosah ExpressRoute je povolené mezi okruh X a Y okruh a mezi okruh Y a Z okruhu, bude Moje sítích na pracovišti připojené k okruhu X a okruh Z komunikovat přes síť Microsoftu?

Ne. Pokud chcete povolit připojení mezi dvěma místními sítěmi, je explicitně připojit odpovídající okruhy ExpressRoute. V příkladu výše je nutné připojit okruh X a okruh Z. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Co je propustnost sítě, očekávané mezi Moje sítích na pracovišti po povolit globální dosah ExpressRoute?

Propustnost sítě mezi vaší místní sítě, zajišťuje ExpressRoute globální dosah je uzavřeny menší ze dvou okruhů ExpressRoute.

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>S ExpressRoute globální dosah jaká jsou omezení na počet tras, které můžu můžete inzerovat a počtu tras, které budu dostávat?

Počet tras, které můžete inzerovat Microsoftu na soukromý partnerský vztah Azure zůstává na 4000 na standardní okruh nebo 10000 v okruhu Premium. Počet tras, které jste dostali z Microsoftu na soukromý partnerský vztah Azure bude součtem trasy virtuálním sítím Azure a trasy z vašich místních sítí připojenou přes ExpressRoute globální dosah. Ujistěte se prosím, že můžete nastavit limit odpovídající maximální předpony místní směrovač. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Co je smlouva SLA pro globální dosah ExpressRoute?

Globální dosah ExpressRoute bude poskytovat stejné [smlouva SLA o dostupnosti](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) jako regulární služby ExpressRoute.
