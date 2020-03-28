---
title: 'Kurz 2: Modely úvěrového rizika vlaku'
titleSuffix: ML Studio (classic) - Azure
description: Podrobný kurz, který ukazuje, jak vytvořit prediktivní analytické řešení pro hodnocení úvěrového rizika v Azure Machine Learning Studio (klasické). Tento kurz je druhá část třídílné série kurzů. Ukazuje, jak trénovat a hodnotit modely.
keywords: úvěrové riziko,řešení prediktivní analýzy,posouzení rizika
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 8feca17f10bb891f0ca5577b2363f95901da4a46
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79217867"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>Kurz 2: Trénování modelů úvěrového rizika – Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

V tomto kurzu se podrobněpodíváte na proces vývoje řešení prediktivní analýzy. Jednoduchý model vyvíjíte ve Strojovém učení (klasické).  Potom nasadit model jako webovou službu Azure Machine Learning.  Tento nasazený model můžete provést předpovědi pomocí nových dat. Tento výukový program je **druhá část třídílné série kurzů**.

Předpokládejme, že potřebujete předpovědět úvěrové riziko u jednotlivých zákazníků na základě údajů, které uvedli v žádosti o úvěr.  

Hodnocení úvěrového rizika je složitý problém, ale tento výukový program to trochu zjednoduší. Použijete ji jako příklad toho, jak můžete vytvořit prediktivní analytické řešení pomocí Microsoft Azure Machine Learning Studio (klasické). Pro toto řešení budete používat Azure Machine Learning Studio (klasické) a webovou službu Machine Learning.  

V tomto třídílném kurzu začnete s veřejně dostupnými údaji o úvěrovém riziku.  Potom vyvinout a trénovat prediktivní model.  Nakonec nasadit model jako webovou službu.

V [první části kurzu](tutorial-part1-credit-risk.md)jste vytvořili pracovní prostor Machine Learning Studio (klasické), nahráli data a vytvořili experiment.

V této části tutoriálu si:
 
> [!div class="checklist"]
> * Trénování více modelů
> * Skóre a vyhodnocení modelů


Ve [třetí části kurzu](tutorial-part3-credit-risk-deploy.md)nasadíte model jako webovou službu.

## <a name="prerequisites"></a>Požadavky

Dokončení [první části výukového programu](tutorial-part1-credit-risk.md).

## <a name="train-multiple-models"></a><a name="train"></a>Trénování více modelů

Jednou z výhod používání Azure Machine Learning Studio (klasické) pro vytváření modelů strojového učení je možnost vyzkoušet více než jeden typ modelu najednou v jednom experimentu a porovnat výsledky. Tento typ experimentování vám pomůže najít nejlepší řešení pro váš problém.

V experimentu, který vyvíjíme v tomto kurzu, vytvoříte dva různé typy modelů a poté porovnáte jejich výsledky hodnocení a rozhodnete se, který algoritmus chcete použít v našem posledním experimentu.  

Existují různé modely, ze kterých si můžete vybrat. Chcete-li zobrazit modely k dispozici, rozbalte uzel **Strojového učení** v paletě modulů a potom **rozbalte Inicializovat model** a uzly pod ním. Pro účely tohoto experimentu vyberete [dvoutřídní podpůrný vektorový počítač][two-class-support-vector-machine] (SVM) a moduly [dvoutřídového posíleného rozhodovacího stromu.][two-class-boosted-decision-tree]

> [!TIP]
> Pokud chcete získat pomoc při rozhodování, který algoritmus strojového učení nejlépe vyhovuje konkrétnímu problému, který se pokoušíte vyřešit, přečtěte si informace [o tom, jak vybrat algoritmy pro Microsoft Azure Machine Learning Studio (klasické).](algorithm-choice.md)
> 
> 

Do tohoto experimentu přidáte modul [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] i modul [Dvoutřídní ho podpůrný vektorový stroj.][two-class-support-vector-machine]

### <a name="two-class-boosted-decision-tree"></a>Posílený rozhodovací strom se dvěma třídami

Nejprve nastavte model posíleného rozhodovacího stromu.

1. Najděte modul [Dvoutřídového posíleného rozhodovacího stromu][two-class-boosted-decision-tree] v paletě modulů a přetáhněte ho na plátno.

1. Najděte modul [Model vlaku,][train-model] přetáhněte ho na plátno a pak připojte výstup modulu [Dvoutřídový posílený rozhodovací strom][two-class-boosted-decision-tree] k levému vstupnímu portu modulu Model [vlaku.][train-model]
   
   [Modul Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] inicializuje obecný model a model [trénování][train-model] používá data školení k trénování modelu. 

1. Připojte levý výstup levého modulu [Execute R Script][execute-r-script] k pravému vstupnímu portu modulu Model [vlaku][train-model] (v tomto kurzu jste [použili data přicházející z levé strany](#train) modulu Split Data pro školení).
   
   > [!TIP]
   > nepotřebujete dva vstupy a jeden z výstupů modulu [Execute R Script][execute-r-script] pro tento experiment, takže je můžete nechat nepřipojené. 
   > 
   > 

Tato část experimentu nyní vypadá asi takto:  

![Školení modelu](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Nyní musíte říct modulu [Model vlaku,][train-model] že chcete, aby model předpověděl hodnotu úvěrového rizika.

1. Vyberte modul [Model vlaku.][train-model] V podokně **Vlastnosti** klepněte na **příkaz Spustit výběr sloupců**.

1. V **dialogovém** okně Vybrat jeden sloupec zadejte do vyhledávacího pole v části **Dostupné sloupce**"Úvěrové riziko" a klepnutím na tlačítko se šipkou vpravo (**>**) přesuňte "Úvěrové riziko" do vybraných **sloupců**. 

    ![Vyberte sloupec Úvěrové riziko pro modul Model vlaku.](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Klikněte na značku **OK.**

### <a name="two-class-support-vector-machine"></a>Support Vector Machine (SVM) se dvěma třídami

Dále nastavíte model SVM.  

Za prvé, trochu vysvětlení o SVM. Posílené rozhodovací stromy dobře fungují s funkcemi jakéhokoli typu. Však vzhledem k tomu, že modul SVM generuje lineární třídění, model, který generuje má nejlepší chybu testu, pokud všechny číselné funkce mají stejné měřítko. Chcete-li převést všechny číselné funkce na stejné měřítko, použijte transformaci "Tanh" (s modulem [Normalize Data).][normalize-data] Tím se naše čísla přeměňují do rozsahu [0,1]. Modul SVM převádí funkce řetězce na kategorické funkce a poté na binární funkce 0/1, takže není nutné ručně transformovat funkce řetězce. Také nechcete transformovat sloupec Úvěrové riziko (sloupec 21) - je to číselné, ale je to hodnota, kterou trénujeme model předpovědět, takže je třeba nechat na pokoji.  

Chcete-li nastavit model SVM, postupujte takto:

1. Najděte modul [Dvoutřídní podpůrný vektorový stroj][two-class-support-vector-machine] v paletě modulů a přetáhněte ho na plátno.

1. Klikněte pravým tlačítkem myši na modul [Model vlaku,][train-model] vyberte **Kopírovat**a potom klikněte pravým tlačítkem myši na plátno a vyberte **Vložit**. Kopie modulu [Model vlaku][train-model] má stejný výběr sloupce jako originál.

1. Připojte výstup modulu [Dvoutřídní podpůrný vektorový stroj][two-class-support-vector-machine] k levému vstupnímu portu druhého modulu [Train Model.][train-model]

1. Najděte modul [Normalizovat data][normalize-data] a přetáhněte ho na plátno.

1. Připojte levý výstup levého modulu [Execute R Script][execute-r-script] ke vstupu tohoto modulu (všimněte si, že výstupní port modulu může být připojen k více než jednomu jinému modulu).

1. Připojte levý výstupní port modulu [Normalize Data][normalize-data] k pravému vstupnímu portu druhého modulu [Model vlaku.][train-model]

Tato část našeho experimentu by nyní měla vypadat nějak takto:  

![Školení druhého modelu](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Nyní nakonfigurujte modul [Normalize Data:][normalize-data]

1. Klepnutím vyberte modul [Normalizovat data.][normalize-data] V podokně **Vlastnosti** vyberte **tanh** pro parametr **metody Transformace.**

1. Klikněte na **Vybrat sloupec spuštění**, vyberte "Žádné sloupce" pro Začít **,** vyberte **Zahrnout** do prvního rozevíracího souboru, vyberte **typ sloupce** v druhém rozevíracím seznamu a ve třetím rozevíracím seznamu vyberte **Číselné.** Tím určíte, že všechny číselné sloupce (a pouze číselné) jsou transformovány.

1. Klikněte na znaménko plus (+) vpravo od tohoto řádku – tím se vytvoří řádek rozevíracích informací. V prvním rozevíracím seznamu vyberte **Vyloučit,** v druhém rozevíracím seznamu vyberte **názvy sloupců** a do textového pole zadejte "Úvěrové riziko". To určuje, že sloupec Úvěrové riziko by měl být ignorován (je třeba to provést, protože tento sloupec je číselný a proto by byl transformován, pokud jste jej nevyloučili).

1. Klikněte na značku **OK.**  

    ![Výběr sloupců pro modul Normalizovat data](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


Modul [Normalize Data][normalize-data] je nyní nastaven na provedení transformace Tanh ve všech číselných sloupcích s výjimkou sloupce Úvěrové riziko.  

## <a name="score-and-evaluate-the-models"></a>Skóre a vyhodnocení modelů

použijete testovací data, která byla oddělena modulem [Split Data,][split] k vyhotovení našich trénovaných modelů. poté můžete porovnat výsledky těchto dvou modelů a zjistit, které modely přinesly lepší výsledky.  

### <a name="add-the-score-model-modules"></a>Přidání modulů modelu skóre

1. Najděte modul [Model skóre][score-model] a přetáhněte ho na plátno.

1. Připojte modul [Model vlaku,][train-model] který je připojen k modulu [Dvoutřídový posílený rozhodovací strom,][two-class-boosted-decision-tree] k levému vstupnímu portu modulu [Model skóre.][score-model]

1. Připojte správný modul [Execute R Script][execute-r-script] (naše testovací data) ke správnému vstupnímu portu modulu Score [Model.][score-model]

    ![Připojený modul modelu skóre](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   Modul [Score Model][score-model] nyní můžete vzít informace o úvěru z testovacích dat, spustit je prostřednictvím modelu a porovnat předpovědi model generuje se skutečným sloupec úvěrové riziko v testovacích dat.

1. Zkopírujte a vložte modul [Model skóre][score-model] a vytvořte druhou kopii.

1. Připojte výstup modelu SVM (tj. výstupní port modulu [Model vlaku,][train-model] který je připojen k modulu [Dvoutřídní podpůrný vektorový stroj)][two-class-support-vector-machine] ke vstupnímu portu druhého modulu [modelu skóre.][score-model]

1. Pro model SVM je třeba provést stejnou transformaci na testovací data jako u trénovacích dat. Takže zkopírujte a vložte modul [Normalize Data][normalize-data] a vytvořte druhou kopii a připojte ji ke správnému modulu Execute [R Script.][execute-r-script]

1. Připojte levý výstup druhého modulu [Normalize Data][normalize-data] k pravému vstupnímu portu druhého modulu [modelu skóre.][score-model]

    ![Oba moduly score modelu jsou připojeny](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Přidání modulu Vyhodnotit model

Chcete-li vyhodnotit dva výsledky hodnocení a porovnat je, použijte [vyhodnotit model][evaluate-model] modulu.  

1. Najděte modul [Vyhodnotit model][evaluate-model] a přetáhněte ho na plátno.

1. Připojte výstupní port modulu [Model skóre][score-model] přidruženého k posílenému modelu rozhodovacího stromu k levému vstupnímu portu modulu [Vyhodnotit model.][evaluate-model]

1. Připojte druhý modul [modelu skóre][score-model] k pravému vstupnímu portu.  

    ![Vyhodnocení připojeného modulu modelu](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Spuštění experimentu a kontrola výsledků

Chcete-li experiment spustit, klepněte na tlačítko **RUN** pod plátnem. Může to trvat několik minut. Indikátor předení na každém modulu ukazuje, že je spuštěn, a pak zelená značka zaškrtnutí zobrazí, když je modul dokončen. Když všechny moduly mají zaškrtnutí, experiment byl dokončen.

Experiment by nyní měl vypadat nějak takto:  

![Vyhodnocení obou modelů](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Chcete-li zkontrolovat výsledky, klepněte na výstupní port modulu [Vyhodnotit model][evaluate-model] a vyberte **možnost Vizualizovat**.  

Modul [Vyhodnotit model][evaluate-model] vytvoří dvojici křivek a metrik, které umožňují porovnat výsledky dvou modelů s hodnocením. Výsledky můžete zobrazit jako křivky Charakteristika operátora přijímače (ROC), Křivky přesnosti/odvolání nebo Křivky zdvižení. Další zobrazená data zahrnují matici záměny, kumulativní hodnoty pro oblast pod křivkou (AUC) a další metriky. Prahovou hodnotu můžete změnit přesunutím jezdce doleva nebo doprava a uvidíte, jak ovlivňuje sadu metrik.  

Napravo od grafu klikněte na **Datová sada s vyhodnocenou skóre** nebo **Datová sada s kinem, abyste porovnali zobrazení** přidružené křivky a zobrazili přidružené metriky níže. V legendě pro křivky "Scored dataset" odpovídá levému vstupnímu portu modulu [Vyhodnotit model][evaluate-model] - v našem případě se jedná o model posíleného rozhodovacího stromu. "Scored dataset porovnat" odpovídá správnému vstupnímu portu - modelu SVM v našem případě. Po klepnutí na jeden z těchto popisků se zvýrazní křivka pro tento model a zobrazí se odpovídající metriky, jak je znázorněno na následujícím obrázku.  

![Roc křivky pro modely](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Zkoumáním těchto hodnot se můžete rozhodnout, který model je nejblíže k tomu, aby vám poskytl výsledky, které hledáte. Můžete se vrátit zpět a iterate na experiment změnou hodnoty parametrů v různých modelech. 

Věda a umění interpretace těchto výsledků a ladění výkonu modelu je mimo rozsah tohoto kurzu. Další nápovědu naleznete v následujících článcích:
- [Jak vyhodnotit výkon modelu v Azure Machine Learning Studio (klasické)](evaluate-model-performance.md)
- [Výběr parametrů pro optimalizaci algoritmů v Azure Machine Learning Studio (klasické)](algorithm-parameters-optimize.md)
- [Interpretace výsledků modelu v Azure Machine Learning Studio (klasické)](interpret-model-results.md)

> [!TIP]
> Při každém spuštění experimentu je záznam této iterace uložen v historii spuštění. Můžete zobrazit tyto iterace a vrátit se k libovolné z nich kliknutím **na zobrazit historii spuštění** pod plátnem. Můžete také klepnout na **předchozí spuštění** v podokně **Vlastnosti** a vrátit se k iteraci bezprostředně předcházející otevřené iteraci.
> 
> Můžete vytvořit kopii libovolné iterace experimentu kliknutím uložit **jako** pod plátnem. 
> Pomocí vlastností **Souhrn** a **popis** experimentu můžete uchovávat záznamy o tom, co jste vyzkoušeli v iterací experimentu.
> 
> Další informace najdete [v tématu Správa iterací experimentu v Azure Machine Learning Studio (klasické)](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste dokončili tyto kroky: 
 
> [!div class="checklist"]
> * Vytvoření experimentu
> * Trénování více modelů
> * Skóre a vyhodnocení modelů

Nyní jste připraveni k nasazení modelů pro tato data.

> [!div class="nextstepaction"]
> [Kurz 3 – Nasazení modelů](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/