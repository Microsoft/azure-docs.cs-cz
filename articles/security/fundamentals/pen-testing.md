---
title: Testování průniku | Microsoft Docs
description: Tento článek poskytuje přehled procesu testování průniku a o tom, jak provést test pera na aplikaci běžící v infrastruktuře Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: terrylan
ms.openlocfilehash: 9f65133f622c71189f35a1533e278ca09ab02085
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550197"
---
# <a name="penetration-testing"></a>Testování průniku

Jednou z výhod používání Azure pro testování a nasazení aplikací je, že můžete rychle vytvořit prostředí vytvořená. Nemusíte se starat o přemístění, získání a "rackování a skládání" vašeho vlastního místního hardwaru.

Rychlé vytváření prostředí je skvělé – ale pořád je potřeba zajistit, abyste měli jistotu, že byste měli provést běžnou opatrnost z bezpečnostních důvodů. Jednou z věcí, které pravděpodobně chcete udělat, je testování aplikací, které nasazujete v Azure.

Neprovádíme testování průniku vaší aplikace za vás, ale chápeme, že chcete a potřebujete provádět testování na vašich vlastních aplikacích. To je dobré, protože když vylepšujete zabezpečení vašich aplikací, pomůžete zajistit vyšší zabezpečení celého ekosystému Azure.

Od 15. června 2017 Microsoft k provádění penetračních testů prostředků Azure nevyžaduje předběžné schválení. Tento proces se vztahuje pouze na Microsoft Azure a neplatí pro žádnou jinou službu Microsoft Cloud.

>[!IMPORTANT]
>Zatímco oznamování společnosti Microsoft pro testování pera již nejsou vyžadovány, zákazníci musí stále splňovat pravidla pro [testování průniku v rámci služby Engagement, Microsoft Cloud sjednocení](https://technet.microsoft.com/mt784683).

Mezi standardní testy, které můžete provádět:

* Testuje vaše koncové body, aby se odkryla [5 hlavních chyb zabezpečení projektu webové aplikace (OWASP)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) .
* [Testování koncových bodů neplatným vstupem](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)
* [Prohledávání portů](https://en.wikipedia.org/wiki/Port_scanner) koncových bodů

Jeden typ testu pera, který nemůžete provést, je libovolný druh útoku DOS [(Denial of Service)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Tento test zahrnuje inicializaci samotného útoku DoS nebo provádění souvisejících testů, které mohou určit, Ukázat nebo simulovat jakýkoli typ útoku DoS.

>[!Note]
>Microsoft spolupracuje s cloudem BreakingPoint a vytváří rozhraní, ve kterém můžete vygenerovat provoz proti DDoS Protection veřejné IP adresy pro simulace. Další informace o simulaci cloudu BreakingPoint najdete v tématu [testování prostřednictvím simulací](../../ddos-protection/test-through-simulations.md).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [pravidlech testování průniku v rámci zapojení](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2).
