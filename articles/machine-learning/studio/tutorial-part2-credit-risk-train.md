---
title: 'Kurz 2: Trénování modelů úvěrové riziko'
titleSuffix: Azure Machine Learning Studio
description: Podrobný kurz ukazuje, jak vytvořit řešení prediktivní analýzy pro posuzování úvěrového rizika v nástroji Azure Machine Learning Studio. Tento kurz je druhou částí z třídílné série. Ukazuje, jak pro trénování a vyhodnocení modelů.
keywords: úvěrové riziko,řešení prediktivní analýzy,posouzení rizika
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 8ffcfc86823d46e65e116eed86ef35fcba2a99bf
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008010"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio"></a>Kurz 2: Trénování modelů úvěrové riziko - Azure Machine Learning Studio

V tomto kurzu provedete rozšířené pohled na proces vývoje řešení prediktivní analýzy. Při vývoji jednoduchý model v nástroji Machine Learning Studio.  Potom tento model nasadíme jako webové služby Azure Machine Learning.  Tento model nasazený můžete vytvářet predikce na nová data. Tento kurz je **druhou částí z třídílné série kurzů**.

Předpokládejme, že potřebujete předpovědět úvěrové riziko u jednotlivých zákazníků na základě údajů, které uvedli v žádosti o úvěr.  

Posouzení úvěrového rizika je komplexní problém, ale v tomto kurzu se tak si ho Zjednodušte trochu. Budete ho používat jako příklad, jak můžete vytvořit řešení prediktivní analýzy pomocí služby Microsoft Azure Machine Learning. Budete používat Azure Machine Learning Studio a webovou službou Machine Learning pro toto řešení.  

V tomto kurzu třemi částmi začnete s veřejně dostupnými daty úvěrového rizika.  Vyvíjejte a natrénujeme prediktivní model.  Nakonec tento model nasadíme jako webovou službu.

V [první části kurzu](tutorial-part1-credit-risk.md), vytvořili pracovní prostor Machine Learning Studio, nahrát data a vytvoření experimentu.

V této části kurzu jste:
 
> [!div class="checklist"]
> * Trénování více modelů
> * Stanovení skóre a vyhodnocení modelů


V [třetí částí z kurzu](tutorial-part3-credit-risk-deploy.md), bude nasazení modelu jako webové služby.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="prerequisites"></a>Požadavky

Kompletní [první části kurzu](tutorial-part1-credit-risk.md).

## <a name="train"></a>Trénování více modelů

Jednou z výhod pro vytváření modelů strojového učení pomocí Azure Machine Learning Studio je schopnost zkuste více než jeden typ modelu v daný okamžik v jednom experimentu a porovnejte výsledky. Tento typ služby experimentování ve službě vám pomůže najít nejlepší řešení pro váš problém.

V experiment, který vyvíjíme v tomto kurzu vytvoříte dva různé typy modelů a porovnejte jejich vyhodnocování výsledky se rozhodnout, které algoritmus, kterou chcete použít v našich konečný experiment.  

Existují různé modely, které může vybírat. Pokud chcete zobrazit dostupné modely, rozbalte **Machine Learning** uzel paletě modulů a potom rozbalte **inicializovat Model** a uzlů pod ním. Pro účely tohoto experimentu, vyberete [Two-Class Support Vector Machine] [ two-class-support-vector-machine] (SVM) a [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] moduly.

> [!TIP]
> Nechte si poradit který nejlepší algoritmus Machine Learning vyhovuje konkrétního problému, který se snažíte vyřešit, najdete v článku [jak zvolit algoritmy pro Microsoft Azure Machine Learning Studio](algorithm-choice.md).
> 
> 

Přidáte i [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] modulu a [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modulu v tohoto experimentu .

### <a name="two-class-boosted-decision-tree"></a>Two-Class posíleného rozhodovacího stromu

Nejprve nastavte model posíleného rozhodovacího stromu.

1. Najít [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] modulu paletě modulů a přetáhněte ji na plátno.

1. Najít [Train Model] [ train-model] modulu, přetáhněte na plátno a potom propojte výstup modulu [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] modul na levé straně vstupním portem modulu [Train Model] [ train-model] modulu.
   
   [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] inicializuje modul obecný model a [trénování modelu] [ train-model] používá trénovacích dat pro trénování model. 

1. Propojte levý výstup levé straně [spustit skript jazyka R] [ execute-r-script] modulu na pravé straně vstupním portem modulu [Train Model] [ train-model] modulu (v tomto kurzu můžete [používá data přicházející z levé strany](#train) modulu rozdělení dat pro trénování).
   
   > [!TIP]
   > není nutné dvou vstupů a jeden z výstupů [spustit skript jazyka R] [ execute-r-script] modul pro tento experiment, takže můžete nechat nepřipojené. 
   > 
   > 

Tato část experiment teď vypadá přibližně takto:  

![Trénování modelu](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Nyní je třeba sdělit [Train Model] [ train-model] modul, který má model pro předpověď úvěrového rizika hodnotu.

1. Vyberte [Train Model] [ train-model] modulu. V **vlastnosti** podokně klikněte na tlačítko **spustit selektor sloupců**.

1. V **zvolte jeden sloupec** dialogové okno, zadejte vyhledávacího pole v části "úvěrové riziko" **dostupných sloupců**, vyberte "Úvěrové riziko" níže a klikněte na tlačítko se šipkou doprava (**>**) přesunete "Úvěrové riziko" **vybrané sloupce**. 

    ![Vyberte sloupec úvěrové riziko pro modul trénování modelu](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Klikněte na tlačítko **OK** zaškrtávací políčko.

### <a name="two-class-support-vector-machine"></a>Support Vector Machine (SVM) se dvěma třídami

V dalším kroku nastavíte SVM modelu.  

První, trochu vysvětlení SVM. Posílený rozhodovací stromy dobře fungují s funkcí libovolného typu. Protože SVM modul generuje lineární třídění, model, který generuje má ale nejlepší Chyba testu při všechny číselné funkce mají stejné měřítko. Převést všechny číselné funkce na stejném měřítku, používáte transformaci "Tanh" (s [normalizovat Data] [ normalize-data] modul). To transformuje naše čísla v rozsahu [0, 1]. Modulu SVM převede funkce řetězec zařazené do kategorií funkce a klikněte na binární funkce 0 nebo 1, takže není nutné ručně transformace funkce řetězec. Navíc nechcete transformovat úvěrové riziko column (sloupec 21) – je číselná, ale jedná se o hodnotu jsme supervizí trénujete model k predikci, takže je třeba ponechat samostatně.  

Nastavení modelu SVM, postupujte takto:

1. Najít [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modulu paletě modulů a přetáhněte ji na plátno.

1. Klikněte pravým tlačítkem na [Train Model] [ train-model] modulu, vyberte **kopírování**a potom klikněte pravým tlačítkem na plátně a vyberte možnost **vložit**. Kopii [Train Model] [ train-model] modul má stejné výběr sloupce jako původní.

1. Propojte výstup modulu [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modul na levé straně vstupním portem modulu druhý [Train Model] [ train-model] modulu.

1. Najít [normalizovat Data] [ normalize-data] modulu a přetáhněte ji na plátno.

1. Propojte levý výstup levé straně [spustit skript jazyka R] [ execute-r-script] tohoto modulu (Všimněte si, že na výstupní port modulu může být připojen k více než jeden další modul) se vstupem modulu.

1. Propojte levý výstupní port modulu [normalizovat Data] [ normalize-data] modulu na pravé straně vstupním portem modulu druhý [Train Model] [ train-model] modulu.

Tato část naší experiment by teď měl vypadat přibližně takto:  

![Trénujete model pro druhý](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Teď nakonfigurovat [normalizovat Data] [ normalize-data] modul:

1. Kliknutím vyberte [normalizovat Data] [ normalize-data] modulu. V **vlastnosti** vyberte **Tanh** pro **metoda transformace** parametru.

1. Klikněte na tlačítko **spustit selektor sloupců**, vyberte "Žádné sloupce" pro **začít s**vyberte **zahrnout** v prvním rozevíracím seznamu vyberte **typ sloupce** v druhé rozevírací seznam a vyberte **číselné** v třetí rozevíracího seznamu. Toto nastavení určuje, že jsou transformovány všechny číselné sloupce (a pouze číslice).

1. Klikněte na znaménko plus (+) napravo od tohoto řádku – tím se vytvoří řádek rozevíracích seznamech. Vyberte **vyloučit** v prvním rozevíracím seznamu vyberte **názvy sloupců** v druhé rozevírací nabídce a zadejte "Úvěrové riziko" v textovém poli. Určuje, že mají být ignorovány úvěrové riziko sloupce (musíte to provést, protože tento sloupec je číselná a proto by je neměli vyloučíte-li transformovat).

1. Klikněte na tlačítko **OK** zaškrtávací políčko.  

    ![Vyberte sloupce pro modul normalizovat Data](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


[Normalizovat Data] [ normalize-data] modulu je nyní nastaven na provedení transformace Tanh pro všechny číselné sloupce kromě sloupce úvěrové riziko.  

## <a name="score-and-evaluate-the-models"></a>Stanovení skóre a vyhodnocení modelů

použití testovacích dat, který byl oddělen navýšení kapacity podle [rozdělení dat] [ split] modulu ke stanovení skóre pro naše trénované modely. pak můžete porovnat výsledky ze dvou modelů zobrazíte, která vygeneruje lepší výsledky.  

### <a name="add-the-score-model-modules"></a>Přidat moduly určení skóre modelu

1. Najít [Score Model] [ score-model] modulu a přetáhněte ji na plátno.

1. Připojení [Train Model] [ train-model] modul, který je připojený k [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] modul na levé straně vstupním portem modulu [Score Model] [ score-model] modulu.

1. Připojit vpravo [spustit skript jazyka R] [ execute-r-script] modulu (našich testovacích dat) na pravé straně vstupním portem modulu [Score Model] [ score-model] modulu.

    ![Připojený modul určení skóre modelu](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   [Score Model] [ score-model] modulu teď můžete vzít platební informace z testovacích dat, spusťte ho pomocí modelu a porovnat předpovědi modelu vygeneruje se sloupcem riziko skutečné kredit v testování dat.

1. Zkopírujte a vložte [Score Model] [ score-model] modul k vytváření druhé kopie.

1. Propojte výstup modulu SVM modelu (to znamená, výstupní port modulu [Train Model] [ train-model] modul, který je připojený k [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modul) ke vstupnímu portu druhého [Score Model] [ score-model] modulu.

1. Pro SVM model budete muset provést stejné transformace na testovací data, jako jste to udělali na trénovací data. Takže zkopírujte a vložte [normalizovat Data] [ normalize-data] modul k vytváření druhé kopie a jejím připojení k pravému [spustit skript jazyka R] [ execute-r-script] modulu.

1. Propojte levý výstup druhého [normalizovat Data] [ normalize-data] modulu na pravé straně vstupním portem modulu druhý [Score Model] [ score-model] modulu.

    ![Obě připojeným modulům určení skóre modelu](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Přidání modulu Evaluate Model

K vyhodnocení dvě bodování výsledky a k jejich porovnání, můžete použít [Evaluate Model] [ evaluate-model] modulu.  

1. Najít [Evaluate Model] [ evaluate-model] modulu a přetáhněte ji na plátno.

1. Připojte výstupní port modulu [Score Model] [ score-model] modulu spojené s modelem Posílený rozhodovací strom levý vstupní port [Evaluate Model] [ evaluate-model] modulu.

1. Připojte druhou [Score Model] [ score-model] vstupním portem modulu na pravé straně.  

    ![Vyhodnocení modelu připojeným](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Spusťte experiment a zkontrolovat výsledky

Spusťte experiment, klikněte na tlačítko **spustit** tlačítko dole na plátně. Může to trvat několik minut. Rotující indikátor na každý modul ukazuje, že je spuštěná a poté zobrazí zelená značka zaškrtnutí dokončení modulu. Když u všech modulů se zaškrtávací políčko, experiment byl ukončen.

Experiment by teď měl vypadat přibližně takto:  

![Vyhodnocení obou modelů](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Pokud chcete zkontrolovat výsledky, klikněte na výstupní port modulu [Evaluate Model] [ evaluate-model] modul a vyberte **vizualizovat**.  

[Evaluate Model] [ evaluate-model] modul vytvoří dvojici křivky a metriky, které můžete porovnat výsledky ze dvou modelů Vyhodnocená. Můžete zobrazit výsledky jako křivky příjemce operátor charakteristiku (roc s více TŘÍDAMI), přesnosti/spojené s vracením křivky nebo výtah křivky. Další data obsahuje chybovou matici kumulativní hodnoty pro oblasti pod křivkou (AUC) a další metriky. Můžete změnit prahovou hodnotu přesunete posuvník doleva nebo doprava a zjistit, jak to ovlivní nastavení metriky.  

Napravo od grafu, klikněte na tlačítko **skóre datovou sadu** nebo **skóre datovou sadu, která porovnání** zvýraznit související křivky a zobrazit související metriky níže. V legendě pro křivky, "Skóre datovou sadu" odpovídá levý vstupní port [Evaluate Model] [ evaluate-model] modul – v našem případě je to model posíleného rozhodovacího stromu. "Skóre datovou sadu, která porovnání" odpovídá pravý vstupní port – model SVM v našem případě. Když kliknete na některý z těchto popisky, křivky pro tento model je zvýrazněn a odpovídající metriky se zobrazí, jak je znázorněno na následujícím obrázku.  

![Křivky roc s více TŘÍDAMI pro modely](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Prozkoumáním těchto hodnot můžete rozhodnout, které model je nejblíže získáte výsledky, které hledáte. Můžete přejít zpět a iterace experimentu změnou hodnoty parametrů v různých modelech. 

Vědy a grafiky interpretace tyto výsledky a optimalizace výkonu modelu je mimo rámec tohoto návodu. Potřebujete další pomoc může přečíst následující články:
- [Jak vyhodnotit výkon modelu ve službě Azure Machine Learning](evaluate-model-performance.md)
- [Vybrat parametry pro optimalizaci algoritmů ve službě Azure Machine Learning](algorithm-parameters-optimize.md)
- [Interpretace výsledků modelu ve službě Azure Machine Learning](interpret-model-results.md)

> [!TIP]
> Pokaždé, když spuštění experimentu záznam danou iteraci, zůstane v historii spuštění. Můžete zobrazit tyto iterace a snadnému návratu na kteroukoli z nich, kliknutím na **zobrazit HISTORII spuštění** dole na plátně. Můžete také kliknout na **předchozího spuštění** v **vlastnosti** se vraťte na iteraci bezprostředně před tím máte otevřený.
> 
> Kliknutím můžete vytvořit kopii kteroukoli iteraci experimentu **uložit jako** dole na plátně. 
> Použít experiment **Souhrn** a **popis** vlastnosti, které chcete mít přehled, co jste se pokusili v iterace experimentu.
> 
> Další informace najdete v tématu [Správa iterací experimentů v nástroji Azure Machine Learning Studio](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste dokončili tyto kroky: 
 
> [!div class="checklist"]
> * Vytvoření experimentu
> * Trénování více modelů
> * Stanovení skóre a vyhodnocení modelů

Teď jste připravení nasadit modely pro tato data.

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