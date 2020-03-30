---
title: Příprava modelu pro nasazení
titleSuffix: ML Studio (classic) - Azure
description: Jak připravit trénovaný model pro nasazení jako webovou službu převedením vašeho machine learningového studia (klasického) trénovacího experimentu na prediktivní experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/28/2017
ms.openlocfilehash: 061c340f8c4952d5a0f2a3873f7475e4f733c290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204507"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Jak připravit model pro nasazení v Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (classic) vám poskytuje nástroje, které potřebujete k vývoji prediktivního analytického modelu a jeho následnému zprovoznění jeho nasazením jako webové služby Azure.

Chcete-li to provést, použijte Studio (klasické) k vytvoření experimentu - nazývaného *tréninkový experiment* - kde trénujete, skóre a upravujete model. Jakmile budete spokojeni, připravíte model k nasazení převedením trénovacího experimentu na *prediktivní experiment,* který je nakonfigurovaný tak, aby získával uživatelská data.

Příklad tohoto procesu můžete vidět v [kurzu 1: Předvídání úvěrového rizika](tutorial-part1-credit-risk.md).

Tento článek se podrobně zabývá podrobnostmi o tom, jak se tréninkový experiment převede na prediktivní experiment a jak se tento prediktivní experiment nasadí. Pochopením těchto podrobností se dozvíte, jak nakonfigurovat nasazený model tak, aby byl efektivnější.



## <a name="overview"></a>Přehled 

Proces převodu trénovacího experimentu na prediktivní experiment zahrnuje tři kroky:

1. Nahraďte moduly algoritmů strojového učení trénovaným modelem.
2. Ořízněte experiment pouze na moduly, které jsou potřebné pro vyhodnocování. Trénovací experiment obsahuje řadu modulů, které jsou nezbytné pro školení, ale nejsou potřeba po trénování modelu.
3. Definujte, jak bude model přijímat data od uživatele webové služby a jaká data budou vrácena.

> [!TIP]
> V tréninkovém experimentu jste se zabývali trénovaním a hodnocením modelu pomocí vlastních dat. Ale po nasazení, uživatelé odešlou nová data do modelu a vrátí výsledky předpovědi. Takže při převodu tréninkového experimentu na prediktivní experiment, abyste jej připravili k nasazení, mějte na paměti, jak bude model používán ostatními uživateli.
> 
> 

## <a name="set-up-web-service-button"></a>Tlačítko Nastavit webovou službu
Po spuštění experimentu **(v** dolní části plátna experimentu klepněte na tlačítko Nastavit webovou službu ) klepněte na tlačítko **Nastavit webovou službu** (vyberte možnost **Prediktivní webová služba).** **Služba Set Up Web Service** provádí tři kroky převodu trénovacího experimentu na prediktivní experiment:

1. Uloží trénovaný model v části **Trénované modely** palety modulů (vlevo od plátna experimentu). Potom nahradí algoritmus strojového učení a [moduly modelu train][train-model] uloženým trénovaným modelem.
2. Analyzuje váš experiment a odstraňuje moduly, které byly jasně použity pouze pro školení a již nejsou potřeba.
3. Vloží vstupní a _výstupní_ moduly _webové služby_ do výchozích umístění v experimentu (tyto moduly přijímají a vracejí uživatelská data).

Například následující experiment trénuje dvoutřídový model rozhodovacího stromu pomocí dat ukázkového sčítání:

![Tréninkový experiment](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Moduly v tomto experimentu vykonávají v podstatě čtyři různé funkce:

![Funkce modulu](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Když převedete tento tréninkový experiment na prediktivní experiment, některé z těchto modulů již nejsou potřeba nebo nyní slouží jinému účelu:

* **Data** – data v této ukázkové datové sadě se nepoužívají během vyhodnocování – uživatel webové služby poskytne data, která mají být hodnocena. Metadata z této datové sady, jako jsou například datové typy, je však používán trénovaný model. Takže je třeba zachovat datovou sadu v prediktivní experiment, aby mohla poskytnout tato metadata.

* **Příprava** – v závislosti na uživatelských datech, která budou odeslána ke vyhodnocování, mohou být tyto moduly nezbytné pro zpracování příchozích dat. Tlačítko **Nastavit webovou službu** se jich nedotýká – musíte se rozhodnout, jak s nimi chcete zacházet.
  
    Například v tomto příkladu ukázkové datové sady může mít chybějící hodnoty, takže [clean missing data][clean-missing-data] modul byl zahrnut k jejich řešení. Ukázková datová sada také obsahuje sloupce, které nejsou potřebné k trénování modelu. Takže [select columns in Dataset][select-columns] modul byl zahrnut vyloučit tyto další sloupce z toku dat. Pokud víte, že data, která budou odeslána pro vyhodnocování prostřednictvím webové služby nebude mít chybějící hodnoty, pak můžete odebrat modul [Vyčistit chybějící data.][clean-missing-data] Však vzhledem k tomu, [že vybrat sloupce v datové sadě][select-columns] modulu pomáhá definovat sloupce dat, které trénovaný model očekává, že modul musí zůstat.

* **Vlak** - Tyto moduly se používají k trénování modelu. Po klepnutí na tlačítko **Nastavit webovou službu**budou tyto moduly nahrazeny jedním modulem obsahujícím trénovaný model. Tento nový modul je uložen v části **Trénované modely** palety modulů.

* **Skóre** – V tomto příkladu se modul [Split Data][split] používá k rozdělení datového proudu na testovací data a trénovací data. V prediktivním experimentu už netrénujeme, takže [split data][split] mohou být odebrána. Podobně druhý modul [modelu skóre][score-model] a modul [Vyhodnotit model][evaluate-model] se používají k porovnání výsledků z testovacích dat, takže tyto moduly nejsou potřebné v prediktivníexperiment. Zbývající [modul modelu skóre][score-model] je však potřeba k vrácení výsledku skóre prostřednictvím webové služby.

Zde je, jak náš příklad vypadá po kliknutí **nastavit webovou službu**:

![Převedený prediktivní experiment](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Práce **vykonaná službou Set Up Web Service** může být dostatečná k přípravě experimentu k nasazení jako webové služby. Můžete však chtít provést další práci specifickou pro váš experiment.

### <a name="adjust-input-and-output-modules"></a>Nastavení vstupních a výstupních modulů
V trénovacím experimentu jste použili sadu trénovacích dat a potom jste provedli určité zpracování, abyste získali data ve formě, kterou algoritmus strojového učení potřeboval. Pokud data, která očekáváte, že obdržíte prostřednictvím webové služby, nebudou toto zpracování vyžadovat, můžete je obejít: připojit výstup **vstupního modulu webové služby** k jinému modulu v experimentu. Data uživatele nyní dorazí v modelu v tomto umístění.

Ve výchozím nastavení například umístí **vstupní** modul webové **služby** na začátek toku dat, jak je znázorněno na obrázku výše. Ale můžeme ručně umístit **vstup webové služby** kolem modulů zpracování dat:

![Přesunutí vstupu webové služby](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Vstupní data poskytnutá prostřednictvím webové služby budou nyní přecházet přímo do modulu Score Model bez jakéhokoli předběžného zpracování.

Podobně ve výchozím nastavení **nastavit webovou službu** umístí výstupní modul webové služby v dolní části toku dat. V tomto příkladu vrátí webová služba uživateli výstup modulu [Score Model,][score-model] který obsahuje vektor úplných vstupních dat a výsledky hodnocení.
Pokud však chcete vrátit něco jiného, můžete před výstupní modul **webové služby** přidat další moduly. 

Chcete-li například vrátit pouze výsledky hodnocení a nikoli celý vektor vstupních dat, přidejte modul [Vybrat sloupce v datové sadě,][select-columns] který vyloučí všechny sloupce kromě výsledků hodnocení. Potom přesuňte výstupní modul **webové služby** na výstup modulu [Vybrat sloupce v datové sadě.][select-columns] Experiment vypadá takto:

![Přesunutí výstupu webové služby](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Přidání nebo odebrání dalších modulů pro zpracování dat
Pokud existuje více modulů v experimentu, který víte, že nebude potřeba během bodování, tyto mohou být odstraněny. Například protože jsme přesunuli vstupní modul **webové služby** do bodu po modulech zpracování dat, můžeme odebrat modul [Vyčistit chybějící data][clean-missing-data] z prediktivního experimentu.

Náš prediktivní experiment nyní vypadá takto:

![Odebrání dalšího modulu](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Přidání volitelných parametrů webové služby
V některých případech můžete povolit uživateli webové služby změnit chování modulů při přístupu ke službě. *Parametry webové služby* umožňují provést.

Běžným příkladem je nastavení modulu [importu dat,][import-data] aby uživatel nasazené webové služby mohl při přístupu k webové službě zadat jiný zdroj dat. Nebo konfigurace modulu [Export dat][export-data] tak, aby bylo možné zadat jiný cíl.

Můžete definovat parametry webové služby a přidružit je k jednomu nebo více parametrům modulu a můžete určit, zda jsou povinné nebo volitelné. Uživatel webové služby poskytuje hodnoty pro tyto parametry při přístupu ke službě a akce modulu jsou odpovídajícím způsobem upraveny.

Další informace o parametrech webové služby a jejich použití najdete v tématu [Using Azure Machine Learning Web Service Parameters][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Nasazení prediktivního experimentu jako webové služby
Teď, když prediktivní experiment byl dostatečně připraven, můžete jej nasadit jako webovou službu Azure. Pomocí webové služby mohou uživatelé odesílat data do modelu a model vrátí své předpovědi.

Další informace o procesu úplného nasazení najdete v [tématu Nasazení webové služby Azure Machine Learning.][deploy]

[deploy]: deploy-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
