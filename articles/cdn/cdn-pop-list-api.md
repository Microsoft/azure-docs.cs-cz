---
title: Načtení aktuálního seznamu IP adres protokolu POP pro Azure CDN| Dokumenty společnosti Microsoft
description: Přečtěte si, jak načíst aktuální seznam POP.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 95b85aa11d99ddd48c90c8d9fa28789e79ee979f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299247"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Načtení aktuálního seznamu IP adres protokolu POP pro Azure CDN

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Načtení aktuálního seznamu IP adres Verizon PRO Azure CDN

Rozhraní REST API můžete použít k načtení sady IP adresy pro servery Verizon point of presence (POP). Tyto servery POP pořazují požadavky na původní servery, které jsou přidruženy ke koncovým bodům sítě Azure Content Delivery Network (CDN) na profilu Verizon **(Azure CDN Standard od Verizonu** nebo **Azure CDN Premium od společnosti Verizon).** Všimněte si, že tato sada IP adresy se liší od IP adresy, které by klient vidět při vytváření požadavků na POP. 

Syntaxe operace rozhraní REST API pro načtení seznamu POP naleznete v [tématu Hraniční uzly – seznam](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Načtení aktuálního seznamu IP adres Microsoft POP pro Azure CDN

Chcete-li uzamknout aplikaci přijímat provoz pouze z Azure CDN od společnosti Microsoft, budete muset nastavit IP ACLs pro back-end. Můžete také omezit sadu přijatých hodnot pro hlavičku "X-Forwarded-Host" odeslanou azure CDN od microsoftu. Tyto kroky jsou podrobně popsány takto:

Nakonfigurujte ip acling pro vaše back-endy tak, aby přijímali provoz z Azure CDN z back-endového IP adresového prostoru Microsoftu a pouze z infrastrukturních služeb Azure. 

* Azure CDN z ip prostoru Back-endu IPv4 od Microsoftu: 147.243.0.0/16
* Azure CDN z ip prostoru Back-endu IPv6 od Microsoftu: 2a01:111:2050::/44

Rozsahy IP adres a značky služeb pro služby společnosti Microsoft naleznete [zde](https://www.microsoft.com/download/details.aspx?id=56519)


## <a name="typical-use-case"></a>Typický případ použití

Z bezpečnostních důvodů můžete tento seznam IP vynucovat, že požadavky na váš zdrojový server jsou vyrobeny pouze z platného protokolu POP Verizon. Například pokud někdo objevil název hostitele nebo IP adresu pro zdrojový server koncového bodu CDN, jeden může provádět požadavky přímo na zdrojový server, tedy obcházet škálování a možnosti zabezpečení poskytované Azure CDN. Nastavením IP adresy ve vráceném seznamu jako jediné povolené IP adresy na původu serveru, tento scénář lze zabránit. Chcete-li zajistit, že máte nejnovější seznam POP, načtěte jej alespoň jednou denně. 

## <a name="next-steps"></a>Další kroky

Informace o rozhraní REST API najdete v [tématu Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/).
