---
title: Nespravedlivá verze machine learningu (Preview)
titleSuffix: Azure Machine Learning
description: Přečtěte si o tom, jak se dá v rámci strojového učení najímat a jak může balíček Pythonu Fairlearn posuzovat a zmírnit nespravedlivost.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 04/02/2021
ms.custom: responsible-ml
ms.openlocfilehash: 45c63780eac021301e5b01c0ff8abea7d704a4cf
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220517"
---
# <a name="machine-learning-fairness-preview"></a>Nespravedlivá verze machine learningu (Preview)

Přečtěte si informace o tom, jak se v strojovém učení přizpůsobuje a jak [Fairlearn](https://fairlearn.github.io/) Open Source balíčku Python vám může posuzovat a zmírnit problémy s nepoctivou podporou v modelech Machine Learning. 

## <a name="what-is-machine-learning-fairness"></a>Co je to u strojového učení nespravedlivé?

Umělá logika a systémy strojového učení můžou zobrazovat nerovné chování. Jedním ze způsobů, jak definovat nerovné chování, je poškození nebo dopad na lidi. Existuje mnoho typů poškození, které mohou systémy AI vyvolat. Další informace najdete v článku [NeurIPS 2017 vystoupení od Kate Crawford](https://www.youtube.com/watch?v=fMym_BKWQzk) .

Existují dva běžné typy AI, které způsobují poškození:

- Poškození přidělení: systém AI rozšiřuje nebo odpírá příležitosti, prostředky nebo informace pro určité skupiny. Příklady zahrnují zařazení, školní přístup a půjčování, kde model může být mnohem lepší při vybírání kvalitních kandidátů mezi konkrétní skupinou lidí, než mezi ostatními skupinami.

- Poškození kvality služby: systém AI nefunguje stejně jako jedna skupina lidí, protože to pro jiný má. V takovém případě může systém pro rozpoznávání hlasu nefungovat i u žen, jak to dělá pro muže.

Chcete-li omezit nerovné chování v systémech AI, je nutné tyto škody vyhodnotit a zmírnit.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Posouzení a zmírnění spravedlnosti pomocí Fairlearn

Fairlearn je open source balíček Pythonu, který vývojářům v systémech Machine Learning umožňuje vyhodnotit jeho spravedlivost a zmírnit nespravedlivost.

>[!NOTE]
> Spravedlivé je společenská a technická výzva. V kvantitativních metrikách spravedlivosti se nezachycují spousta aspektů spravedlnosti, jako je Soudní dvůr a řádný proces. Řadu kvantitativních metrik pro spravedlivost se navíc nedají splnit současně. Cílem s Fairlearn Open Source balíčkem je umožnit člověkům vyhodnotit různé strategie pro dopad na dopad a omezení rizik. V konečném případě se jedná o lidské uživatele, kteří vytvářejí umělá inteligentní funkce a modely strojového učení, které jsou vhodné pro jejich scénář.

Otevřený zdrojový balíček Fairlearn má dvě komponenty:

- Řídicí panel posouzení: pomůcka poznámkového bloku Jupyter pro vyhodnocení způsobu, jakým předpovědi modelu ovlivňuje různé skupiny. Umožňuje také porovnání více modelů pomocí měření vlivu a metriky výkonu.
- Algoritmy zmírnění: Sada algoritmů pro zmírnění nespravedlivosti v binární klasifikaci a regresi.

Společně tyto komponenty umožňují odborníkům přes data a vedoucím pracovníkům proměňovat jakékoli kompromisy týkající se spravedlnosti a výkonu a vybrat strategii pro zmírnění rizik, která nejlépe vyhovuje jejich potřebám.

## <a name="assess-fairness-in-machine-learning-models"></a>Posouzení spravedlivosti v modelech strojového učení

V Fairlearnm Open Source balíčku se spravedlivě rozkládá i v případě, že se jedná o přizpůsobenou **skupinu**, na které se požádá: které skupiny jednotlivců jsou ohrožené poškozením? Příslušné skupiny, označované také jako podmnožiny, jsou definovány prostřednictvím **citlivých funkcí** nebo citlivých atributů. Citlivé funkce jsou předány do Estimator v Fairlearn Open Source balíčku jako vektor nebo matice s názvem  `sensitive_features` . Pojem naznačuje, že při posuzování spravedlnosti skupin by měl být System Designer citlivý na tyto funkce. 

Je třeba mít na vědomí, zda tyto funkce obsahují dopad na ochranu osobních údajů z důvodu soukromých dat. Ale slovo "citlivé" neznamená, že tyto funkce by neměly být použity pro předpovědi.

>[!NOTE]
> Posouzení spravedlivosti není čistě technické cvičení.  Fairlearn Open Source balíček vám pomůže vyhodnotit spravedlivosti modelu, ale neprovede posouzení za vás.  Fairlearn Open Source balíček pomáhá identifikovat kvantitativní metriky k posouzení spravedlivosti, ale vývojáři musí také provést kvalitativní analýzu, aby vyhodnotila spravedlivé jejich vlastních modelů.  Citlivé funkce uvedené výše jsou příkladem tohoto druhu kvalitativní analýzy.     

Během fáze hodnocení se spravedlivě kvantifikuje prostřednictvím metrik neparitních hodnot. **Metriky neparity** můžou vyhodnotit a porovnat chování modelu v různých skupinách buď jako poměry, nebo jako rozdíly. Fairlearn Open Source balíček podporuje dvě třídy neparitních metrik:


- Neparitný výkon modelu: tyto sady metrik vypočítávají neparitní (rozdílové) v hodnotách vybrané metriky výkonu v různých podskupinách. Možné příklady:

  - Míra přesnosti pro neparitu
  - míra nerovnosti v chybových sazbách
  - neparitní v přesnosti
  - neparitní při odvolání
  - neparitní v MAE
  - mnoho dalších

- Míra nekonzistence pro výběr: Tato metrika obsahuje rozdíl mezi různými podskupinami v sazbě výběru. Příkladem je neparita v míře schvalování půjček. Míra výběru znamená zlomky datapoints v každé třídě klasifikované jako 1 (v binární klasifikaci) nebo distribuci hodnot předpovědi (v regresi).

## <a name="mitigate-unfairness-in-machine-learning-models"></a>Zmírnění nespravedlivosti v modelech strojového učení

### <a name="parity-constraints"></a>Omezení parity

Fairlearn Open Source balíček zahrnuje řadu nepoctivých algoritmů pro zmírnění rizik. Tyto algoritmy podporují sadu omezení chování prediktivního volání s názvem **omezení parity** nebo kritéria. Omezení parity vyžadují, aby některé aspekty prediktivního chování byly srovnatelné v rámci skupin, které určují citlivé funkce (např. různé Races). Algoritmy zmírnění v Fairlearn Open Source balíčku používají taková omezení parity ke zmírnění pozorovaných problémů s rovností.

>[!NOTE]
> Zmírnění nespravedlivosti v modelu znamená, že je snížení nepoctivosti, ale toto technické zmírnění nemůže tuto nespravedlivost zcela eliminovat.  Algoritmy zmírnění nepoctivosti v Fairlearn Open Source balíčku můžou poskytovat navržené strategie zmírnění, které pomáhají snižovat nespravedlivost v modelu strojového učení, ale nejedná se o řešení, která úplně eliminují nespravedlivost.  Můžou existovat další omezení parity nebo kritéria, která byste měli zvážit pro každý konkrétní model strojového učení pro vývojáře. Vývojáři, kteří používají Azure Machine Learning, musí určit pro sebe sama v případě, že zmírňující rizika dostatečně eliminuje nespravedlivost v zamýšleném použití a nasazení modelů strojového učení.  

Fairlearn Open Source balíček podporuje následující typy omezení parity: 

|Omezení parity  | Účel  |Úloha strojového učení  |
|---------|---------|---------|
|Demografická parita     |  Zmírnění škod přidělení | Binární klasifikace, regrese |
|Equaled lichá  | Diagnostika přidělení a poškození kvality služby | Binární klasifikace        |
|Rovnost příležitostí | Diagnostika přidělení a poškození kvality služby | Binární klasifikace        |
|Ztráta ohraničené skupiny     |  Zmírnění škod ve službě kvality | Regrese |

### <a name="mitigation-algorithms"></a>Algoritmy pro zmírnění rizik

Fairlearn Open Source balíček poskytuje postprocessing a redukční algoritmy pro zmírnění nepoctivosti:

- Snížení: tyto algoritmy přijímají standardní černý krabicový Estimator strojového učení (např. model LightGBM) a generují sadu předaných modelů pomocí sekvence převážených datových sad. Například uchazeči určitého pohlaví můžou být vážené nebo nižší, aby mohli předávat modely a snižovat rozdíly mezi různými skupinami pohlaví. Uživatelé pak mohou vybrat model, který poskytuje nejlepší kompromis mezi přesností (nebo jinou metrikou výkonu) a neparitou, což obecně musí být založeno na obchodních pravidlech a výpočtech nákladů.  
- Následné zpracování: tyto algoritmy přebírají existující klasifikátor a citlivou funkci jako vstup. Pak odvozují transformaci předpovědi klasifikátoru, aby vynutila zadaná omezení rovnosti. Největší výhodou optimalizace prahové hodnoty je jednoduchost a flexibilita, protože není potřeba přeškolit model. 

| Algoritmus | Description | Úloha strojového učení | Citlivé funkce | Podporovaná omezení parity | Typ algoritmu |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | Přístup k černému poli, který je popsaný v [rámci snížení úrovně přístupu k korektní klasifikaci](https://arxiv.org/abs/1803.02453) | Binární klasifikace | Kategorické | [Demografická parita](#parity-constraints), [rovná se lichá](#parity-constraints) | Roztříštěn |
| `GridSearch` | Přístup k černému poli, který je popsaný v tématu [snižování přístupu k korektní klasifikaci](https://arxiv.org/abs/1803.02453)| Binární klasifikace | Binární | [Demografická parita](#parity-constraints), [rovná se lichá](#parity-constraints) | Roztříštěn |
| `GridSearch` | Přístup k černému poli, který implementuje variantu pro hledání v mřížce s použitím algoritmu pro hranici ohraničené skupiny popsané v [poctivé regresi: kvantitativní definice a algoritmy založené na omezování](https://arxiv.org/abs/1905.12843) | Regrese | Binární | [Ztráta ohraničené skupiny](#parity-constraints) | Roztříštěn |
| `ThresholdOptimizer` | Postprocessing algoritmus založený na [rovnosti příležitostí v dohledovém učení](https://arxiv.org/abs/1610.02413). Tento postup přijímá jako vstup existující klasifikátoru a citlivou funkci a odvozuje transformaci monotone předpovědi klasifikátoru k vynutit zadaná omezení parity. | Binární klasifikace | Kategorické | [Demografická parita](#parity-constraints), [rovná se lichá](#parity-constraints) | Následné zpracování |

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak používat různé komponenty, na [GitHubu](https://github.com/fairlearn/fairlearn/), [uživatelské příručce](https://fairlearn.github.io/main/user_guide/index.html), [Příklady](https://fairlearn.github.io/main/auto_examples/index.html)a [ukázkové poznámkové bloky](https://github.com/fairlearn/fairlearn/tree/master/notebooks)pro Fairlearn.
- Naučte [se, jak](how-to-machine-learning-fairness-aml.md) v Azure Machine Learning povolit posouzení spravedlnosti pro modely strojového učení.
- V [ukázkových poznámkových blocích](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) najdete další scénáře posouzení spravedlivosti v Azure Machine Learning. 
