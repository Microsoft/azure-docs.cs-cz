---
title: Testování průniku | Microsoft Docs
description: Tento článek poskytuje přehled procesu testování průniku a o tom, jak provést test pera na aplikaci běžící v infrastruktuře Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2020
ms.author: terrylan
ms.openlocfilehash: af61b6ee1e69d175f47170df30f75832033d61d5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489713"
---
# <a name="penetration-testing"></a>Testování průniku

Jednou z výhod používání Azure pro testování a nasazení aplikací je, že můžete rychle vytvořit prostředí vytvořená. Nemusíte se starat o přemístění, získání a "rackování a skládání" vašeho vlastního místního hardwaru.

Rychlé vytváření prostředí je skvělé – ale pořád je potřeba zajistit, abyste měli jistotu, že byste měli provést běžnou opatrnost z bezpečnostních důvodů. Jednou z věcí, které pravděpodobně chcete udělat, je testování aplikací, které nasazujete v Azure.

Možná už víte, že Microsoft provádí [testování průniku našeho prostředí Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Toto testování pomáhá řídit vylepšení Azure.

Neprovádíme testování průniku vaší aplikace za vás, ale chápeme, že chcete a potřebujete provádět testování na vašich vlastních aplikacích. To je dobré, protože když vylepšujete zabezpečení vašich aplikací, pomůžete zajistit vyšší zabezpečení celého ekosystému Azure.

Od 15. června 2017 už společnost Microsoft nepotřebuje předběžné schválení k provedení testu průniku na prostředky Azure. Tento proces se vztahuje pouze na Microsoft Azure a neplatí pro žádnou jinou službu Microsoft Cloud.

>[!IMPORTANT]
>Zatímco oznamování společnosti Microsoft pro testování pera již nejsou vyžadovány, zákazníci musí stále splňovat pravidla pro [testování průniku v rámci služby Engagement, Microsoft Cloud sjednocení](https://technet.microsoft.com/mt784683).

Mezi standardní testy, které můžete provádět:

* Testuje vaše koncové body, aby se odkryla [5 hlavních chyb zabezpečení projektu webové aplikace (OWASP)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) .
* [Přibližné testování](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) vašich koncových bodů
* [Kontrola portů](https://en.wikipedia.org/wiki/Port_scanner) v koncových bodech

Jeden typ testu pera, který nemůžete provést, je libovolný druh útoku DOS [(Denial of Service)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Tento test zahrnuje inicializaci samotného útoku DoS nebo provádění souvisejících testů, které mohou určit, Ukázat nebo simulovat jakýkoli typ útoku DoS.

>[!Note]
>Microsoft spolupracuje s cloudem BreakingPoint a vytváří rozhraní, ve kterém můžete vygenerovat provoz proti DDoS Protection veřejné IP adresy pro simulace. Další informace o simulaci cloudu zarážky najdete v tématu [ověření DDoS detekce](../../ddos-protection/manage-ddos-protection.md#validate-and-test).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [pravidlech testování průniku v rámci zapojení](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2).