---
title: Osvědčené postupy pro zabezpečení vývoje Microsoft Azure
description: Osvědčené postupy, které vám pomůžou při vývoji bezpečnějšího kódu a nasazování bezpečnější aplikace v cloudu.
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
ms.openlocfilehash: baaa311620f5c10948aa3494002ce359cc5dab28
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517168"
---
# <a name="secure-development-best-practices-on-azure"></a>Osvědčené postupy pro zabezpečení vývoje v Azure
Tato série článků představuje bezpečnostní aktivity a ovládací prvky, které je potřeba vzít v úvahu při vývoji aplikací pro Cloud. Pojednává o fázích Microsoft Security Development Lifecycle (SDL) a bezpečnostních otázkách a konceptech, které je potřeba vzít v úvahu během každé fáze životního cyklu. Cílem je pomáhat vám definovat aktivity a služby Azure, které můžete v jednotlivých fázích životního cyklu použít k návrhu, vývoji a nasazení bezpečnější aplikace.

Doporučení v článcích pocházejí z našeho prostředí se zabezpečením Azure a z prostředí našich zákazníků. Tyto články můžete použít jako referenci k tomu, co byste měli vzít v úvahu v konkrétní fázi vývojového projektu, ale doporučujeme, abyste si přečetli také všechny články od začátku až do konce alespoň jednou. Čtení všech článků vás seznámí s koncepty, které jste pravděpodobně vynechali v předchozích fázích projektu. Implementace těchto konceptů před vydáním produktu vám pomůže vytvořit zabezpečený software, řešit požadavky na dodržování zabezpečení a snižovat náklady na vývoj.

Tyto články mají být prostředkem pro návrháře softwaru, vývojáře a testery na všech úrovních, kteří sestavují a nasazují zabezpečené aplikace Azure.

## <a name="overview"></a>Přehled

Zabezpečení je jedním z nejdůležitějších aspektů jakékoli aplikace a není to jednoduché. Naštěstí Azure poskytuje spoustu služeb, které vám pomůžou zajistit zabezpečení vaší aplikace v cloudu. Tyto články řeší aktivity a služby Azure, které můžete implementovat v každé fázi životního cyklu vývoje softwaru, abyste mohli vyvíjet bezpečnější kód a nasazovat bezpečnější aplikace v cloudu.

## <a name="security-development-lifecycle"></a>Životní cyklus vývoje zabezpečení

Následující osvědčené postupy pro bezpečný vývoj softwaru vyžadují integraci zabezpečení do každé fáze životního cyklu vývoje softwaru, od analýzy požadavků až po údržbu, bez ohledu na metodologii projektu ([vodopád](https://en.wikipedia.org/wiki/Waterfall_model), [agilní](https://en.wikipedia.org/wiki/Agile_software_development)nebo [DevOps](https://en.wikipedia.org/wiki/DevOps)). V případě narušení zabezpečení s vysokým profilem a využití chyb zabezpečení systému jsou více vývojářů obeznámeni s tím, že je nutné řešit zabezpečení v průběhu procesu vývoje.

Později vyřešíte problém v životním cyklu vývoje, což bude mít za následek tím větší náklady. Problémy se zabezpečením nejsou žádné výjimky. Pokud nevyřešíte problémy se zabezpečením v počátečních fázích vývoje softwaru, každá z následujících fází může dědit ohrožení zabezpečení předchozí fáze. Konečný produkt bude mít za sebou několik problémů se zabezpečením a možnost porušení. Sestavování zabezpečení v každé fázi životního cyklu vývoje vám pomůže včas zachytit problémy a pomůže vám snížit náklady na vývoj.

Provedeme fáze Microsoft [SDL (Security Development Lifecycle)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) k zavedení aktivit a služeb Azure, které můžete použít ke splnění postupů zabezpečení softwaru v každé fázi životního cyklu.

Fáze SDL:

  - Školení
  - Požadavky
  - Návrh
  - Implementace
  - Ověření
  - Vydat
  - Odpověď

![Životní cyklus vývoje zabezpečení (Security Development Lifecycle)](./media/secure-dev-overview/01-sdl-phase.png)

V těchto článcích se fáze SDL seskupují do návrhu, vývoje a nasazení.

## <a name="engage-your-organizations-security-team"></a>Zapojení týmu zabezpečení vaší organizace

Vaše organizace může mít formální program zabezpečení aplikací, který vám pomůže s aktivitami zabezpečení od začátku do konce během životního cyklu vývoje. Pokud má vaše organizace týmy zabezpečení a dodržování předpisů, ujistěte se, že je před zahájením vývoje aplikace zahájíte. Zeptejte se v každé fázi SDL, jestli nedošlo k nějakým úlohám, které jste vynechali.

Chápeme, že mnoho čtenářů nemusí mít tým zabezpečení nebo dodržování předpisů, aby mohl zapojit. Tyto články vám pomůžou s bezpečnostními otázkami a rozhodnutími, která je potřeba vzít v úvahu v každé fázi SDL.

## <a name="resources"></a>Zdroje informací

Následující zdroje vám pomůžou získat další informace o vývoji zabezpečených aplikací a k zabezpečení aplikací v Azure:

[Microsoft Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) – SDL je proces vývoje softwaru od Microsoftu, který vývojářům pomáhá sestavovat bezpečnější software. Pomáhá řešit požadavky na dodržování předpisů zabezpečení a současně snižovat náklady na vývoj.

[Otevřete projekt Web Application Security (OWASP)](https://www.owasp.org/index.php/Main_Page) – OWASP je online komunita, která v poli zabezpečení webových aplikací vytváří volně dostupné články, metodologie, dokumentaci, nástroje a technologie.

[Vložením doleva, jako je například vedoucí, získáte](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) řadu Online článků, které popisují různé typy aktivit zabezpečení aplikací, které vývojáři dokončí k vytváření bezpečnějšího kódu.

[Microsoft Identity Platform](../../active-directory/develop/index.yml) – Microsoft Identity Platform je vývoj služby identity a vývojářské platformy Azure AD. Je to plně vybavená platforma, která se skládá ze služby ověřování, open source knihoven, registrace a konfigurace aplikací, úplné dokumentace pro vývojáře, ukázek kódu a dalšího obsahu pro vývojáře. Platforma Microsoft identity podporuje standardní protokoly jako OAuth 2,0 a OpenID Connect.

[Osvědčené postupy zabezpečení pro řešení Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – kolekce osvědčených postupů zabezpečení pro použití při navrhování, nasazování a správě cloudových řešení pomocí Azure. Tento dokument je určený jako prostředek pro odborníky v oblasti IT. To může zahrnovat návrháře, architekty, vývojáře a testery, kteří sestavují a nasazují zabezpečená řešení Azure.

[Plány zabezpečení a dodržování předpisů v Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – plány zabezpečení a dodržování předpisů Azure jsou prostředky, které vám pomůžou vytvářet a spouštět cloudové aplikace, které jsou v souladu s přísnými předpisy a standardy.

## <a name="next-steps"></a>Další kroky
V následujících článcích doporučujeme kontrolu zabezpečení a aktivity, které vám pomůžou navrhovat, vyvíjet a nasazovat zabezpečené aplikace.

- [Návrh zabezpečených aplikací](secure-design.md)
- [Vývoj zabezpečených aplikací](secure-develop.md)
- [Nasazení zabezpečených aplikací](secure-deploy.md)