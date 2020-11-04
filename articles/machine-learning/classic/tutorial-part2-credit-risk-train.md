---
title: 'Kurz pro ML Studio (Classic) 2: modely úvěrového riziku vlaku – Azure'
description: Podrobný kurz, který ukazuje, jak vytvořit řešení prediktivní analýzy pro posuzování úvěrového rizika v Azure Machine Learning Studio (Classic). Tento kurz je druhou částí série kurzů s třemi částmi. Ukazuje, jak proškolit a vyhodnocovat modely.
keywords: úvěrové riziko,řešení prediktivní analýzy,posouzení rizika
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 59567cf2dc03952a78852f3288e78ba06aa769ee
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325692"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>Kurz 2: modely úvěrových rizik výukového programu – Azure Machine Learning Studio (Classic)

**platí pro:** ![ Ano ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ bez ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


V tomto kurzu se podíváme na proces vývoje řešení prediktivní analýzy. V Machine Learning Studio (Classic) vyvíjíte jednoduchý model.  Model pak nasadíte jako Azure Machine Learning webovou službu.  Tento nasazený model může vytvářet předpovědi s využitím nových dat. Tento kurz je **druhou částí série kurzů s třemi částmi**.

Předpokládejme, že potřebujete předpovědět úvěrové riziko u jednotlivých zákazníků na základě údajů, které uvedli v žádosti o úvěr.  

Posouzení úvěrového rizika je složitý problém, ale v tomto kurzu se tento kurz zjednodušuje. Použijete ho jako příklad, jak můžete vytvořit řešení prediktivní analýzy pomocí Microsoft Azure Machine Learning Studio (Classic). Pro toto řešení použijete Azure Machine Learning Studio (Classic) a Machine Learning webovou službu.  

V tomto kurzu se třemi částmi začínáte veřejně dostupnými daty o úvěrovém riziku.  Pak vyvíjíte a naučíte prediktivní model.  Nakonec model nasadíte jako webovou službu.

V [první části kurzu](tutorial-part1-credit-risk.md)jste vytvořili pracovní prostor Machine Learning Studio (klasický), nahráli jste data a vytvořili experiment.

V této části kurzu:
 
> [!div class="checklist"]
> * Výuka více modelů
> * Skóre a vyhodnocení modelů


V [třetí části kurzu](tutorial-part3-credit-risk-deploy.md)nasadíte model jako webovou službu.

## <a name="prerequisites"></a>Předpoklady

Vyplňte [první část kurzu](tutorial-part1-credit-risk.md).

## <a name="train-multiple-models"></a><a name="train"></a>Výuka více modelů

Jednou z výhod použití Azure Machine Learning Studio (Classic) pro vytváření modelů strojového učení je možnost vyzkoušet v jednom experimentu více než jeden typ modelu a porovnat výsledky. Tento typ experimentu vám pomůže najít nejlepší řešení pro váš problém.

V experimentu, který vyvíjíme v tomto kurzu, vytvoříte dva různé typy modelů a pak porovnáte výsledky jejich bodování a určíte, který algoritmus chcete použít v konečném experimentu.  

Existují různé modely, ze kterých si můžete vybrat. Pokud chcete zobrazit dostupné modely, rozbalte uzel **Machine Learning** v paletě modulu a potom rozbalte položku **inicializovat model** a uzly pod ním. Pro účely tohoto experimentu vyberete SVM ( [Podpora dvou tříd][two-class-support-vector-machine] ) a moduly pro [zvýšení rozhodovacího stromu se dvěma třídami][two-class-boosted-decision-tree] .

> [!TIP]
> Pokud chcete získat pomoc s rozhodnutím, který Machine Learning algoritmus nejlépe vyhovuje konkrétnímu problému, který se snažíte vyřešit, přečtěte si téma [Jak zvolit algoritmy pro Microsoft Azure Machine Learning Studio (Classic)](../how-to-select-algorithms.md).
> 
> 

Do tohoto experimentu přidáte jak modul [zvýšení rozhodovacího stromu obou tříd][two-class-boosted-decision-tree] , tak i modul [podpory dvou tříd][two-class-support-vector-machine] .

### <a name="two-class-boosted-decision-tree"></a>Posílený rozhodovací strom se dvěma třídami

Nejdřív nastavte model zesíleného rozhodovacího stromu.

1. V paletě modulu Najděte modul pro [zvýšení rozhodovacího stromu se dvěma třídami][two-class-boosted-decision-tree] a přetáhněte ho na plátno.

1. Najděte modul [vlakového modelu][train-model] , přetáhněte ho na plátno a pak připojte výstup modulu [zesíleného rozhodovacího stromu se dvěma třídami][two-class-boosted-decision-tree] k levému vstupnímu portu modulu [vlakového modelu][train-model] .
   
   Modul pro [zvýšení rozhodovacího stromu se dvěma třídami][two-class-boosted-decision-tree] inicializuje obecný model a [model][train-model] výuky používá školicí data pro výuku modelu. 

1. Připojte levý výstup levého modulu [spuštění skriptu jazyka R][execute-r-script] ke správnému vstupnímu portu modulu [vlakového modelu][train-model] (v tomto kurzu jste [použili data přicházející z levé strany](#train) modulu rozdělit data pro školení).
   
   > [!TIP]
   > pro tento experiment nepotřebujete dva vstupy a jeden z výstupů modulu [spuštění skriptu jazyka R][execute-r-script] , takže je můžete nechat nepřipojený. 
   > 
   > 

Tato část experimentu teď vypadá nějak takto:  

![Školení modelu](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Nyní potřebujete sdělit modul [vlakového modelu][train-model] , který má model předpovědět hodnotu úvěrového rizika.

1. Vyberte modul [vlakového modelu][train-model] . V podokně **vlastnosti** klikněte na **Spustit selektor sloupců**.

1. V dialogovém okně **vybrat jeden sloupec** zadejte v poli Hledat v části **Dostupné sloupce** položku úvěrové riziko, vyberte níže "úvěrové riziko" a kliknutím na tlačítko se šipkou doprava ( **>** ) přesuňte "úvěrové riziko" na **vybrané sloupce**. 

    ![Výběr sloupce úvěrového rizika pro modul vlakového modelu](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Klikněte na značku zaškrtnutí **OK** .

### <a name="two-class-support-vector-machine"></a>Support Vector Machine (SVM) se dvěma třídami

Dále nastavte SVM model.  

Za prvé, trochu vysvětlující informace o SVM. Vylepšené rozhodovací stromy dobře fungují s funkcemi libovolného typu. Vzhledem k tomu, že modul SVM generuje lineární klasifikátor, má model, který generuje, k chybě nejlepšího testu, pokud mají všechny číselné funkce stejné měřítko. Chcete-li převést všechny číselné funkce na stejné měřítko, použijte transformaci "tanh –" (s modulem [data Normalize][normalize-data] ). Tím se čísla transformují do rozsahu [0, 1]. Modul SVM převádí řetězcové funkce na funkce kategorií a pak do binárních funkcí 0/1, takže nemusíte ručně transformovat řetězcové funkce. Nechcete také transformovat sloupec úvěrového rizika (sloupec 21) – je číslo, ale je to hodnota, kterou sledujeme model pro předpověď, takže je potřeba ho ponechat samostatně.  

K nastavení modelu SVM postupujte takto:

1. V paletě modulu Najděte modul pro [vektorový stroj podpory dvou tříd][two-class-support-vector-machine] a přetáhněte ho na plátno.

1. Klikněte pravým tlačítkem na modul [vlakového modelu][train-model] , vyberte **Kopírovat** a pak klikněte pravým tlačítkem na plátno a vyberte **Vložit**. Kopie modulu [vlakového modelu][train-model] má stejný výběr sloupce jako původní.

1. Připojte výstup modulu [vektorového počítače podpory dvou tříd][two-class-support-vector-machine] k levému vstupnímu portu druhého modulu [vlakového modelu][train-model] .

1. Najděte modul [Normalize data][normalize-data] a přetáhněte ho na plátno.

1. Připojí levý výstup levého modulu [spuštění skriptu jazyka R][execute-r-script] ke vstupu tohoto modulu (Všimněte si, že výstupní port modulu může být připojen k více než jednomu jinému modulu).

1. Připojte levý výstupní port modulu [Normalize data][normalize-data] ke správnému vstupnímu portu druhého modulu [vlakového modelu][train-model] .

Tato část našeho experimentu by teď měla vypadat nějak takto:  

![Školení druhého modelu](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Teď nakonfigurujte modul [dat Normalize][normalize-data] :

1. Kliknutím vyberte modul [normalizing data][normalize-data] Module. V podokně **vlastnosti** vyberte možnost **tanh –** pro parametr **metody transformace** .

1. Klikněte na tlačítko **Spustit selektor sloupců** , vyberte možnost žádné sloupce pro možnost **začít s** , vyberte možnost **Zahrnout** v prvním rozevíracím seznamu, v rozevíracím seznamu druhý vyberte **typ sloupce** a v rozevíracím seznamu třetí vyberte **číslo** . Tím se určí, že se transformují všechny číselné sloupce (a jenom číselné).

1. Klikněte na znaménko plus (+) napravo od tohoto řádku – tím se vytvoří řádek rozevíracích seznamů. V prvním rozevíracím seznamu vyberte **vyloučit** , v druhém rozevíracím seznamu vyberte **názvy sloupců** a v textovém poli zadejte "úvěrové riziko". To určuje, že by se měl sloupec úvěrového rizika ignorovat (musíte to udělat, protože tento sloupec je numerický, takže by se transformoval, pokud jste ho nevyloučili).

1. Klikněte na značku zaškrtnutí **OK** .  

    ![Vyberte sloupce pro modul normalizing data Module.](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


Modul [normalizing data][normalize-data] Module je teď nastavený tak, aby provedl transformaci tanh – pro všechny číselné sloupce s výjimkou sloupce úvěrové riziko.  

## <a name="score-and-evaluate-the-models"></a>Skóre a vyhodnocení modelů

testovací data, která byla oddělená modulem [rozdělení dat][split] , můžete použít k vyhodnocení našich vyškolených modelů. pak můžete porovnat výsledky dvou modelů a zjistit, které vygenerovaly lepší výsledky.  

### <a name="add-the-score-model-modules"></a>Přidání modulů modelu skóre

1. Najděte modul určení [skóre modelu][score-model] a přetáhněte ho na plátno.

1. Připojte modul [vlakového modelu][train-model] , který je připojený k modulu [zesíleného rozhodovacího stromu se dvěma třídami][two-class-boosted-decision-tree] , na levý vstupní port modulu určení [skóre modelu][score-model] .

1. Připojte ke správnému vstupnímu portu modulu [skóre modelu][score-model] správný spouštěcí modul [R skriptu][execute-r-script] (naše testovací data).

    ![Připojený modul modelu skóre](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   Modul určení [skóre][score-model] teď může z testovacích dat přebírat platební údaje, spustit ho v modelu a porovnat předpovědi, který model generuje, se skutečným sloupcem úvěrového rizika v testovacích datech.

1. Zkopírujte a vložte modul určení [skóre modelu][score-model] a vytvořte druhou kopii.

1. Připojte výstup modelu SVM (to znamená výstupní port modulu [vlak model][train-model] , který je připojený ke [třídě Vector Machine Machine support][two-class-support-vector-machine] Module), do vstupního portu druhého modulu [bodového modelu][score-model] .

1. V případě modelu SVM je třeba provést stejnou transformaci testovacích dat, jako byste to provedli v školicích datech. Proto zkopírujte a vložte modul [Normalize data][normalize-data] a vytvořte druhou kopii a připojte ji ke správnému modulu [skriptu jazyka R][execute-r-script] .

1. Připojte levý výstup z druhého modulu [Normalize data][normalize-data] ke správnému vstupnímu portu pro druhý modul [bodového modelu][score-model] .

    ![Oba propojené moduly modelu skóre](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Přidání modulu vyhodnocení modelu

Pro vyhodnocení dvou výsledků bodování a jejich porovnání použijte modul [vyhodnocení modelu][evaluate-model] .  

1. Najděte modul [vyhodnocení modelu][evaluate-model] a přetáhněte ho na plátno.

1. Připojte výstupní port modulu [bodového modelu][score-model] , který je přidružený k modelu zesíleného rozhodovacího stromu, k levému vstupnímu portu modulu [vyhodnocení modelu][evaluate-model] .

1. Připojte modul pro druhý [model skóre][score-model] ke správnému vstupnímu portu.  

    ![Vyhodnotit připojený modul modelu](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Spusťte experiment a podívejte se na výsledky.

Experiment spustíte tak, že kliknete na tlačítko **Spustit** pod plátnem. Může to trvat několik minut. Otáčející se ukazatel u každého modulu ukazuje, že je spuštěný, a po dokončení modulu se zobrazí zelená značka zaškrtnutí. Když mají všechny moduly značku zaškrtnutí, experiment byl dokončen.

Experiment by teď měl vypadat nějak takto:  

![Vyhodnocování obou modelů](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Pokud chcete výsledky kontrolovat, klikněte na výstupní port modulu [vyhodnocení modelu][evaluate-model] a vyberte **vizualizovat**.  

Modul [vyhodnocení modelu][evaluate-model] vytvoří dvojici křivek a metrik, které vám umožní porovnat výsledky dvou modelů skóre. Výsledky můžete zobrazit jako charakteristiky operátorů přijímače (ROC), křivky přesnosti a odvolání nebo křivky zvednutí. Další zobrazená data zahrnují nejasnou matrici, kumulativní hodnoty pro oblast pod křivkou (AUC) a další metriky. Mezní hodnotu můžete změnit přesunutím posuvníku vlevo nebo vpravo a zobrazením vlivu na sadu metrik.  

Napravo od grafu klikněte na **vyhodnocenou** datovou sadu nebo **datovou sadu, která se má porovnat** , zvýrazněte související křivku a zobrazte související metriky níže. V legendě pro křivky "sada dat s skóre" odpovídá levému vstupnímu portu modulu [vyhodnocení modelu][evaluate-model] – v našem případě se jedná o model zesíleného rozhodovacího stromu. "Vyhodnocená datová sada pro porovnání" odpovídá správnému vstupnímu portu – model SVM v našem případě. Po kliknutí na jeden z těchto popisků je křivka tohoto modelu zvýrazněna a zobrazí se odpovídající metrika, jak je znázorněno na následujícím obrázku.  

![ROC – křivky pro modely](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Prozkoumáním těchto hodnot se můžete rozhodnout, který model je nejblížený, abyste vám poskytli výsledky, které hledáte. Můžete se vrátit zpět a iterovat na experimentu změnou hodnot parametrů v různých modelech. 

Vědy a umělecké interpretace těchto výsledků a optimalizace výkonu modelu jsou mimo rozsah tohoto kurzu. Pro další nápovědu si můžete přečíst následující články:
- [Postup vyhodnocení výkonu modelu v Azure Machine Learning Studio (Classic)](evaluate-model-performance.md)
- [Výběr parametrů pro optimalizaci algoritmů v Azure Machine Learning Studio (Classic)](algorithm-parameters-optimize.md)
- [Interpretace výsledků modelu v Azure Machine Learning Studio (Classic)](interpret-model-results.md)

> [!TIP]
> Pokaždé, když spustíte experiment, záznam této iterace se uchová v historii spuštění. Tyto iterace můžete zobrazit a vrátit se k libovolné z nich kliknutím na **Zobrazit historii spuštění** pod plátnem. Můžete také kliknout na **předchozí spuštění** v podokně **vlastnosti** a vrátit se do iterace hned před tím, co jste otevřeli.
> 
> Kopii jakékoli iterace experimentu můžete vytvořit tak, že kliknete na tlačítko **Uložit** pod plátnem. 
> Pomocí vlastností **souhrnu** a **popisu** experimentu můžete uchovat záznam o tom, co jste se snažili v iteracích experimentu.
> 
> Další informace najdete v tématu [Správa iterací experimentů v Azure Machine Learning Studio (Classic)](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste dokončili tyto kroky: 
 
> [!div class="checklist"]
> * Vytvoření experimentu
> * Výuka více modelů
> * Skóre a vyhodnocení modelů

Nyní jste připraveni nasadit modely pro tato data.

> [!div class="nextstepaction"]
> [Kurz 3 – Nasazení modelů](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[split]: /azure/machine-learning/studio-module-reference/split-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/