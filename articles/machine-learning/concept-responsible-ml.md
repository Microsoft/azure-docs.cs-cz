---
title: Co je zodpovědné Machine Learning (Preview)
titleSuffix: Azure Machine Learning
description: Přečtěte si, co je odpovědný strojové učení a jak ho používat s Azure Machine Learning k pochopení modelů, ochraně dat a řízení životního cyklu modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 02/25/2021
ms.custom: responsible-ml
ms.openlocfilehash: aeff928c14bd61a374f8b2610f35c0347a7cfc39
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062416"
---
# <a name="what-is-responsible-machine-learning-preview"></a>Co je zodpovědný strojové učení? (Preview)

V tomto článku se dozvíte, jaké jsou odpovědné počítačové učení (ML) a způsoby, kterými se dá v praxi Azure Machine Learning.

## <a name="responsible-machine-learning-principles"></a>Příslušné principy strojového učení

V průběhu vývoje a používání systémů AI musí být vztah důvěryhodnosti v jádru. Vztah důvěryhodnosti v rámci platformy, procesu a modelů. V Microsoftu zodpovědné počítačové učení zahrnuje následující hodnoty a principy:

- Principy modelů strojového učení
  - Interpretace a vysvětlení chování modelu
  - Posouzení a zmírnění nepoctivosti modelu
- Ochrana uživatelů a jejich dat
  - Zabránit expozici dat s rozdílovou ochranou soukromí
  - Práce s šifrovanými daty pomocí šifrování homomorphic
- Řízení kompletního procesu strojového učení
  - Zdokumentujte životní cyklus machine learningu pomocí datových listů

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="Odpovědní pilíře ML – výklad, rozdílové soukromí, šifrování homomorphic, Azure Machine Learning pro audit":::

V případě umělých a autonomních systémů, které se integrují do prostředků infrastruktury společnosti, je důležité aktivně proaktivně odhadnout a zmírnit nezamýšlené důsledky těchto technologií.

## <a name="interpret-and-explain-model-behavior"></a>Interpretace a vysvětlení chování modelu

Obtížně vysvětlené nebo neprůhledné systémy mohou být problematické, protože jsou pro účastníky, jako jsou vývojáři systému, regulačních skupin, uživatelé a tvůrci obchodních rozhodnutí, obtížné porozumět tomu, proč systémy provádějí určitá rozhodnutí. Některé systémy AI jsou vysvětlené než jiné a někdy se jedná o kompromis mezi systémem s vyšší přesností a jedním z nich, který je popsán.

K sestavení interpretované systémy AI použijte [InterpretML](https://github.com/interpretml/interpret), což je open source balíček sestavený Microsoftem. Balíček InterpretML podporuje širokou škálu metod interpretace, jako je například SHapley aditivní vysvětlení (SHAP), napodobující vysvětlení a funkci permutace (PFI).  [InterpretML se dají použít v Azure Machine Learning](how-to-machine-learning-interpretability.md) k [interpretaci a vysvětlení modelů strojového učení](how-to-machine-learning-interpretability-aml.md), včetně [automatizovaných modelů strojového učení](how-to-machine-learning-interpretability-automl.md).

## <a name="mitigate-fairness-in-machine-learning-models"></a>Zmírnění rovnosti v modelech strojového učení

Vzhledem k tomu, že se systémy AI stanou více zapojenými do každodenního rozhodování společnosti, je mimořádně důležité, aby tyto systémy dobře fungovaly na zajištění korektních výsledků pro každého.

Nespravedlivost v systémech AI může mít za následek následující nezamýšlené důsledky:

- Odmítnutí příležitostí, prostředků nebo informací od jednotlivců.
- Posílit bias a stereotypy.

Metriky nemůžou zachytit ani znázornit mnoho aspektů nerovnosti. Existují nástroje a postupy, které mohou zlepšit spravedlivost při navrhování a vývoji systémů AI.

Dva klíčové kroky při zmenšování nespravedlivosti v systémech AI jsou posouzení a zmírnění. Doporučujeme [FairLearn](https://github.com/fairlearn/fairlearn), open source balíček, který dokáže vyhodnotit a zmírnit potenciální nepoctivé systémy AI. Další informace o spravedlivém a balíčku FairLearn najdete [v článku o spravedlivosti v ml](./concept-fairness-ml.md).

## <a name="prevent-data-exposure-with-differential-privacy"></a>Zabránit expozici dat s rozdílovou ochranou soukromí

Když se data používají k analýze, je důležité, aby data zůstala soukromá a důvěrná během jejího používání. Rozdílová ochrana osobních údajů je sada systémů a postupů, které pomůžou udržet data jednotlivců v bezpečí a soukromí.

V tradičních scénářích se nezpracovaná data ukládají do souborů a databází. Když uživatelé analyzují data, obvykle používají nezpracovaná data. To je důležité, protože by mohlo dojít k porušení ochrany osobních údajů jednotlivce. Rozdílové soukromí se snaží s tímto problémem zabývat přidáním "šumu" nebo náhodnosti k datům, aby uživatelé nemohli identifikovat jednotlivé datové body.

Implementace rozdílových privátních systémů je obtížná. [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core) je open source projekt, který obsahuje různé komponenty pro vytváření globálních rozdílných privátních systémů. Další informace o rozdílových zásadách ochrany osobních údajů a projektu SmartNoise najdete v článku [zachování ochrany osobních údajů pomocí rozdílového soukromí a SmartNoise](./concept-differential-privacy.md) .

## <a name="work-on-encrypted-data-with-homomorphic-encryption"></a>Práce s šifrovanými daty pomocí šifrování homomorphic

V tradičním cloudovém úložišti a výpočtovém řešení potřebuje Cloud mít nešifrovaný přístup k zákaznickým datům, která se na něj budou počítat. Tento přístup zpřístupňuje data cloudovým operátorům. Ochrana osobních údajů v datech se spoléhá na zásady řízení přístupu implementované cloudem a důvěryhodným zákazníkem.

Homomorphic šifrování umožňuje provádět výpočty na šifrovaných datech bez nutnosti přístupu ke tajnému (dešifrovacímu) klíči. Výsledky výpočtů jsou zašifrované a dají se odhalet jenom vlastníkem tajného klíče. Při použití šifrování homomorphic nemají operátoři cloudu nikdy zašifrovaný přístup k datům, která ukládá a výpočetní výkon. Výpočty se provádějí přímo v šifrovaných datech. Ochrana osobních údajů dat se spoléhá na špičkovou kryptografii a vlastník dat kontroluje všechny verze informací. Další informace o šifrování homomorphic v Microsoftu najdete v tématu [Microsoft Research](https://www.microsoft.com/research/project/homomorphic-encryption/).

Pokud chcete začít se šifrováním homomorphic v Azure Machine Learning, použijte k [Microsoft zapečetit](https://github.com/microsoft/SEAL)vazby Pythonu, která je [zašifrovaná](https://pypi.org/project/encrypted-inference/) . Microsoft SEAL je open source knihovna šifrování homomorphic, která umožňuje provádět přidávání a násobení u šifrovaných celých čísel nebo reálných čísel. Další informace o Microsoft SEAL najdete na stránce [cetrum architektury Azure](/azure/architecture/solution-ideas/articles/homomorphic-encryption-seal) nebo [projektu Microsoft Research](https://www.microsoft.com/research/project/microsoft-seal/).

V následující ukázce se dozvíte, [Jak nasadit šifrovanou webovou službu Inferencing v Azure Machine Learning](how-to-homomorphic-encryption-seal.md).

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>Zdokumentujte životní cyklus machine learningu pomocí datových listů

Dokumentování správných informací v procesu strojového učení je klíčem k rozhodování o příslušných rozhodnutích v jednotlivých fázích. Datové listy představují způsob, jak zdokumentovat assety strojového učení, které se používají a vytváří v rámci životního cyklu machine learningu.

Modely se obvykle považují za "neprůhledná pole" a často jsou k dispozici pouze malé informace. Vzhledem k tomu, že systémy strojového učení se stávají větší Pervasive a používají se k rozhodování, je použití datových listů krok k vývoji dalších zodpovědných systémů strojového učení.

Některé informace o modelu, které byste mohli chtít dokumentovat jako součást datového listu:

- Zamýšlené použití
- Architektura modelu
- Použitá data školení
- Použitá data vyhodnocení
- Metriky výkonu modelu školení
- Informace o spravedlnosti

V následující ukázce se dozvíte, jak použít sadu SDK Azure Machine Learning k implementaci [datových listů pro modely](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb).

## <a name="additional-resources"></a>Další zdroje informací

- Další informace o osvědčených postupech najdete v [příslušné sadě nástrojů pro inovace](/azure/architecture/guide/responsible-innovation/) .
- Přečtěte si další informace o pokynech pro dokumentaci k systému Machine Learning [o ml](https://www.partnershiponai.org/about-ml/) .