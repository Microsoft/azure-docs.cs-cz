---
title: Posouzení a zmírnění rovnosti v modelech strojového učení
titleSuffix: Azure Machine Learning
description: Přečtěte si o nespravedlivosti v modelech strojového učení a o tom, jak může balíček Pythonu Fairlearn pomáhat při vytváření spravedlivých modelů.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/02/2020
ms.openlocfilehash: c21ec0329a7b5716a00262b7422296df3afe208b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598602"
---
# <a name="fairness-in-machine-learning-models"></a>Spravedlivost v modelech strojového učení

Přečtěte si o nerovnosti ve strojovém učení a o tom, jak Fairlearn Open Source balíček python vám může pomáhat při sestavování modelů, které jsou příznivější.

## <a name="what-is-fairness-in-machine-learning-systems"></a>Jaké jsou spravedlivé v systémech Machine Learning?

Umělá logika a systémy strojového učení můžou zobrazovat nerovné chování. Jedním ze způsobů, jak definovat nerovné chování, je poškození nebo dopad na lidi. Existuje mnoho typů poškození, které mohou systémy AI vyvolat. Existují dva běžné typy AI, které způsobují poškození:

- Poškození přidělení: systém AI rozšiřuje nebo odpírá příležitosti, prostředky nebo informace. Příklady zahrnují zařazení, školní přístup a půjčování, kde model může být mnohem lepší při vybírání kvalitních kandidátů mezi konkrétní skupinou lidí, než mezi ostatními skupinami.

- Poškození kvality služby: systém AI nefunguje stejně jako jedna skupina lidí, protože to pro jiný má. V takovém případě může systém pro rozpoznávání hlasu nefungovat i u žen, jak to dělá pro muže.

Chcete-li omezit nerovné chování v systémech AI, je nutné tyto škody vyhodnotit a zmírnit.

>[!NOTE]
> Spravedlivé je společenská a technická výzva. V kvantitativních metrikách spravedlivosti se nezachycují spousta aspektů spravedlnosti, jako je Soudní dvůr a řádný proces. Řadu kvantitativních metrik pro spravedlivost se navíc nedají splnit současně. Cílem je umožnit člověkům vyhodnotit různé strategie pro zmírnění rizik a pak učinit kompromisy, které jsou vhodné pro jejich scénář.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Posouzení a zmírnění spravedlnosti pomocí Fairlearn

Fairlearn je open source balíček Pythonu, který vývojářům v systémech Machine Learning umožňuje vyhodnotit jeho neškodné dodržování předpisů a zmírnit pozorně zjištěné problémy s spravedlivou škodou.

Fairlearn má dvě komponenty:

- Řídicí panel posouzení: pomůcka poznámkového bloku Jupyter pro vyhodnocení způsobu, jakým předpovědi modelu ovlivňuje různé skupiny. Umožňuje také porovnání více modelů pomocí měření vlivu a metriky výkonu.
- Algoritmy zmírnění: Sada algoritmů pro zmírnění nespravedlivosti v binární klasifikaci a regresi.

Společně tyto komponenty umožňují odborníkům přes data a vedoucím pracovníkům proměňovat jakékoli kompromisy týkající se spravedlnosti a výkonu a vybrat strategii pro zmírnění rizik, která nejlépe vyhovuje jejich potřebám.

## <a name="fairness-assessment"></a>Posouzení spravedlivosti

V Fairlearn se spravedlivě rozkládá i v případě, že se jedná o přizpůsobenou **skupinu**, na které se požádá: které skupiny jednotlivců jsou ohrožené poškozením?

Příslušné skupiny, označované také jako podmnožiny, jsou definovány prostřednictvím **citlivých funkcí** nebo citlivých atributů. Citlivé funkce jsou předány do Fairlearn Estimator jako vektor nebo matice s názvem `sensitive_features` . Pojem naznačuje, že při posuzování spravedlnosti skupin by měl být System Designer citlivý na tyto funkce. S ohledem na to, jestli tyto funkce obsahují dopad na ochranu osobních údajů v důsledku informací o osobním identifikaci Ale slovo "citlivé" neznamená, že tyto funkce by neměly být použity pro předpovědi.

Během fáze hodnocení se spravedlivě kvantifikuje prostřednictvím metrik neparitních hodnot. **Metriky neparity** můžou vyhodnotit a porovnat chování modelu v různých skupinách buď jako poměry, nebo jako rozdíly. Fairlearn podporuje dvě třídy neparitních metrik:


- Neparitný výkon modelu: tyto sady metrik vypočítávají neparitní (rozdílové) v hodnotách vybrané metriky výkonu v různých podskupinách. Možné příklady:

  - Míra přesnosti pro neparitu
  - míra nerovnosti v chybových sazbách
  - neparitní v přesnosti
  - neparitní při odvolání
  - neparitní v MAE
  - mnoho dalších

- Míra nekonzistence pro výběr: Tato metrika obsahuje rozdíl mezi různými podskupinami v sazbě výběru. Příkladem je neparita v míře schvalování půjček. Míra výběru znamená zlomky datapoints v každé třídě klasifikované jako 1 (v binární klasifikaci) nebo distribuci hodnot předpovědi (v regresi).

## <a name="unfairness-mitigation"></a>Zmírnění nepoctivosti

### <a name="parity-constraints"></a>Omezení parity

Fairlearn zahrnuje řadu nepoctivých algoritmů zmírnění. Tyto algoritmy podporují sadu omezení chování prediktivního volání s názvem **omezení parity** nebo kritéria. Omezení parity vyžadují, aby některé aspekty prediktivního chování byly srovnatelné v rámci skupin, které určují citlivé funkce (např. různé Races). Algoritmy pro zmírnění rizika Fairlearn používají taková omezení parity k zmírnění pozorovaných problémů s spravedlivou škodou.

Fairlearn podporuje následující typy omezení parity:

|Omezení parity  | Účel  |Úloha strojového učení  |
|---------|---------|---------|
|Demografická parita     |  Zmírnění škod přidělení | Binární klasifikace, regrese |
|Equaled lichá  | Diagnostika přidělení a poškození kvality služby | Binární klasifikace        |
|Ztráta ohraničené skupiny     |  Zmírnění škod ve službě kvality | Regrese |

### <a name="mitigation-algorithms"></a>Algoritmy pro zmírnění rizik

Fairlearn poskytuje algoritmy pro zmírnění nepoctivosti postprocessing a omezení:

- Snížení: tyto algoritmy přijímají standardní černou Estimator ML (např. model LightGBM) a generují sadu předaných modelů pomocí sekvence opakovaných datových sad školení. Například uchazeči určitého pohlaví můžou být vážené nebo nižší, aby mohli předávat modely a snižovat rozdíly mezi různými skupinami pohlaví. Uživatelé pak mohou vybrat model, který poskytuje nejlepší kompromis mezi přesností (nebo jinou metrikou výkonu) a neparitou, což obecně musí být založeno na obchodních pravidlech a výpočtech nákladů.  
- Následné zpracování: tyto algoritmy přebírají existující klasifikátor a citlivou funkci jako vstup. Pak odvozují transformaci předpovědi klasifikátoru, aby vynutila zadaná omezení rovnosti. Největší výhodou optimalizace prahové hodnoty je jednoduchost a flexibilita, protože není potřeba přeškolit model. 

| Algoritmus | Popis | Úloha strojového učení | Citlivé funkce | Podporovaná omezení parity | Typ algoritmu |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | Přístup k černému poli, který je popsaný v [rámci snížení úrovně přístupu k korektní klasifikaci](https://arxiv.org/abs/1803.02453) | Binární klasifikace | Kategorické | [Demografická parita](#parity-constraints), [rovná se lichá](#parity-constraints) | Roztříštěn |
| `GridSearch` | Přístup k černému poli, který je popsaný v tématu [snižování přístupu k korektní klasifikaci](https://arxiv.org/abs/1803.02453)| Binární klasifikace | binární | [Demografická parita](#parity-constraints), [rovná se lichá](#parity-constraints) | Roztříštěn |
| `GridSearch` | Přístup k černému poli, který implementuje variantu pro hledání v mřížce s použitím algoritmu pro hranici ohraničené skupiny popsané v [poctivé regresi: kvantitativní definice a algoritmy založené na omezování](https://arxiv.org/abs/1905.12843) | Regrese | binární | [Ztráta ohraničené skupiny](#parity-constraints) | Roztříštěn |
| `ThresholdOptimizer` | Postprocessing algoritmus založený na [rovnosti příležitostí v dohledovém učení](https://arxiv.org/abs/1610.02413). Tento postup přijímá jako vstup existující klasifikátoru a citlivou funkci a odvozuje transformaci monotone předpovědi klasifikátoru k vynutit zadaná omezení parity. | Binární klasifikace | Kategorické | [Demografická parita](#parity-constraints), [rovná se lichá](#parity-constraints) | Následné zpracování |

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak používat různé komponenty, najdete v [úložišti GitHub Fairlearn](https://github.com/fairlearn/fairlearn/) a [ukázkových poznámkových blocích](https://github.com/fairlearn/fairlearn/tree/master/notebooks).
- Přečtěte si informace o zachování ochrany osobních údajů pomocí [rozdílového soukromí a balíčku WhisperNoise](concept-differential-privacy.md).