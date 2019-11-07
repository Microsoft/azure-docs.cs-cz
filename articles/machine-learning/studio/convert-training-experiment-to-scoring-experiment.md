---
title: Příprava modelu pro nasazení
titleSuffix: ML Studio (classic) Azure
description: Jak připravit vyškolený model pro nasazení jako webovou službu převodem výukového experimentu Machine Learning Studio (Classic) na prediktivní experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 03/28/2017
ms.openlocfilehash: e24393783dac0f918009f3138f31bdda98bbd22e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684854"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Postup přípravy modelu pro nasazení v Azure Machine Learning Studio (Classic)

Azure Machine Learning Studio (Classic) poskytuje nástroje, které potřebujete k vývoji prediktivních analytických modelů, a pak je zprovoznění nasadit jako webovou službu Azure.

K tomu je třeba použít klasickou verzi nástroje Studio k vytvoření experimentu, který se nazývá *experiment* , kde můžete vytvářet výuku, skóre a upravovat svůj model. Jakmile budete spokojeni, získáte model připravený k nasazení, a to tak, že převedete svůj školicí experiment na *prediktivní experiment* , který je nakonfigurovaný tak, aby vyhledal data uživatelů.

Příklad tohoto procesu můžete zobrazit v [kurzu 1: předpověď úvěrového rizika](tutorial-part1-credit-risk.md).

V tomto článku se postará o podrobné informace o tom, jak se zkušební experiment převede do prediktivního experimentu a jak se nasazuje prediktivní experiment. Díky porozumění těmto podrobnostem se můžete dozvědět, jak nakonfigurovat nasazený model tak, aby byl efektivnější.



## <a name="overview"></a>Přehled 

Proces převodu školicího experimentu na prediktivní experiment zahrnuje tři kroky:

1. Nahraďte moduly algoritmu strojového učení vašim vyškolenou modelem.
2. Zkraťte experiment jenom na ty moduly, které jsou potřeba pro vyhodnocování. Školicí experiment zahrnuje řadu modulů, které jsou nezbytné pro školení, ale nejsou potřeba, když je model vyškolený.
3. Definujte, jak bude model přijímat data od uživatele webové služby a jaká data budou vrácena.

> [!TIP]
> Ve školicím experimentu jste se rozhodli o školení a vyhodnocování modelu pomocí vašich vlastních dat. Ale po nasazení budou uživatelé do modelu posílat nová data a výsledky předpovědi budou vracet. Takže při převodu školicího experimentu na prediktivní experiment, který je připravený pro nasazení, pamatujte na to, jak model budou používat jiní uživatelé.
> 
> 

## <a name="set-up-web-service-button"></a>Tlačítko nastavit webovou službu
Po spuštění experimentu (klikněte na **Run (spustit** ) v dolní části plátna experimentu), klikněte na tlačítko **nastavit webovou službu** (vyberte možnost **prediktivní webová služba** ). **Nastavení webové služby** provede za vás tři kroky převodu školicího experimentu na prediktivní experiment:

1. Tento model se uloží do části **Proučené modely** v paletě modulu (nalevo od plátna experimentu). Pak nahrazuje algoritmus strojového učení a moduly [modelu vlaků][train-model] s uloženým školicím modelem.
2. Analyzuje experiment a odebírá moduly, které byly jasně používány pouze pro školení a již není potřeba.
3. Vloží vstupní a _výstupní_ moduly _webové služby_ do výchozích umístění v experimentu (tyto moduly přijímají a vrátí data uživatelů).

Například následující experiment navlakuje pomocí ukázkového sčítání dat modelem zesíleného rozhodovacího stromu:

![Experiment školení](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Moduly v tomto experimentu provádějí základní čtyři různé funkce:

![Funkce modulu](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Když převedete tento experiment školení na prediktivní experiment, některé z těchto modulů už nejsou potřebné nebo teď budou sloužit k jinému účelu:

* **Data** – data v této ukázkové datové sadě se při vyhodnocování nepoužijí – uživatel webové služby dodá data, která se mají určit skóre. Nicméně metadata z této datové sady, jako jsou například datové typy, používá trained model. Proto je třeba zachovat datovou sadu ve prediktivním experimentu, aby mohla tato metadata poskytnout.

* **Příprava** – v závislosti na uživatelských datech, která budou odeslána pro vyhodnocování, mohou být tyto moduly nebo nemusí být nezbytné ke zpracování příchozích dat. Tlačítko **nastavit webovou službu** je nedotykové – musíte se rozhodnout, jak je chcete zpracovat.
  
    Například v tomto příkladu může mít ukázková datová sada chybějící hodnoty, takže modul [Vyčištění chybějících dat][clean-missing-data] byl zahrnut k tomu, aby se s nimi mohla pracovat. Ukázková datová sada také obsahuje sloupce, které nejsou nutné pro výuku modelu. Proto byl zahrnutý modul [Výběr sloupců v datové sadě][select-columns] , aby vyloučil tyto nadbytečné sloupce z toku dat. Pokud víte, že data, která budou odeslána pro bodování prostřednictvím webové služby, nebudou obsahovat hodnoty, můžete odebrat modul [Vyčištění chybějících dat][clean-missing-data] . Vzhledem k tomu, že modul [Výběr sloupců v datové sadě][select-columns] pomáhá definovat sloupce dat, které vyškolený model očekává, musí tento modul zůstat.

* **Výuka** – tyto moduly se používají ke školení modelu. Když kliknete na **nastavit webovou službu**, tyto moduly se nahradí jedním modulem, který obsahuje model, který jste vyškole. Tento nový modul je uložený v části s **výukou modelů** v paletě modulu.

* **Skóre** – v tomto příkladu se modul [rozdělení dat][split] používá k rozdělení datového proudu do testovacích dat a dat školení. Ve prediktivním experimentu už nebudeme školením moct odebrat [rozdělená data][split] . Podobně modul s druhým [modelem skóre][score-model] a modul [vyhodnocení modelu][evaluate-model] slouží k porovnání výsledků z testovacích dat, takže tyto moduly nejsou v prediktivním experimentu potřeba. Modul zbývajícího [modelu skóre][score-model] je však potřeba k vrácení výsledku skóre prostřednictvím webové služby.

Tady je postup, jak náš příklad vypadá po kliknutí na **nastavit webovou službu**:

![Převedený prediktivní experiment](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Práce, kterou provedete pomocí **nastavení webové služby** , může být dostačující k přípravě experimentu, který chcete nasadit jako webovou službu. Můžete ale chtít udělat další práci, která je specifická pro váš experiment.

### <a name="adjust-input-and-output-modules"></a>Upravit vstupní a výstupní moduly
Ve školicím experimentu jste použili sadu školicích dat a pak nějaké zpracování využívali k získání dat ve formuláři, který algoritmus strojového učení potřebuje. Pokud data, která očekáváte pro příjem prostřednictvím webové služby, nebude potřeba zpracovat, můžete ji obejít: připojit výstup **vstupního modulu webové služby** k jinému modulu v experimentu. Data uživatele se nyní dostanou do modelu v tomto umístění.

Například ve výchozím nastavení umístí **Webová služba** **Vstupní modul webové služby** v horní části toku dat, jak je znázorněno na obrázku výše. Ale můžeme ručně umístit **vstup webové služby** za moduly zpracování dat:

![Přesun vstupu webové služby](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Vstupní data poskytnutá prostřednictvím webové služby teď budou předávat přímo do modulu bodového modelu bez jakéhokoli předběžného zpracování.

Podobně výchozí nastavení **webové služby** umístí modul výstupu webové služby do dolní části toku dat. V tomto příkladu se webová služba vrátí uživateli výstup modulu určení [skóre modelu][score-model] , který zahrnuje kompletní vstupní datový vektor a výsledky bodování.
Pokud ale dáváte přednost vrácení jiné položky, můžete přidat další moduly před **výstupní modul webové služby** . 

Například chcete-li vrátit pouze výsledky bodování a ne celý vektor vstupních dat, přidejte modul [Výběr sloupců v datové sadě][select-columns] , který vyloučí všechny sloupce kromě výsledků bodování. Pak přesuňte modul **výstupu webové služby** na výstup modulu [Výběr sloupců v datové sadě][select-columns] . Experiment vypadá takto:

![Přesunutí výstupu webové služby](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Přidat nebo odebrat další moduly zpracování dat
Pokud máte v experimentu více modulů, které vás při bodování nebudou potřebovat, můžete je odebrat. Protože jsme například přesunuli modul **vstupu webové služby** do bodu za moduly zpracování dat, můžeme z prediktivního experimentu odebrat modul [Vyčištění chybějících dat][clean-missing-data] .

Náš prediktivní experiment teď vypadá takto:

![Odebírá se další modul.](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Přidat volitelné parametry webové služby
V některých případech můžete chtít uživateli webové služby změnit chování modulů, když je služba k dispozici. Tato možnost umožňuje použít *parametry webové služby* .

Běžným příkladem je nastavení modulu [Import dat][import-data] , aby uživatel nasazené webové služby mohl při použití webové služby zadat jiný zdroj dat. Nebo nakonfigurujte modul [exportu dat][export-data] tak, aby bylo možné zadat jiný cíl.

Můžete definovat parametry webové služby a přidružit je k jednomu nebo více parametrům modulu a můžete určit, jestli jsou povinné nebo volitelné. Uživatel webové služby poskytne hodnoty pro tyto parametry, když je služba k dispozici, a akce modulu se upraví odpovídajícím způsobem.

Další informace o tom, jaké parametry webové služby jsou a jak je používat, najdete v tématu [použití Azure Machine Learningch parametrů webové služby][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Nasazení prediktivního experimentu jako webové služby
Teď, když je prediktivní experiment dostatečně připravený, můžete ho nasadit jako webovou službu Azure. Pomocí webové služby mohou uživatelé odesílat data do modelu a model vrátí jeho předpovědi.

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
