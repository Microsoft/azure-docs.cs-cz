---
title: Zodpovědné Machine Learning (ML)
titleSuffix: Azure Machine Learning
description: Seznamte se s tím, co je odpovědné ML a jak ho používat v Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/08/2020
ms.openlocfilehash: 17cbf4f7b0ae31b0523a0fbea0c1cae8f83b234b
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2020
ms.locfileid: "84982590"
---
# <a name="responsible-machine-learning-ml"></a>Zodpovědné Machine Learning (ML)

V tomto článku se dozvíte, co je odpovědná ML, a způsoby, kterými se dá dát do praxe Azure Machine Learning.

V průběhu vývoje a používání systémů AI musí být vztah důvěryhodnosti v jádru. Vztah důvěryhodnosti v rámci platformy, procesu a modelů. V Microsoftu, který odpovídá na ML, zahrnuje následující hodnoty a principy:

- Principy modelů strojového učení
  - Interpretace a vysvětlení chování modelu
  - Posouzení a zmírnění nepoctivosti modelu
- Ochrana uživatelů a jejich dat
  - Zabránit expozici dat s rozdílovou ochranou soukromí  
- Řízení kompletního procesu strojového učení
  - Zdokumentujte životní cyklus machine learningu pomocí datových listů

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Zodpovědné pilíře ML":::

V případě umělých a autonomních systémů, které se integrují do prostředků infrastruktury společnosti, je důležité aktivně proaktivně odhadnout a zmírnit nezamýšlené důsledky těchto technologií.

## <a name="interpret-and-explain-model-behavior"></a>Interpretace a vysvětlení chování modelu

Obtížně vysvětlené nebo černé systémy mohou být problematické, protože jsou pro účastníky, jako jsou vývojáři systému, regulačních skupin, uživatelé a tvůrci obchodních rozhodnutí, obtížné pochopit, proč systémy provádějí určitá rozhodnutí. Některé systémy AI jsou vysvětlené než jiné a někdy se jedná o kompromis mezi systémem s vyšší přesností a jedním z nich, který je popsán.

K sestavení interpretované systémy AI použijte [InterpretML](https://github.com/interpretml/interpret), což je open source balíček sestavený Microsoftem. [InterpretML se dají použít v Azure Machine Learning](how-to-machine-learning-interpretability.md) k [interpretaci a vysvětlení modelů strojového učení](how-to-machine-learning-interpretability-aml.md), včetně [automatizovaných modelů strojového učení](how-to-machine-learning-interpretability-automl.md).

## <a name="assess-and-mitigate-model-unfairness"></a>Posouzení a zmírnění nepoctivosti modelu

Vzhledem k tomu, že se systémy AI stanou více zapojenými do každodenního rozhodování společnosti, je mimořádně důležité, aby tyto systémy dobře fungovaly na zajištění korektních výsledků pro každého.

Nespravedlivost v systémech AI může mít za následek následující nezamýšlené důsledky:

- Odmítnutí příležitostí, prostředků nebo informací od jednotlivců.
- Posílit bias a stereotypy.

Metriky nemůžou zachytit ani znázornit mnoho aspektů nerovnosti. Existují nástroje a postupy, které mohou zlepšit spravedlivost při navrhování a vývoji systémů AI.

Dva klíčové kroky při zmenšování nespravedlivosti v systémech AI jsou posouzení a zmírnění. Doporučujeme [FairLearn](https://github.com/fairlearn/fairlearn), open source balíček, který dokáže vyhodnotit a zmírnit potenciální nepoctivé systémy AI. Další informace o spravedlivém a balíčku FairLearn najdete [v článku o spravedlivosti v ml](./concept-fairness-ml.md).

## <a name="prevent-data-exposure-with-differential-privacy"></a>Zabránit expozici dat s rozdílovou ochranou soukromí

Když se data používají k analýze, je důležité, aby data zůstala soukromá a důvěrná během jejího používání. Rozdílová ochrana osobních údajů je sada systémů a postupů, které pomůžou udržet data jednotlivců v bezpečí a soukromí.

V tradičních scénářích se nezpracovaná data ukládají do souborů a databází. Když uživatelé analyzují data, obvykle používají nezpracovaná data. To je důležité, protože by mohlo dojít k porušení ochrany osobních údajů jednotlivce. Rozdílové soukromí se snaží s tímto problémem zabývat přidáním "šumu" nebo náhodnosti k datům, aby uživatelé nemohli identifikovat jednotlivé datové body.

Implementace rozdílových privátních systémů je obtížná. [WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-core) je open source projekt, který obsahuje různé komponenty pro vytváření globálních rozdílných privátních systémů. Další informace o rozdílových zásadách ochrany osobních údajů a projektu WhiteNoise najdete v článku [zachování ochrany osobních údajů pomocí rozdílového soukromí a WhiteNoise](./concept-differential-privacy.md) .

> [!NOTE]
> Všimněte si, že přejmenováváme sadu nástrojů a zavádíme nové jméno do nadcházejících týdnů. 

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>Zdokumentujte životní cyklus machine learningu pomocí datových listů

Dokumentování správných informací v procesu strojového učení je klíčem k rozhodování o příslušných rozhodnutích v jednotlivých fázích. Datové listy představují způsob, jak zdokumentovat assety strojového učení, které se používají a vytváří v rámci životního cyklu machine learningu.

Modely se obvykle považují za "černé čtverečky" a často jsou k dispozici jen malé informace. Vzhledem k tomu, že systémy strojového učení se stávají větší Pervasive a používají se k rozhodování, je použití datových listů krok k vývoji dalších zodpovědných systémů strojového učení.

Některé informace o modelu, které byste mohli chtít dokumentovat jako součást datového listu:

- Zamýšlené použití
- Architektura modelu
- Použitá data školení
- Použitá data vyhodnocení
- Metriky výkonu modelu školení
- Informace o spravedlnosti

V následující ukázce se dozvíte, jak použít sadu SDK Azure Machine Learning k implementaci [datových listů pro modely](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb).

## <a name="additional-resources"></a>Další zdroje

- K [nasazení šifrované webové služby Inferencing](how-to-homomorphic-encryption-seal.md)použijte šifrování homomorphic.
- Přečtěte si další informace o pokynech pro dokumentaci k systému Machine Learning [o ml](https://www.partnershiponai.org/about-ml/) .