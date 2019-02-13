---
title: Testování průniku | Dokumentace Microsoftu
description: Tento článek obsahuje základní informace o testování procesu (pentest) průniku a jak provádět pentest proti svých aplikací spuštěných v infrastruktuře Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2018
ms.author: barclayn
ms.openlocfilehash: bfe289e29a6a2bb048d9fd42323f32ce8c12792c
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110604"
---
# <a name="pen-testing"></a>Testování průniku
Jednou z výhod používání Azure pro testování aplikací a nasazení je, že můžete rychle získat prostředí vytvořená.  Není nutné se starat o žádanek, získání a "r: sledování a stohování" v místním hardwaru.

To je skvělé – ale je stále potřeba Ujistěte se, že provádíte běžná bezpečnostní vypršení platnosti co nejopatrněji. Jednou z věcí, je třeba provést je ho otestujte nasazení aplikace v Azure.

Možná už znáte, aby Microsoft prováděl [testování průniku naše prostředí Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). To pomáhá vám zajistí vylepšení Azure.

Společnost Microsoft není pera otestovat aplikaci za vás, ale rozumí tomu, že se chcete a potřebujete provést na vašich vlastních aplikací testování průniku. To je dobrá věc, protože můžete zlepšit zabezpečení vašich aplikací, vám pomoci lépe zabezpečit celý ekosystém Azure.

Co dělat?

Od 15. června 2017 Microsoft už nevyžaduje předběžné schválení ho nechali testy s prostředky Azure. Nepodnikovým zákazníkům, kteří chtějí formálně dokumentu nadcházející testování průniku s dokumentem s Microsoft Azure k vyplnění [oznámení testování průniku Azure služby formuláře](https://portal.msrc.microsoft.com/en-us/engage/pentest). Tento proces se týká jenom Microsoft Azure a nevztahuje se na jakékoli jiné cloudové služby Microsoftu.

>[!IMPORTANT]
>Při oznámení Microsoft aktivity testování průniku se už nevyžaduje zákazníci i nadále vyhovět [Microsoft Cloud Unified průniku testování pravidla zapojení](https://technet.microsoft.com/mt784683).

Standardní testy, které můžete provádět, patří:

* Testování na koncových bodech, a odhalit [Open Web Application zabezpečení projektu (OWASP) top 10 ohrožení zabezpečení](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Testování argumentu neurčité](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) z vašich koncových bodů
* [Port scanning](https://en.wikipedia.org/wiki/Port_scanner) z vašich koncových bodů

Jeden typ testu, který nelze provést, je libovolný druh [útok na dostupnost služby (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) útoku. Jedná se o zahájení útoku DoS, samotné nebo provádění související testy, které může určit, ukazují nebo simulovat jakýkoli typ útoku DoS.

## <a name="next-steps"></a>Další postup

- Jste připravení začít s pera testování vaší aplikace hostované v Microsoft Azure? Pokud ano, pak přejděte na na [průniku testování pravidla zapojení](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2) a vyplňte formulář testovací oznámení.
