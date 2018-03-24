---
title: Postup přípravy modelu pro nasazení v nástroji Azure Machine Learning Studio | Microsoft Docs
description: Postup přípravy trained model nasazení jako webovou službu převedením experimentu Machine Learning Studio školení na prediktivní experiment.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: eb943c45-541a-401d-844a-c3337de82da6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: 45b8e8812c2c63d60ccdb143656cca9f0dade183
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio"></a>Postup přípravy modelu pro nasazení v nástroji Azure Machine Learning Studio

Azure Machine Learning Studio poskytuje nástroje, které potřebujete k vývoji modelu prediktivní analýzy a zprovoznit ho pomocí jeho nasazení jako Azure webové služby.

K tomuto účelu použijete k vytvoření experimentu - názvem Studio *výukový experiment* – kde jste trénování, stanovení skóre a upravovat modelu. Jakmile budete spokojeni, můžete připravit váš model nasazení na základě převedení experimentu školení pro *prediktivní experiment* nakonfigurovaný skóre uživatelská data.

Můžete zobrazit příklad tohoto procesu v [návod: vývoj řešení prediktivní analýzy pro posuzování úvěrového rizika v Azure Machine Learning](walkthrough-develop-predictive-solution.md).

Tento článek přebírá podrobné informace na podrobné informace o tom, jak získá výukový experiment převeden do prediktivní experiment a nasazení této prediktivní experiment. Pochopením tyto podrobnosti můžete zjistěte, jak nakonfigurovat vaše nasazené model, aby efektivnější.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Přehled 

Proces převod výukový experiment prediktivní experiment zahrnuje tři kroky:

1. Nahraďte strojového učení algoritmu modulů s trained model.
2. Trim experimentu pouze moduly, které jsou potřebné pro vyhodnocování. Výukový experiment obsahuje několik modulů, které jsou nezbytné, aby školení ale nejsou potřebné, jakmile je cvičení modelu.
3. Definujte, jak váš model přijímá data od uživatele webové služby, a jaká data budou vráceny.

> [!TIP]
> V experimentu školení jste byla nevadí, školení a vyhodnocování model pomocí svoje vlastní data. Ale po nasazení uživatele bude posílat nová data do modelu a vrátí výsledky předpovědi. Ano jak převedete výukový experiment prediktivní experiment a připravit ji pro nasazení, mějte na paměti použití modelu třetími stranami.
> 
> 

## <a name="set-up-web-service-button"></a>Tlačítko nastavit webové služby
Po spuštění experimentu (klikněte na tlačítko **spustit** v dolní části plátna experimentu), klikněte na tlačítko **nastavit webové služby** tlačítko (vyberte **webové služby prediktivní** možnost). **Nastavení webové služby** pro vás provede převod výukový experiment prediktivní experiment tři kroky:

1. Ukládá vaše trained model v **Trénované modely** části palety modulů (nalevo od plátna experimentu). Nahradí algoritmu strojového učení a [Train Model] [ train-model] modulů s uložené naučeného modelu.
2. Ho analyzuje experimentu a odebere moduly, které byly jasně používají pouze pro školení a už nejsou potřeba.
3. Vloží _webové služby vstup_ a _výstup_ moduly do výchozího umístění v experimentu (tyto moduly přijmout a vrátit data uživatele).

Například následující experimentu soupravy two-class boosted decision stromu modelu pomocí ukázkových dat úplné zjišťování:

![Výukový experiment][figure1]

Modulů do tohoto experimentu proveďte v podstatě čtyři různé funkce:

![Funkce modulu][figure2]

Pokud převedete tento výukový experiment prediktivní experiment, některé z těchto modulů už nejsou potřeba nebo se teď mají různé účely:

* **Data** -data v této datové sadě ukázka nepoužívá během vyhodnocování – uživatel webové služby se zadat data, která mají být vypočítat skóre. Metadata z této datové sady, jako je například datové typy, ale používá pro cvičný model. Proto je třeba zachovat datovou sadu v prediktivní experiment tak, že může poskytovat tato metadata.

* **Příprava** – v závislosti na tom uživatelská data, která bude odeslána pro vyhodnocování, tyto moduly může nebo nemusí být potřeba zpracovat příchozí data. **Nastavit webové služby** tlačítko nemá touch tyto – musíte se rozhodnout, jak chcete mohli je zpracovat.
  
    Například v tomto příkladu ukázkovou datovou sadu může mít chybějící hodnoty, takže [vyčištění chybějících dat] [ clean-missing-data] modul byl zahrnut jejich odstranění. Také obsahuje ukázkovou datovou sadu sloupců, které nejsou potřebné pro trénování modelu. Proto [výběr sloupců v datové sadě] [ select-columns] modul byl zahrnut vyloučit tyto další sloupce z datového toku. Pokud víte, že data, která bude odeslána pro vyhodnocování prostřednictvím webové služby nebude mít chybějící hodnoty a pak můžete odebrat [vyčištění chybějících dat] [ clean-missing-data] modulu. Ale protože [výběr sloupců v datové sadě] [ select-columns] modulu pomáhá definovat sloupce dat, které pro cvičný model očekává, že modul musí zůstat.

* **Cvičení** -tyto moduly jsou použity při cvičení modelu. Když kliknete na tlačítko **nastavit webové služby**, nahradí tyto moduly se jeden modul, který obsahuje můžete cvičení modelu. Tento nový modul se uloží do **Trénované modely** části palety modulů.

* **Skóre** – v tomto příkladu [rozdělení dat] [ split] modul se používá k rozdělení datový proud do testovací data a Cvičná data. V prediktivní experiment jsme nejsou cvičení už nepotřebujeme, takže [rozdělení dat] [ split] lze odebrat. Podobně, druhý [Score Model] [ score-model] modulu a [Evaluate Model] [ evaluate-model] modul se používá k porovnání výsledků v testovacích datech, takže tyto moduly nejsou potřebné v prediktivní experiment. Zbývající [Score Model] [ score-model] , ale není potřeba modul vrácení výsledku skóre prostřednictvím webové služby.

Zde je, jak našem příkladu vypadá po kliknutí na **nastavit webové služby**:

![Převést prediktivní experiment][figure3]

Podle **nastavit webové služby** může být plně dostačující k přípravě experimentu nasadit jako webovou službu. Můžete však provést některé další kroky, které jsou specifické pro experimentu.

### <a name="adjust-input-and-output-modules"></a>Upravte vstupní a výstupní moduly
V experimentu školení používá sadu Cvičná data a pak se některé zpracování pro získání dat ve formuláři, který algoritmus machine learning potřeby. Pokud toto zpracování nebude nutné data očekáváte získat prostřednictvím webové služby, můžete ho obejít: připojit výstup **vstupní modul webové služby** do jiného modulu v experimentu. Data uživatele nyní dorazí v modelu v tomto umístění.

Například ve výchozím nastavení **nastavit webové služby** umístí **webové služby vstup** modulu v horní části datový tok, jak je znázorněno na obrázku výše. Ale můžete ručně umístit **webové služby vstup** po zpracování dat moduly:

![Přesunutí vstup webové služby][figure4]

Vstupní data poskytnutá prostřednictvím webové služby předá nyní přímo do modul určení skóre modelu bez jakékoli předběžného zpracování.

Podobně, ve výchozím nastavení **nastavit webové služby** uloží modul výstupní webové služby v dolní části datový tok. V tomto příkladu webovou službu vrátí uživateli výstup [Score Model] [ score-model] modul, který se skládá z vektoru dokončení vstupní data a vyhodnocování výsledky.
Ale pokud chcete vrátit něco jiný, pak můžete přidat další moduly před **webové služby výstup** modulu. 

Například pokud chcete vrátit pouze vyhodnocování výsledky a není celý vektoru vstupních dat, přidejte [výběr sloupců v datové sadě] [ select-columns] modulu vyloučit všechny sloupce kromě vyhodnocování výsledky. Pak přejděte **webové služby výstup** modulu výstup [výběr sloupců v datové sadě] [ select-columns] modulu. Experiment vypadá takto:

![Přesunutí výstup webové služby][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Přidat nebo odebrat moduly další zpracování dat
Pokud existují další moduly v experimentu víte, že nebude potřeba při vyhodnocování, tyto lze odebrat. Například protože jsme přesunout **webové služby vstup** modulu bod po zpracování dat moduly, jsme odebrat [vyčištění chybějících dat] [ clean-missing-data] modulu z prediktivní experiment.

Naše prediktivní experiment nyní vypadat třeba takto:

![Odebrání dalších modulu][figure6]


### <a name="add-optional-web-service-parameters"></a>Přidejte volitelné parametry webové služby
V některých případech můžete povolit uživateli webové služby můžete změnit chování modulů při přístupu k službě. *Parametry webové služby* vám umožní to udělat.

Běžným příkladem je nastavení [importovat Data] [ import-data] modulu, takže uživatel nasazenou webovou službu, můžete zadat jiný zdroj dat při přístupu k webové službě. Nebo konfigurace [Export dat] [ export-data] modulu tak, aby lze zadat jiný cíl.

Můžete definovat parametry webové služby a přidružit jeden nebo více parametrů modulu a můžete určit, jestli jsou požadované nebo volitelné. Při přístupu služby a modul akce jsou upraveny uživatele webové služby obsahuje hodnoty pro tyto parametry.

Další informace o jaké jsou parametry webové služby a jejich použití naleznete v tématu [pomocí parametry webové služby Azure Machine Learning][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Nasadit prediktivní experiment jako webovou službu
Teď, když dostatečně připravený prediktivní experiment, můžete ho nasadit jako Azure webové služby. Pomocí webové služby, mohou uživatelé odesílat data do modelu a vrátí její předpovědi modelu.

Další informace o procesu kompletní nasazení najdete v tématu [nasazení webové služby Azure Machine Learning][deploy]

[deploy]: publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
