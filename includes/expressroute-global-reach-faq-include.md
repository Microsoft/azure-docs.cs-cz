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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174743"
---
### <a name="what-is-expressroute-global-reach"></a>Co je globální dosah ExpressRoute?

ExpressRoute Global Reach je služba Azure, která propojuje vaše místní sítě prostřednictvím služby ExpressRoute prostřednictvím globální sítě Microsoftu. Pokud máte například soukromé datové centrum v Kalifornii připojené k ExpressRoute v Silicon Valley a další soukromé datové centrum v Texasu připojené k ExpressRoute v Dallasu, můžete pomocí expressroute Global Reach propojit svá soukromá datová centra prostřednictvím dvou připojení ExpressRoute a provoz mezi datovými centry bude procházet páteřní sítí společnosti Microsoft.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Jak povolím nebo zakážu Globální dosah ExpressRoute?

Globální dosah ExpressRoute povolíte spojením okruhů ExpressRoute dohromady. Funkci zakážete odpojením obvodů. Podívejte se na [konfiguraci](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Potřebuji ExpressRoute Premium pro Globální dosah ExpressRoute?

Pokud jsou vaše okruhy ExpressRoute ve stejné geopolitické oblasti, nepotřebujete ExpressRoute Premium, abyste je spojili. Pokud jsou dva okruhy ExpressRoute v různých geopolitických oblastech, potřebujete ExpressRoute Premium pro oba okruhy, abyste mezi nimi povolili připojení. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Jak se mi bude účtovat globální dosah ExpressRoute?

ExpressRoute umožňuje připojení z místní sítě ke cloudovým službám Microsoftu. ExpressRoute Global Reach umožňuje připojení mezi vašimi vlastními místními sítěmi prostřednictvím stávajících obvodů ExpressRoute a využívá globální síť společnosti Microsoft. Globální dosah ExpressRoute se účtuje odděleně od stávající služby ExpressRoute. Za povolení této funkce v každém okruhu ExpressRoute se účtuje poplatek za přidání. Provoz mezi místními sítěmi povolený misedy ExpressRoute Global Reach se bude účtovat za rychlost odchozího přenosu ve zdroji a za rychlost příchozího přenosu dat v cílovém umístění. Ceny jsou založeny na zóně, ve které jsou obvody umístěny.

### <a name="where-is-expressroute-global-reach-supported"></a>Kde je podporován globální dosah ExpressRoute?

Globální dosah ExpressRoute je podporován ve [vybraných zemích nebo oblastech nebo místech](../articles/expressroute/expressroute-global-reach.md). Okruhy ExpressRoute musí být vytvořeny v partnerských umístěních v těchto zemích nebo oblastech nebo místech.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Mám více než dvě místní sítě, z nichž každá je připojena k okruhu ExpressRoute. Můžu povolit službu ExpressRoute Global Reach pro propojení všech místních sítí?

Ano, můžete, pokud jsou okruhy v podporovaných zemích nebo oblastech. Musíte připojit dva okruhy ExpressRoute najednou. Chcete-li vytvořit plně síťovou síť, musíte vytvořit výčet všech dvojic okruhů a zopakovat konfiguraci. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Mohu povolit ExpressRoute Global Reach mezi dvěma okruhy ExpressRoute ve stejném umístění partnerského vztahu?

Ne. Dva okruhy musí být z různých partnerských umístění. Pokud má metro v podporované zemi nebo oblasti více než jedno umístění partnerského vztahu ExpressRoute, můžete propojit okruhy ExpressRoute vytvořené v různých partnerských umístěních v tomto metru. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Pokud je expressroute Global Reach povolen mezi okruhem X a obvodem Y a mezi okruhem Y a obvodem Z, budou moje místní sítě připojené k okruhu X a okruhu Z vzájemně mluvit prostřednictvím sítě společnosti Microsoft?

Ne. Chcete-li povolit připojení mezi dvěma místními sítěmi, musíte explicitně připojit odpovídající okruhy ExpressRoute. Ve výše uvedeném příkladu je nutné připojit obvod X a obvod Z. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Jaká je propustnost sítě, kterou mohu očekávat mezi místními sítěmi po povolení globálního dosahu ExpressRoute?

Propustnost sítě mezi místními sítěmi, povolená technologií ExpressRoute Global Reach, je omezena menší ze dvou okruhů ExpressRoute. Provoz mezi prostory Azure a provoz mezi místními objekty sdílejí stejný okruh a podléhají stejnému limitu šířky pásma. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Jaké jsou limity počtu tras, které mohu inzerovat, a počtu tras, které obdržím, v případě, že je společnost ExpressRoute Global Reach?

Počet tras, které můžete inzerovat na Microsoft v Privátní partnerský vztah Azure zůstává na 4000 na okruhu Standard nebo 10000 na okruhu Premium. Počet tras, které obdržíte od Microsoftu v privátním partnerství Azure, bude součtem tras vašich virtuálních sítí Azure a tras z vašich dalších místních sítí připojených přes ExpressRoute Global Reach. Ujistěte se, že jste na místním směrovači nastavili odpovídající maximální limit předpony. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Co je sla pro Globální dosah ExpressRoute?

ExpressRoute Global Reach bude poskytovat stejnou [dostupnost SLA](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) jako běžná služba ExpressRoute.
