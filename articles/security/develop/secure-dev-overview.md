---
title: Secure development doporučených postupech ohledně Microsoft Azure
description: Osvědčené postupy, které vám pomohou vyvíjet bezpečnějšího kódu a nasadit více zabezpečenou webovou aplikaci v cloudu.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: ee5c043038fbb747e90bca9530cbe2e94c8a95c2
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144461"
---
# <a name="secure-development-best-practices-on-azure"></a>Secure development osvědčených postupů v Azure
Tato série článků představuje aktivity související se zabezpečením a ovládací prvky, které je třeba zvážit při vývoji aplikací pro cloud. Fáze otázky týkající se Microsoft Security Development Lifecycle (SDL) a zabezpečení a koncepty, které je vhodné zvážit během každé fáze životního cyklu se vztahuje. Cílem je může pomoci určit činností a služeb Azure, které můžete použít k návrhu, vývoji a nasazení aplikace lépe zabezpečit v každé fázi životního cyklu.

Doporučení v článcích pocházet z našich zkušenostech s Azure security a prostředí našich zákazníků. Tyto články můžete použít jako referenci pro co byste měli zvážit během určité fázi vašeho vývojového projektu, ale doporučujeme, aby si také přečíst prostřednictvím všech článků od začátku do konce alespoň jednou. Čtení všech článků vás seznámí s koncepty, které je asi v dřívějších fázích vašeho projektu. Implementace těchto konceptů, ještě před vydáním produktu můžete sestavit zabezpečené softwaru řešila požadavky na dodržování předpisů zabezpečení a snížit náklady na vývoj.

Tyto články jsou určené jako zdroj pro profesionální návrháře využívající softwaru, vývojáři, a testery na všech úrovních, která sestavení a nasazení zabezpečených aplikací Azure.

## <a name="overview"></a>Přehled

Zabezpečení je jedním z nejdůležitějších aspektů libovolnou aplikaci a není jednoduché věc, kterou chcete-li získat správný. Naštěstí Azure obsahuje mnoho služeb, které vám může pomoct zabezpečit svoji aplikaci v cloudu. Tyto články řeší činností a služeb Azure můžete implementovat v každé fázi životního cyklu vývoje softwaru pro vývoj bezpečnějšího kódu a nasadit více zabezpečenou webovou aplikaci v cloudu.

## <a name="security-development-lifecycle"></a>Životní cyklus vývoje zabezpečení

Následující osvědčené postupy pro vývoj zabezpečeného softwaru vyžaduje integraci zabezpečení do každé fáze cyklu vývoje softwaru, z Analýza požadavků do režimu údržby, bez ohledu na to metodologie projektu ([vodopádu](https://en.wikipedia.org/wiki/Waterfall_model), [agilní](https://en.wikipedia.org/wiki/Agile_software_development), nebo [DevOps](https://en.wikipedia.org/wiki/DevOps)). Probuzení úniky dat profilu vysokou a využívání provozní zabezpečení chyby jsou Principy víc vývojářů, že zabezpečení by měl být zákazníky a vyřešené v celém procesu vývoje.

Vyšší je opravit problém v cyklu vývoje, čím se oprava bude stát. Problémy se zabezpečením se žádná výjimka. Pokud Ignorovat problémy se zabezpečením v počátečních fázích vývoje softwaru, mohou jednotlivé fáze, která následuje dědit ohrožení zabezpečení předchozí fáze. Finální produkt bude nashromáždili několik problémy se zabezpečením a možnost porušení zabezpečení. Umožňuje vytvářet zabezpečení do každé fáze životního cyklu vývoje včasné rozpoznávání potíží a pomůže vám snížit náklady na vývoj.

Budeme postupovat podle fáze Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) zavést činností a služeb Azure, které můžete použít ke splnění zabezpečené praktik vývoje v každé fázi životního cyklu.

Fáze procesu SDL jsou:

  - Školení
  - Požadavky
  - Návrh
  - Implementace
  - Ověření
  - Vydat
  - Odpověď

![Security Development Lifecycle](./media/secure-dev-overview/01-sdl-phase.png)

V těchto článcích budeme seskupení SDL fáze návrhu, vývoji a nasazování.

## <a name="engage-your-organizations-security-team"></a>Zapojení týmu zabezpečení vaší organizace

Vaše organizace může mít formální aplikace zabezpečení, který vám pomůže s aktivity související se zabezpečením od začátku do konce během životního cyklu vývoje. Pokud má vaše organizace týmů zabezpečení a dodržování předpisů, ujistěte se, že jste zapojte je před zahájením vývoj vaší aplikace. Zeptejte se v jednotlivých fázích SDL, zda jsou všechny úlohy, které jste zmeškali.

Chápeme, že mnoho čtenáři nemusí mít zapojení týmu zabezpečení nebo dodržování předpisů. Tyto články vám pomůže v bezpečnostní otázky a rozhodnutí, která je třeba zvážit v každé fázi do procesu SDL.

## <a name="resources"></a>Zdroje a prostředky

Další informace o vývoji bezpečných aplikací a k zabezpečení vašich aplikací v Azure, použijte následující prostředky:

[Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – The SDL je proces vývoje softwaru od Microsoftu, která pomáhá vývojářům vytvářet bezpečnější software. Vám může pomoct vyřešit požadavky na dodržování předpisů zabezpečení a snižuje nutnost náklady na vývoj.

[Otevřete Web Application Security projektu (OWASP)](https://www.owasp.org/index.php/Main_Page) – OWASP je online komunita, která vytváří volně dostupných články, metodologie, dokumentaci, nástroje a technologie v oblasti zabezpečení webové aplikace.

[Doručením (push) vlevo, jako je nadřízeného](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) – série online články, které popisuje různé typy aktivity související se zabezpečením aplikace, které vývojáři by se měla dokončit k vytváření bezpečnějšího kódu.

[Platforma identit Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) – platforma identit Microsoft jsou výsledkem vývoje služby Azure AD identity služby a vývojářské platformy. Je to plně funkční platforma, která se skládá z ověřovací služba, open source knihoven, registrace aplikace a konfiguraci, dokumentaci pro vývojáře, ukázky kódu a další obsah pro vývojáře. Platforma identit Microsoft podporuje standardní protokoly jako jsou OAuth 2.0 a OpenID Connect.

[Osvědčené postupy zabezpečení pro řešení Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – kolekce doporučené postupy zabezpečení pro použití při návrhu, nasazení a správě cloudových řešení pomocí služby Azure. Tento dokument je určen jako zdroj pro IT profesionály. To může zahrnovat návrháři, architekty, vývojáři a testery, kteří se vytvářet a nasazovat řešení zabezpečení Azure.

[Zabezpečení a dodržování předpisů plány v Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – zabezpečení Azure a dodržování předpisů plány jsou prostředky, které vám pomůžou vytvářet a spouštět aplikace s využitím cloudu, které dodržovat přísnější předpisy a standardy.

## <a name="next-steps"></a>Další postup
V následujících článcích doporučujeme kontrolních mechanismů pro zabezpečení a aktivity, které vám může pomoct návrh, vývoj a nasazení zabezpečené aplikace.

- [Návrh zabezpečených aplikací](secure-design.md)
- [Vývoj zabezpečených aplikací](secure-develop.md)
- [Nasazení zabezpečené aplikace](secure-deploy.md)
