---
title: Bezpečné osvědčené postupy pro vývoj v Microsoft Azure
description: Doporučené postupy, které vám pomohou vyvinout bezpečnější kód a nasadit bezpečnější aplikaci v cloudu.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c4314a0dcbbcb907ef4d6de0a2788cf04dfe1641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934871"
---
# <a name="secure-development-best-practices-on-azure"></a>Bezpečné osvědčené postupy pro vývoj v Azure
Tato řada článků představuje aktivity zabezpečení a ovládací prvky, které je třeba zvážit při vývoji aplikací pro cloud. Jsou zahrnuty fáze životního cyklu vývoje zabezpečení společnosti Microsoft (SDL) a bezpečnostní otázky a koncepty, které je třeba zvážit během každé fáze životního cyklu. Cílem je pomoci vám definovat aktivity a služby Azure, které můžete použít v každé fázi životního cyklu k návrhu, vývoji a nasazení bezpečnější aplikace.

Doporučení v článcích pocházejí z našich zkušeností se zabezpečením Azure a ze zkušeností našich zákazníků. Tyto články můžete použít jako odkaz na to, co byste měli zvážit během určité fáze vašeho vývojového projektu, ale doporučujeme také přečíst všechny články od začátku do konce alespoň jednou. Čtení všech článků vás seznámí s koncepty, které jste možná vynechali v dřívějších fázích projektu. Implementace těchto konceptů před vydáním produktu vám může pomoci vytvořit zabezpečený software, vyřešit požadavky na dodržování předpisů zabezpečení a snížit náklady na vývoj.

Tyto články jsou určeny jako prostředek pro návrháře softwaru, vývojáře a testery na všech úrovních, kteří vytvářejí a nasazují zabezpečené aplikace Azure.

## <a name="overview"></a>Přehled

Zabezpečení je jedním z nejdůležitějších aspektů každé aplikace, a to není jednoduchá věc, aby si právo. Azure naštěstí poskytuje mnoho služeb, které vám můžou pomoct zabezpečit vaši aplikaci v cloudu. Tyto články se týkají aktivit a služeb Azure, které můžete implementovat v každé fázi životního cyklu vývoje softwaru, které vám pomohou vyvinout bezpečnější kód a nasadit bezpečnější aplikaci v cloudu.

## <a name="security-development-lifecycle"></a>Životní cyklus vývoje zabezpečení

Dodržování osvědčených postupů pro bezpečný vývoj softwaru vyžaduje integraci zabezpečení do každé fáze životního cyklu vývoje softwaru, od analýzy požadavků až po údržbu, bez ohledu na metodiku projektu ([vodopád](https://en.wikipedia.org/wiki/Waterfall_model), [agilní](https://en.wikipedia.org/wiki/Agile_software_development)nebo [DevOps](https://en.wikipedia.org/wiki/DevOps)). V důsledku významných narušení bezpečnosti dat a využívání provozních bezpečnostních chyb více vývojářů chápe, že bezpečnost je třeba řešit v průběhu celého procesu vývoje.

Čím později problém vyřešíte v životním cyklu vývoje, tím více vás oprava bude stát. Problémy se zabezpečením nejsou výjimkou. Pokud ignorujete problémy se zabezpečením v počátečních fázích vývoje softwaru, každá následující fáze může zdědit chyby zabezpečení předchozí fáze. Váš konečný produkt bude mít nahromaděné několik bezpečnostních problémů a možnost porušení. Vytváření zabezpečení do každé fáze životního cyklu vývoje vám pomůže včas zachytit problémy a pomůže vám snížit náklady na vývoj.

Sledujeme fáze životního cyklu vývoje zabezpečení microsoftu [(SDL) a](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) zavádíme aktivity a služby Azure, které můžete použít ke splnění postupů zabezpečeného vývoje softwaru v každé fázi životního cyklu.

Fáze SDL jsou:

  - Školení
  - Požadavky
  - Návrh
  - Implementace
  - Ověření
  - Vydat
  - Odpověď

![Životní cyklus vývoje zabezpečení](./media/secure-dev-overview/01-sdl-phase.png)

V těchto článcích seskupíme fáze SDL do návrhu, vývoje a nasazení.

## <a name="engage-your-organizations-security-team"></a>Zapojení bezpečnostního týmu vaší organizace

Vaše organizace může mít formální program zabezpečení aplikací, který vám pomůže s aktivitami zabezpečení od začátku do konce během životního cyklu vývoje. Pokud má vaše organizace týmy zabezpečení a dodržování předpisů, nezapomeňte je zaujmout dříve, než začnete vyvíjet aplikaci. Zeptejte se jich v každé fázi SDL, zda existují nějaké úkoly, které jste vynechal.

Chápeme, že mnoho čtenářů nemusí mít tým zabezpečení nebo dodržování předpisů, aby se zapojili. Tyto články vám mohou pomoci v bezpečnostních otázkách a rozhodnutích, které je třeba zvážit v každé fázi SDL.

## <a name="resources"></a>Prostředky

Další informace o vývoji zabezpečených aplikací a zabezpečení aplikací v Azure vám pomůžou získat další informace o vývoji zabezpečených aplikací:

[Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – SDL je proces vývoje softwaru od společnosti Microsoft, který pomáhá vývojářům vytvářet bezpečnější software. Pomáhá řešit požadavky na dodržování předpisů zabezpečení a zároveň snižuje náklady na vývoj.

[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) - OWASP je online komunita, která vyrábí volně dostupné články, metodiky, dokumentaci, nástroje a technologie v oblasti zabezpečení webových aplikací.

[Tlačení vlevo, jako šéf](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) - série on-line článků, které nastiňuje různé typy aktivit zabezpečení aplikací, které vývojáři by měli dokončit vytvořit bezpečnější kód.

[Platforma identit Microsoftu](../../active-directory/develop/index.yml) – platforma identit Microsoftu je vývoj služby Identity Azure AD a vývojářské platformy. Jedná se o plnohodnotnou platformu, která se skládá z ověřovací služby, knihoven s otevřeným zdrojovým kódem, registrace a konfigurace aplikací, úplné dokumentace pro vývojáře, ukázek kódu a dalšího obsahu pro vývojáře. Platforma identit microsoftu podporuje standardní protokoly jako OAuth 2.0 a OpenID Connect.

[Doporučené postupy zabezpečení pro řešení Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – kolekce doporučených postupů zabezpečení, které se používají při navrhování, nasazování a správě cloudových řešení pomocí Azure. Tento dokument má být zdrojem pro IT profesionály. To může zahrnovat návrháře, architekty, vývojáře a testery, kteří vytvářejí a nasazují zabezpečená řešení Azure.

[Plány zabezpečení a dodržování předpisů v Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – Azure Security and Compliance Blueprints jsou prostředky, které vám můžou pomoct vytvářet a spouštět cloudové aplikace, které splňují přísné předpisy a standardy.

## <a name="next-steps"></a>Další kroky
V následujících článcích doporučujeme ovládací prvky zabezpečení a aktivity, které vám pomohou navrhovat, vyvíjet a nasazovat zabezpečené aplikace.

- [Navrhujte zabezpečené aplikace](secure-design.md)
- [Vývoj zabezpečených aplikací](secure-develop.md)
- [Nasazení zabezpečených aplikací](secure-deploy.md)
