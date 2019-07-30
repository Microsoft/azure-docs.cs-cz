---
title: Posouzení scénáře – Přizpůsobte si
titleSuffix: Azure Cognitive Services
description: Přizpůsobování se dá použít v každé situaci, kdy vaše aplikace může vybrat správnou položku, akci nebo produkt k zobrazení, aby bylo lépe lepší, dosáhli jsme lepších obchodních výsledků nebo zlepšili produktivitu.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 465fba3a466aceaf9ef7b71e4b1957bfdbcad766
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663641"
---
# <a name="where-can-you-use-personalizer"></a>Kde se dá služba Personalizace využít?

Přizpůsobené možnosti můžete použít v jakékoli situaci, kdy aplikace potřebuje vybrat správnou položku, akci nebo produkt, aby se zajistilo lepší, dosáhnout lepších obchodních výsledků nebo zvýšit produktivitu. 

Individuální nastavení používá Machine Learning k výběru akce pro zobrazení uživatele. Výběr se může výrazně lišit v závislosti na množství, kvalitě a distribuci dat odesílaných službě.

### <a name="checklist-for-applying-personalizer"></a>Kontrolní seznam pro použití přizpůsobeného přizpůsobování


Přizpůsobené přizpůsobování můžete použít v situacích:

* Pro vaši aplikaci máte cíl pro podnikání nebo použitelnost.
* V aplikaci máte místo, kde se pro účely kontextového rozhodování o tom, co Ukázat uživatelům, tento cíl vylepší.
* Nejlepší volba může a měla by se poznat z kolektivního chování uživatelů a celkové skóre odměňování.
* Používání strojového učení pro přizpůsobení se řídí [pokyny pro použití](ethics-responsible-use.md) a volbami, které jste zvolili.
* Kontextové rozhodnutí může být vyjádřeno jako hodnocení nejlepší možnosti (akce) z omezené sady možností.
* To, jak dobře se vybraná volba rozpracované pro vaši aplikaci může určit měřením určitého aspektu chování uživatelů, a jejím vyjádřením ve _skóre odměňování_. Toto je číslo v rozmezí od-1 do 1.
* Skóre odměňování nepřináší příliš mnoho nenalezených nebo externích faktorů. Doba trvání experimentu je dostatečně nízká, aby bylo možné vypočítat skóre odměňování, zatímco je stále relevantní.
* Můžete vyjádřit kontext pro řazení jako seznam alespoň 5 [funkcí](concepts-features.md) , o kterých se domníváte, že vám pomůžete vybrat správnou volbu a že neobsahují osobně identifikovatelné osobní údaje. (PII).
* Máte informace o jednotlivých možnostech obsahu, _akcích_, jako seznam o nejméně 5 [funkcích](concepts-features.md) , které si myslíte, že přizpůsobíte správnou volbu.
* Vaše aplikace může uchovávat data dostatečně dlouho a shromažďovat tak historii nejméně 100 000 interakcí.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Požadavky na strojové učení pro použití přizpůsobení

Přizpůsobený objekt je založený na posílení učení, přístupu ke strojovém učení, které je výukou názorů, které vám dáváme. 

Přizpůsobené aplikace se seznámí nejlépe v situacích, kdy:

* V případě, že je problém v průběhu času (například předvolby zprávy nebo způsobu), existuje dostatek událostí, aby zůstaly nad optimálním přizpůsobením. Přizpůsobený modul se přizpůsobí nepřetržité změně reálného světa, ale výsledky nebudou optimální, pokud není dostatek událostí a data, která by se dala zjistit a usadit na nové vzory. Měli byste zvolit případ použití, který je často k dispozici. Zvažte možnost Hledat případy použití, které se vyskytují minimálně 500 časů za den.
* Kontext a akce mají dostatek [funkcí](concepts-features.md) pro usnadnění učení.
* Pro každé volání je k dispozici méně než 50 akcí.
* Nastavení uchovávání dat umožňují přizpůsobování shromažďování dostatečného množství dat, aby bylo možné provádět vyhodnocení a optimalizaci zásad offline. Obvykle se jedná o minimálně 50 000 datových bodů.

## <a name="monitor-effectiveness-of-personalizer"></a>Monitorování účinnosti přizpůsobení

Efektivitu přizpůsobeného přizpůsobování můžete pravidelně monitorovat prováděním [offline vyhodnocení](concepts-offline-evaluation.md).

## <a name="use-personalizer-with-recommendation-engines"></a>Použití přizpůsobení s moduly s doporučeními

Mnohé společnosti využívají moduly doporučení, marketingové a týmové nástroje, segmentování skupin a clusteringu, filtrování pro spolupráci a další způsoby, jak doporučit produkty od velkého katalogu zákazníkům.

Úložiště GitHubu, které [doporučuje Microsoft](https://github.com/Microsoft/Recommenders) , poskytuje příklady a osvědčené postupy pro vytváření systémů doporučení, které se poskytují jako Jupyter poznámkové bloky. Poskytuje pracovní příklady pro přípravu dat, sestavování modelů, vyhodnocování, ladění a zprovozňováníí pro doporučení pro mnohé běžné přístupy, včetně xDeepFM, správní rady, ALS, MKP, DKN.

Přizpůsobování může pracovat s modulem doporučení, když je přítomen.

* Moduly doporučení využívají velké množství položek (například 500 000) a doporučují podmnožinu (například prvních 20) ze stovek nebo tisíců možností.
* Přizpůsobený modul přebírá malý počet akcí s velkým množstvím informací o nich a řadí je v reálném čase pro daný bohatý kontext, zatímco většina modulů pro doporučení používá jenom několik atributů o uživatelích, produktech a jejich interakcích.
* Přizpůsobený modul je navržený tak, aby se samostatně seznámil s uživatelskými preferencemi, což vám poskytne lepší výsledky, při kterých se rychle mění obsah, jako jsou novinky, živé události, živý obsah komunity, obsah s denními aktualizacemi nebo sezónní obsah.

Běžným použitím je pořídit výstup modulu doporučení (například prvních 20 produktů pro určitého zákazníka) a použít ho jako vstupní akce pro přizpůsobené aplikace.

## <a name="next-steps"></a>Další kroky

[Etika & odpovědná za použití](ethics-responsible-use.md).