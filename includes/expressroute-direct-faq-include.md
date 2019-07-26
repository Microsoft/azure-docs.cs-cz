---
title: zahrnout soubor
description: zahrnout soubor
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 07/09/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 43d1942b1413569d77c6c17fee9fff14dc83c924
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362481"
---
### <a name="what-is-expressroute-direct"></a>Co je ExpressRoute Direct?

ExpressRoute Direct poskytuje zákazníkům možnost připojit se přímo ke globální síti Microsoftu v místech partnerských uzlů strategicky rozmístěných po celém světě. ExpressRoute Direct poskytuje připojení Dual 100 nebo 10 GB/s, které podporuje škálovatelné připojení aktivní/aktivní. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Jak se zákazníci připojí k ExpressRoute Direct? 

Zákazníci budou muset spolupracovat s místními poskytovateli a poskytovateli společného umístění a získat tak připojení k ExpressRoute směrovačům, aby mohli využívat ExpressRoute přímo.

### <a name="what-locations-currently-support-expressroute-direct"></a>Jaká umístění momentálně podporují ExpressRoute Direct? 

Dostupné porty budou dynamické a budou k dispozici prostředí PowerShell pro zobrazení kapacity. Umístění zahrnují a *se mohou měnit na základě dostupnosti*:

* Amsterdam
* Amsterdam2
* Auckland 
* Chicago
* Dallas
* Dublin
* Hongkong – zvláštní správní oblast
* Londýn
* Melbourne
* New York City
* Perth
* Antonio San
* Seattle
* Soul
* Silicon Valley
* Singapur2 
* Sydney
* Tchaj-pej
* Tokio
* Toronto
* Washington
* Washington DC2

### <a name="what-is-the-sla-for-expressroute-direct"></a>Jaká je smlouva SLA pro ExpressRoute Direct?

ExpressRoute Direct využije stejnou [podnikovou třídu ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Jaké scénáře by měly zákazníci zvážit pomocí ExpressRoute Direct?  

ExpressRoute Direct poskytuje zákazníkům přímé páry portů 100 nebo 10 GB/s v globální páteřní síti Microsoft. Mezi scénáře, které budou zákazníkům poskytovat největší výhody, patří: Obrovský příjem dat, fyzickou izolaci regulovaných trhů a vyhrazenou kapacitu pro shlukový scénář, jako je například vykreslování. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Jaký je model fakturace pro ExpressRoute Direct? 

ExpressRoute Direct se bude účtovat jako pár portů za pevnou částku. Standardní okruhy budou zahrnuté bez dalších hodin a Premium budou mít za mírné doplňkové poplatky. Výstup se bude účtovat podle jednotlivých okruhů na základě zóny umístění partnerského vztahu.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>Kdy má faktura počáteční páry portů ExpressRoute?

Páry portů ExpressRoute Direct se účtují po 45 dnech do vytvoření prostředku ExpressRoute Direct nebo v případě, že jsou povolené 1 nebo oba odkazy, podle toho, co nastane dřív. Období odkladu 45 se uděluje, aby zákazníci mohli dokončit proces křížového připojení u poskytovatele kolokace.
