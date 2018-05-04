---
title: Testování pera | Microsoft Docs
description: Článek obsahuje přehled průnikům testování procesu (pentest) a jak provádět pentest proti vaší aplikace běžící v Azure infrastruktury.
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: barclayn
ms.openlocfilehash: 1b7b6604de80beadf15dabe0d2da1d7a2e5fabd6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="pen-testing"></a>Testování pera
Jednou z výhod používání Azure pro testování aplikací a nasazení je, můžete rychle získat prostředí vytvořili.  Nemusíte si dělat starosti o žádanek, získávání a "stáčení a překrývání" vlastní místní hardware.

Toto je skvělým – ale stále nutné a ujistěte se, proveďte běžné zabezpečení kvůli opatrností. Jednou z věcí, musíte udělat je průnikům otestujte nasazení aplikace v Azure.

Možná už víte, že Microsoft provádí [průnikům testování naše prostředí Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Tento pomůže jednotky Azure vylepšení. 

Jsme nemáte pera testování vaší aplikace pro vás, ale Chápeme, že se má a potřeba provést testování na vaše vlastní aplikace pera. Dobrá vlastnost, je to způsobeno můžete zvýšit zabezpečení aplikací, vám pomoci lépe zabezpečit celý ekosystém Azure.

Co dělat?

Od 15. června 2017, Microsoft už vyžaduje schválení před vést průnikům testy s prostředky Azure. Doporučujeme, aby vyplnit se zákazníky, kteří chtějí oficiálně dokumentu nadcházející průnikům testování oznámeních podporujících zapojení uživatelů s Microsoft Azure [Azure Service průnikům testování oznámení formuláře](https://portal.msrc.microsoft.com/en-us/engage/pentest). Tento proces se vztahuje pouze k Microsoft Azure a nemusí platit pro všechny ostatní cloudové služby Microsoftu. 

>[!IMPORTANT] 
>Při odesílání oznámení o chybách společnosti Microsoft pera testování aktivity se už nevyžaduje zákazníky stále splňovat [Microsoft Cloud Unified průnikům testování pravidla zapojení](https://technet.microsoft.com/mt784683). 

Standardní testy, které můžete provádět, zahrnují:

* Testy na koncové body k odhalení [aplikace otevřete webový projekt zabezpečení (OWASP) top 10 ohrožení zabezpečení](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Testování argumentu neurčité](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) vaše koncových bodů
* [Kontrola portů](https://en.wikipedia.org/wiki/Port_scanner) vaše koncových bodů

Druh jeden typ testu, který nelze provést žádné je [útok na dostupnost služby (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) útoku. To zahrnuje inicializaci útoku DoS sám sebe nebo provádění souvisejících testy, které může určit, ukazují nebo simulovat jakýkoli typ útoku DoS.

## <a name="next-steps"></a>Další postup

- Jste připravení začít s pera testování vaší aplikace hostované ve službě Microsoft Azure? Pokud ano, pak přejděte na přes [přehled testovací průnikům](https://technet.microsoft.com/library/mt784683.aspx) (a klikněte na tlačítko požádat o testování v dolní části stránky vytvořit. 
