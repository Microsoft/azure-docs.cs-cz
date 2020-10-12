---
title: Načíst aktuální seznam IP adres POP pro Azure CDN | Microsoft Docs
description: Přečtěte si, jak získat servery POP pomocí REST API. Servery POP vytvářejí požadavky na zdrojové servery přidružené k koncovým bodům Azure Content Delivery Network.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 13d4587a80cbbe66e18eb1bec8f3154221ac62b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88192559"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Načte aktuální seznam IP adres POP pro Azure CDN.

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Načte aktuální seznam IP adres POP Verizon pro Azure CDN.

Pomocí REST API můžete načíst sadu IP adres pro servery POP (Point of Presence) Verizon. Tyto servery POP vytvářejí požadavky na zdrojové servery, které jsou přidružené k koncovým bodům služby Azure Content Delivery Network (CDN) v profilu Verizon (**Azure CDN Standard Verizon** nebo **Azure CDN Premium z Verizon**). Všimněte si, že tato sada IP adres se liší od IP adres, které klient uvidí při přijímání požadavků na body POP. 

Syntaxi operace REST API pro načtení seznamu POP naleznete v tématu [Edge Nodes-list](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Načte aktuální seznam IP adres Microsoft POP pro Azure CDN.

Pokud chcete aplikaci uzamknout, aby přijímala provoz jenom od Microsoftu Azure CDN od Microsoftu, budete muset pro svůj back-end nastavit seznamy ACL pro IP adresu. Můžete také omezit sadu povolených hodnot pro záhlaví "X-Restricted-host" odesílané Azure CDN od Microsoftu. Tyto kroky jsou podrobně popsané níže:

Nakonfigurujte IP funkce acling pro vaše back-endy pro příjem provozu z Azure CDN z adresního prostoru IP adres back-endu Microsoftu a služby infrastruktury Azure. 

* Azure CDN z IP adresy back-endu IPv4 Microsoftu: 147.243.0.0/16
* Azure CDN z IP adresy back-endu IPv6 Microsoftu: 2a01:111:2050::/44

Pokud chcete používat značky služeb s Azure CDN od Microsoftu, použijte prosím značku front-dveří Azure. Rozsahy IP adres a značky služeb pro služby Microsoftu najdete [tady](https://www.microsoft.com/download/details.aspx?id=56519) .


## <a name="typical-use-case"></a>Typický případ použití

Z bezpečnostních důvodů můžete pomocí tohoto seznamu IP adres vymezit, aby se požadavky na zdrojový server vynutily jenom z platného Verizon POP. Pokud někdo například zjistil název hostitele nebo IP adresu pro počáteční server koncového bodu CDN, může jeden požadavek přímo na zdrojový server, takže obchází možnosti škálování a zabezpečení, které poskytuje Azure CDN. Díky nastavení IP adres v vráceném seznamu jako jenom povolených IP adres na zdrojovém serveru je možné tento scénář zabránit. Abyste měli jistotu, že máte nejnovější seznam POP, načtěte ho aspoň jednou denně. 

## <a name="next-steps"></a>Další kroky

Informace o REST API najdete v tématu [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/).
