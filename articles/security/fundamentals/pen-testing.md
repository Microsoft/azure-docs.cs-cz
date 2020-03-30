---
title: Testování perem | Dokumenty společnosti Microsoft
description: Článek obsahuje přehled procesu penetračního testování (pentest) a jak provádět test pentest proti vašim aplikacím spuštěným v infrastruktuře Azure.
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
ms.date: 08/13/2018
ms.author: barclayn
ms.openlocfilehash: 3a2addce83862ef109089f1474330f3821daaed7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726718"
---
# <a name="penetration-testing"></a>Penetrační testování
Jednou z výhod používání Azure pro testování a nasazování aplikací je, že můžete rychle získat vytvořená prostředí. Nemusíte se starat o žádanky, získávání a "regály a stohování" vlastní místní hardware.

To je skvělé - ale stále je třeba, aby se ujistil, že budete provádět své běžné bezpečnostní due diligence. Jednou z věcí, které budete pravděpodobně chtít udělat, je penetrační test aplikací, které nasadíte v Azure.

Možná už víte, že Microsoft provádí [penetrační testování našeho prostředí Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). To pomáhá řídit vylepšení Azure.

Netestujeme pro vás vaši aplikaci, ale chápeme, že budete chtít a budete muset provést testování na vlastních aplikacích. To je dobře, protože když zvýšíte zabezpečení svých aplikací, pomůžete zvýšit zabezpečení celého ekosystému Azure.

června 2017 microsoft už nevyžaduje předběžné schválení k provedení penetračního testu s prostředky Azure. Zákazníkům, kteří chtějí formálně zdokumentovat nadcházející zapojení penetračního testování proti Microsoft Azure, se doporučuje vyplnit [formulář oznámení o testování penetračního testování služby Azure](https://portal.msrc.microsoft.com/en-us/engage/pentest). Tento proces se vztahuje pouze k Microsoft Azure a nevztahuje se na žádnou jinou službu Microsoft Cloud Service.

>[!IMPORTANT]
>I když oznámení společnosti Microsoft o aktivitách testování pera již není vyžadováno, zákazníci musí stále dodržovat [pravidla sjednoceného testování rozšíření služby Microsoft Cloud .](https://technet.microsoft.com/mt784683)

Mezi standardní testy, které můžete provést, patří:

* Testy koncových bodů k odhalení [10 nejlepších chyb zabezpečení projektu Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* Testování koncových bodů [Fuzz](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)
* [Prohledávání](https://en.wikipedia.org/wiki/Port_scanner) koncových bodů porty

Jeden typ testu, který nelze provést, je jakýkoli druh útoku [odmítnutí služby (DoS).](https://en.wikipedia.org/wiki/Denial-of-service_attack) To zahrnuje zahájení samotného útoku DoS nebo provádění souvisejících testů, které mohou určit, demonstrovat nebo simulovat jakýkoli typ útoku DoS.

## <a name="next-steps"></a>Další kroky

- Pokud chcete formálně zdokumentovat nadcházející penetrační testování proti vašim aplikacím hostovaným v Microsoft Azure, přejdete na [pravidla pro testování penetračního testování a](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) vyplňte formulář oznámení o testování.
