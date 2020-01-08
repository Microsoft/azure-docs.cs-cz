---
title: Příprava modelu pro nasazení
titleSuffix: ML Studio (classic) - Azure
description: Jak připravit vyškolený model pro nasazení jako webovou službu převodem výukového experimentu Machine Learning Studio (Classic) na prediktivní experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 03/28/2017
ms.openlocfilehash: e397cfaa9ce521ebe3c2f46ef6cc015bff3112f7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454822"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Postup přípravy modelu pro nasazení v Azure Machine Learning Studio (Classic)

Azure Machine Learning Studio (Classic) poskytuje nástroje, které potřebujete k vývoji prediktivních analytických modelů, a pak je zprovoznění nasadit jako webovou službu Azure.

Chcete-li to provést, použijte Studio (Classic) k vytvoření experimentu, který se nazývá *zkušební experiment* – při výuce, hodnocení a úpravách modelu. Jakmile budete spokojeni, budete připravovat modelu pro nasazení na základě vašeho výukového experimentu k převedení *prediktivní experiment* , který je konfigurován ke skóre uživatelská data.

Příklad tohoto procesu můžete zobrazit v [kurzu 1: předpověď úvěrového rizika](tutorial-part1-credit-risk.md).

Tento článek používá podrobné informace o způsobu získá převést školicího experimentu na prediktivní experiment a jak se tento prediktivní experiment nasadit. Pochopením tyto podrobnosti se dozvíte, jak nakonfigurovat nasazeného modelu k němu efektivnější.



## <a name="overview"></a>Přehled 

Proces převedení školicího experimentu na prediktivní experiment zahrnuje tři kroky:

1. Nahraďte strojového učení algoritmu moduly trénovaného modelu.
2. Trim experiment pouze moduly, které jsou potřeba pro vyhodnocení. Výukový experiment obsahuje několik modulů, které jsou nezbytné pro školení, ale nejsou potřebné, jakmile model se trénuje.
3. Definujte, jak váš model přijímá data od uživatele webové služby a jaká data bude vrácen.

> [!TIP]
> Výukový experiment jste obeznámeni s trénování a vyhodnocování modelu pomocí svoje vlastní data. Ale po nasazení uživatele pošle nová data do modelu a vrátí výsledky předpovědí. Tak jak převést výukového experimentu na prediktivní experiment připravit na nasazení, mějte na paměti použití modelu jinými uživateli.
> 
> 

## <a name="set-up-web-service-button"></a>Tlačítko Nastavení webové služby
Po spuštění experimentu (klikněte na tlačítko **spustit** v dolní části na plátno experimentu), klikněte na tlačítko **nastavení webové služby** tlačítko (vyberte **prediktivní webová služba** možnost). **Nastavení webové služby** za vás provádí převod výukového experimentu na prediktivní experiment tři kroky:

1. Uloží trénovaného modelu ve službě **Trénované modely** části palety modulů (nalevo od plátna experimentu). Pak nahrazuje algoritmus strojového učení a moduly [modelu vlaků][train-model] s uloženým školicím modelem.
2. Analyzuje experimentu a odebere modulů, které byly použity jasně pouze pro školení a už nejsou potřeba.
3. Vloží _webové služby vstup_ a _výstup_ moduly do umístění výchozího v experimentu (tyto moduly přijímají a vrací data uživatele).

Například následující experiment trénovat dvěma třídami Posílený rozhodovací strom model s použitím ukázkových dat sčítání:

![Výukového experimentu](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Moduly v tento experiment provádět v podstatě čtyři různé funkce:

![Modul funkce](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Pokud převedete tento výukový experiment prediktivní experiment, některé z těchto modulů již nejsou potřebné nebo teď slouží k jinému účelu:

* **Data** – data v této ukázkové datové sadě se nepoužijí během vyhodnocování – zadání dat zohlednit uživatele webové službě. Metadata z této datové sady, jako jsou datové typy, ale používá trénovaného modelu. Proto je potřeba nechat datovou sadu v prediktivní experiment tak, aby mohl poskytnout tato metadata.

* **Příprava** – v závislosti na tom uživatelská data, která bude odeslána pro vyhodnocení, tyto moduly může nebo nemusí být potřeba zpracovávat příchozí data. **Nastavení webové služby** tlačítko nemá touch tyto – je potřeba rozhodnutí o způsobu jejich zpracování.
  
    Například v tomto příkladu může mít ukázková datová sada chybějící hodnoty, takže modul [Vyčištění chybějících dat][clean-missing-data] byl zahrnut k tomu, aby se s nimi mohla pracovat. Ukázkovou datovou sadou navíc obsahuje sloupce, které nejsou potřebné pro trénování modelu. Proto byl zahrnutý modul [Výběr sloupců v datové sadě][select-columns] , aby vyloučil tyto nadbytečné sloupce z toku dat. Pokud víte, že data, která budou odeslána pro bodování prostřednictvím webové služby, nebudou obsahovat hodnoty, můžete odebrat modul [Vyčištění chybějících dat][clean-missing-data] . Vzhledem k tomu, že modul [Výběr sloupců v datové sadě][select-columns] pomáhá definovat sloupce dat, které vyškolený model očekává, musí tento modul zůstat.

* **Trénování** – tyto moduly se používají pro trénování modelu. Po kliknutí na **nastavení webové služby**, tyto moduly jsou nahrazeny jednoho modulu, který obsahuje model je školení. Tento nový modul se uloží do **Trénované modely** části palety modulů.

* **Skóre** – v tomto příkladu se modul [rozdělení dat][split] používá k rozdělení datového proudu do testovacích dat a dat školení. Ve prediktivním experimentu už nebudeme školením moct odebrat [rozdělená data][split] . Podobně modul s druhým [modelem skóre][score-model] a modul [vyhodnocení modelu][evaluate-model] slouží k porovnání výsledků z testovacích dat, takže tyto moduly nejsou v prediktivním experimentu potřeba. Modul zbývajícího [modelu skóre][score-model] je však potřeba k vrácení výsledku skóre prostřednictvím webové služby.

Zde je, jak náš příklad vypadá po kliknutí na tlačítko **nastavení webové služby**:

![Převést prediktivní experiment](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Práci prováděnou **nastavení webové služby** může být dostatečné k přípravě experiment nasadit jako webovou službu. Však můžete chtít provést další úkony konkrétní do experimentu.

### <a name="adjust-input-and-output-modules"></a>Upravit vstupní a výstupní moduly
V výukového experimentu používá sadu trénovacích dat a pak jste nějaké zpracování pro získání dat ve formě potřeby algoritmu strojového učení. Když data, které by se měl zobrazit prostřednictvím webové služby, nebude nutné toto zpracování, můžete to obejít: propojte výstup modulu **vstupní modul webové služby** do jiného modulu v experimentu. Data uživatele se teď dorazí v modelu v tomto umístění.

Například ve výchozím nastavení **nastavení webové služby** umístí **webové služby vstup** modulu v horní části váš tok dat, jak je znázorněno na obrázku výše. Ale můžete ručně umístit **webové služby vstup** za zpracování dat modulů:

![Přesunutí vstup webové služby](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Vstupní data poskytovaná prostřednictvím webové služby předá nyní přímo do modulu určení skóre modelu bez jakékoli předběžného zpracování.

Podobně, ve výchozím nastavení **nastavení webové služby** umístí modul výstup webové služby v dolní části datový tok. V tomto příkladu se webová služba vrátí uživateli výstup modulu určení [skóre modelu][score-model] , který zahrnuje kompletní vstupní datový vektor a výsledky bodování.
Ale pokud chcete vrátit něco jiné, pak můžete přidat další moduly před **webové služby výstup** modulu. 

Například chcete-li vrátit pouze výsledky bodování a ne celý vektor vstupních dat, přidejte modul [Výběr sloupců v datové sadě][select-columns] , který vyloučí všechny sloupce kromě výsledků bodování. Pak přesuňte modul **výstupu webové služby** na výstup modulu [Výběr sloupců v datové sadě][select-columns] . Experiment vypadá takto:

![Přesunutí výstup webové služby](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Přidat nebo odebrat moduly další zpracování dat
Pokud existují další moduly do experimentu, o kterém víte, že nebude potřeba během vyhodnocování, ty lze odebrat. Protože jsme například přesunuli modul **vstupu webové služby** do bodu za moduly zpracování dat, můžeme z prediktivního experimentu odebrat modul [Vyčištění chybějících dat][clean-missing-data] .

Naše prediktivní experiment teď vypadá takto:

![Odebrání dalších modulů](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Přidání volitelných parametrů webové služby
V některých případech můžete chtít umožnit uživateli webovou službu můžete změnit chování modulů při přístupu k službě. *Webová služba parametry* umožňují udělat.

Běžným příkladem je nastavení modulu [Import dat][import-data] , aby uživatel nasazené webové služby mohl při použití webové služby zadat jiný zdroj dat. Nebo nakonfigurujte modul [exportu dat][export-data] tak, aby bylo možné zadat jiný cíl.

Můžete definovat parametry webové služby a přidružit jeden nebo více parametrů modulu, a můžete určit, jestli jsou povinné nebo volitelné. Když služba přistupuje, a modul akce, které jsou odpovídajícím způsobem změněny uživatelské web service poskytuje hodnoty pro tyto parametry.

Další informace o tom, jaké parametry webové služby jsou a jak je používat, najdete v tématu [použití Azure Machine Learningch parametrů webové služby][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Prediktivní experiment nasadit jako webovou službu
Teď, když prediktivní experiment dostatečně připravený, můžete ho nasadit jako webová služba Azure. Pomocí webové služby, uživatelé můžou posílat data do modelu a vrátí jeho předpovědi modelu.

Další informace o dokončeném procesu nasazení najdete v tématu [nasazení webové služby Azure Machine Learning][deploy] .

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
