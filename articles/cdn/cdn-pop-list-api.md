---
title: Načíst aktuální seznam Verizon POP pro Azure CDN | Dokumentace Microsoftu
description: Zjistěte, jak načíst aktuální seznam Verizon POP pomocí rozhraní REST API.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: 9605b352755933b37819527cecbc4e1ccc30e8aa
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579067"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Načíst aktuální seznam Verizon POP pro Azure CDN

Rozhraní REST API můžete použít k načtení sady IP adres pro bod společnosti Verizon serverů přítomnost (POP). Tyto servery POP provádět požadavky původních serverů, které jsou spojeny s koncovými body Azure Content Delivery Network (CDN) v profilu Verizonu (**Azure CDN Standard od Verizonu** nebo **Azure CDN Premium od Verizonu**). Všimněte si, že tuto sadu IP adres se liší od IP adresy, které klient zobrazoval se vám při zasílání požadavků do bodů POP. 

Syntaxe operaci rozhraní REST API pro načítání seznamu bodů POP, naleznete v tématu [hraničních uzlů – seznam](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="typical-use-case"></a>Typický případ použití

Z bezpečnostních důvodů můžete použít tento seznam IP k vynucení, požadavky na server původu se provedou jenom z místní nabídky platné Verizon. Například někdo zjistit název hostitele nebo IP adresu pro koncový bod CDN zdrojový server, jeden může-li požadavků přímo na původním serveru, proto obcházení škálování a zabezpečení poskytované systémem Azure CDN. Nastavením IP adres ve vráceném seznamu jako pouze povolené IP adresy na původním serveru, můžete zabránit v tomto scénáři. K zajištění, že máte nejnovější seznam POP, načíst, alespoň jednou za den. 

## <a name="next-steps"></a>Další postup

Informace o rozhraní REST API najdete v tématu [REST API služby Azure CDN](https://docs.microsoft.com/rest/api/cdn/).