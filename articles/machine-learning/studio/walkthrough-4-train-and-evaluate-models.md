---
title: 'Krok 4: Trénování modelů prediktivní analýzy - Azure Machine Learning Studio | Dokumentace Microsoftu'
description: 'Vývoj prediktivního řešení návod krok 4: Trénování, stanovení skóre a vyhodnotit několik modelů v Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.author: garye
editor: cgronlun
ms.assetid: d905f6b3-9201-4117-b769-5f9ed5ee1cac
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 2464ef0ba89f138834bedfdc9f29347092e5fe99
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53254509"
---
# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models-in-machine-learning-studio"></a>Krok 4 průvodce: Natrénování a vyhodnocení modelů prediktivní analýzy v nástroji Machine Learning Studio
Toto téma obsahuje návod, čtvrtý krok [vývoj řešení prediktivní analýzy v Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Vytvoření pracovního prostoru Machine Learning Studio](walkthrough-1-create-ml-workspace.md)
2. [Nahrání existujících dat](walkthrough-2-upload-data.md)
3. [Vytvoření nového experimentu](walkthrough-3-create-new-experiment.md)
4. **Natrénování a vyhodnocení modelů**
5. [Nasazení webové služby](walkthrough-5-publish-web-service.md)
6. [Přístup k webové službě](walkthrough-6-access-web-service.md)

- - -
Jednou z výhod pro vytváření modelů strojového učení pomocí Azure Machine Learning Studio je schopnost zkuste více než jeden typ modelu v daný okamžik v jednom experimentu a porovnejte výsledky. Tento typ služby experimentování ve službě vám pomůže najít nejlepší řešení pro váš problém.

V experiment, který vyvíjíme v tomto názorném postupu vytvoříme dva různé typy modelů a porovnejte jejich vyhodnocování výsledky se rozhodnout, které algoritmus chceme použít v našich konečný experiment.  

Existují různé modely, které jsme mohli vybírat. Pokud chcete zobrazit dostupné modely, rozbalte **Machine Learning** uzel paletě modulů a potom rozbalte **inicializovat Model** a uzlů pod ním. Pro účely tohoto experimentu, jsme vyberete [Two-Class Support Vector Machine][two-class-support-vector-machine] (SVM) a [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] moduly.    

> [!TIP]
> Nechte si poradit který nejlepší algoritmus Machine Learning vyhovuje konkrétního problému, který se snažíte vyřešit, najdete v článku [jak zvolit algoritmy pro Microsoft Azure Machine Learning Studio](algorithm-choice.md).
> 
> 

## <a name="train-the-models"></a>Trénování modelů

Přidáme i [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] modulu a [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modulu v tohoto experimentu.

### <a name="two-class-boosted-decision-tree"></a>Two-Class posíleného rozhodovacího stromu

Nejprve vytvoříme model posíleného rozhodovacího stromu.

1. Najít [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] modulu paletě modulů a přetáhněte ji na plátno.

2. Najít [Train Model] [ train-model] modulu, přetáhněte na plátno a potom propojte výstup modulu [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] modul na levé straně vstupním portem modulu [Train Model] [ train-model] modulu.
   
   [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] inicializuje modul obecný model a [trénování modelu] [ train-model] používá trénovacích dat pro trénování model. 

3. Připojit levém výstup doleva [spustit skript jazyka R][execute-r-script] modulu vpravo vstupní port [Train Model][train-model] \(jsme se rozhodli v modulu [Krok 3](walkthrough-3-create-new-experiment.md) tohoto průvodce používat dat pocházejících z levé strany modulu rozdělení dat pro školení).
   
   > [!TIP]
   > Nebudeme potřebovat dvou vstupů a jeden z výstupů [spustit skript jazyka R] [ execute-r-script] modul pro tento experiment, takže jsme můžete je nechat nepřipojené. 
   > 
   > 

Tato část experiment teď vypadá přibližně takto:  

![Trénování modelu][1]

Teď potřebujeme říct [Train Model] [ train-model] modulu, že má být model pro předpověď úvěrového rizika hodnotu.

1. Vyberte [Train Model] [ train-model] modulu. V **vlastnosti** podokně klikněte na tlačítko **spustit selektor sloupců**.

2. V **zvolte jeden sloupec** dialogové okno, zadejte vyhledávacího pole v části "úvěrové riziko" **dostupných sloupců**, vyberte "Úvěrové riziko" níže a klikněte na tlačítko se šipkou doprava (**>**) přesunete "Úvěrové riziko" **vybrané sloupce**. 

    ![Vyberte sloupec úvěrové riziko pro modul trénování modelu][0]

3. Klikněte na tlačítko **OK** zaškrtávací políčko.

### <a name="two-class-support-vector-machine"></a>Support Vector Machine (SVM) se dvěma třídami

V dalším kroku nastavíme SVM modelu.  

První, trochu vysvětlení SVM. Posílený rozhodovací stromy dobře fungují s funkcí libovolného typu. Protože SVM modul generuje lineární třídění, model, který generuje má ale nejlepší Chyba testu při všechny číselné funkce mají stejné měřítko. Převést všechny číselné funkce na stejném měřítku, použijeme transformace "Tanh" (s [normalizovat Data] [ normalize-data] modul). To transformuje naše čísla v rozsahu [0, 1]. Modulu SVM převede řetězec funkce na zařazené do kategorií funkce a potom na binární funkce 0 nebo 1, proto nepotřebujeme ručně transformace funkce řetězec. Navíc nechceme transformace úvěrové riziko column (sloupec 21) – je číselná, ale jedná se o hodnotu jsme supervizí trénujete model k predikci, takže potřebujeme ponechte samostatně.  

Nastavení modelu SVM, postupujte takto:

1. Najít [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modulu paletě modulů a přetáhněte ji na plátno.

2. Klikněte pravým tlačítkem na [Train Model] [ train-model] modulu, vyberte **kopírování**a potom klikněte pravým tlačítkem na plátně a vyberte možnost **vložit**. Kopii [Train Model] [ train-model] modul má stejné výběr sloupce jako původní.

3. Propojte výstup modulu [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modul na levé straně vstupním portem modulu druhý [Train Model] [ train-model] modulu.

4. Najít [normalizovat Data] [ normalize-data] modulu a přetáhněte ji na plátno.

5. Propojte levý výstup levé straně [spustit skript jazyka R] [ execute-r-script] tohoto modulu (Všimněte si, že na výstupní port modulu může být připojen k více než jeden další modul) se vstupem modulu.

6. Propojte levý výstupní port modulu [normalizovat Data] [ normalize-data] modulu na pravé straně vstupním portem modulu druhý [Train Model] [ train-model] modulu.

Tato část naší experiment by teď měl vypadat přibližně takto:  

![Trénujete model pro druhý][2]  

Teď nakonfigurovat [normalizovat Data] [ normalize-data] modul:

1. Kliknutím vyberte [normalizovat Data] [ normalize-data] modulu. V **vlastnosti** vyberte **Tanh** pro **metoda transformace** parametru.

2. Klikněte na tlačítko **spustit selektor sloupců**, vyberte "Žádné sloupce" pro **začít s**vyberte **zahrnout** v prvním rozevíracím seznamu vyberte **typ sloupce** v druhé rozevírací seznam a vyberte **číselné** v třetí rozevíracího seznamu. Toto nastavení určuje, že jsou transformovány všechny číselné sloupce (a pouze číslice).

3. Klikněte na znaménko plus (+) napravo od tohoto řádku – tím se vytvoří řádek rozevíracích seznamech. Vyberte **vyloučit** v prvním rozevíracím seznamu vyberte **názvy sloupců** v druhé rozevírací nabídce a zadejte "Úvěrové riziko" v textovém poli. Určuje, že mají být ignorovány úvěrové riziko sloupce (musíme udělat, protože tento sloupec je číselná a proto by transformuje, pokud jsme neměli vyloučit).

4. Klikněte na tlačítko **OK** zaškrtávací políčko.  

    ![Vyberte sloupce pro modul normalizovat Data][5]

[Normalizovat Data] [ normalize-data] modulu je nyní nastaven na provedení transformace Tanh pro všechny číselné sloupce kromě sloupce úvěrové riziko.  

## <a name="score-and-evaluate-the-models"></a>Stanovení skóre a vyhodnocení modelů

Pomocí testovacích dat, který byl rozdělen podle [rozdělení dat] [ split] modulu ke stanovení skóre pro naše trénované modely. My pak můžete porovnat výsledky ze dvou modelů zobrazíte, která vygeneruje lepší výsledky.  

### <a name="add-the-score-model-modules"></a>Přidat moduly určení skóre modelu

1. Najít [Score Model] [ score-model] modulu a přetáhněte ji na plátno.

2. Připojení [Train Model] [ train-model] modul, který je připojený k [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] modul na levé straně vstupním portem modulu [Score Model] [ score-model] modulu.

3. Připojit vpravo [spustit skript jazyka R] [ execute-r-script] modulu (našich testovacích dat) na pravé straně vstupním portem modulu [Score Model] [ score-model] modulu.

    ![Připojený modul určení skóre modelu][6]
   
   [Score Model] [ score-model] modulu teď můžete vzít platební informace z testovacích dat, spusťte ho pomocí modelu a porovnat předpovědi modelu vygeneruje se sloupcem riziko skutečné kredit v testování dat.

4. Zkopírujte a vložte [Score Model] [ score-model] modul k vytváření druhé kopie.

5. Propojte výstup modulu SVM modelu (to znamená, výstupní port modulu [Train Model] [ train-model] modul, který je připojený k [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modul) ke vstupnímu portu druhého [Score Model] [ score-model] modulu.

6. SVM modelu musíme provést stejnou transformaci na testovací data, jako jsme to udělali na trénovací data. Takže zkopírujte a vložte [normalizovat Data] [ normalize-data] modul k vytváření druhé kopie a jejím připojení k pravému [spustit skript jazyka R] [ execute-r-script] modulu.

7. Propojte levý výstup druhého [normalizovat Data] [ normalize-data] modulu na pravé straně vstupním portem modulu druhý [Score Model] [ score-model] modulu.

    ![Obě připojeným modulům určení skóre modelu][7]

### <a name="add-the-evaluate-model-module"></a>Přidání modulu Evaluate Model

K vyhodnocení dvě bodování výsledky a k jejich porovnání, použijeme [Evaluate Model] [ evaluate-model] modulu.  

1. Najít [Evaluate Model] [ evaluate-model] modulu a přetáhněte ji na plátno.

2. Připojte výstupní port modulu [Score Model] [ score-model] modulu spojené s modelem Posílený rozhodovací strom levý vstupní port [Evaluate Model] [ evaluate-model] modulu.

3. Připojte druhou [Score Model] [ score-model] vstupním portem modulu na pravé straně.  

    ![Vyhodnocení modelu připojeným][8]

### <a name="run-the-experiment-and-check-the-results"></a>Spusťte experiment a zkontrolovat výsledky

Spusťte experiment, klikněte na tlačítko **spustit** tlačítko dole na plátně. Může to trvat několik minut. Rotující indikátor na každý modul ukazuje, že je spuštěná a poté zobrazí zelená značka zaškrtnutí dokončení modulu. Když u všech modulů se zaškrtávací políčko, experiment byl ukončen.

Experiment by teď měl vypadat přibližně takto:  

![Vyhodnocení obou modelů][3]

Pokud chcete zkontrolovat výsledky, klikněte na výstupní port modulu [Evaluate Model] [ evaluate-model] modul a vyberte **vizualizovat**.  

[Evaluate Model] [ evaluate-model] modul vytvoří dvojici křivky a metriky, které můžete porovnat výsledky ze dvou modelů Vyhodnocená. Můžete zobrazit výsledky jako křivky příjemce operátor charakteristiku (roc s více TŘÍDAMI), přesnosti/spojené s vracením křivky nebo výtah křivky. Další data obsahuje chybovou matici kumulativní hodnoty pro oblasti pod křivkou (AUC) a další metriky. Můžete změnit prahovou hodnotu přesunete posuvník doleva nebo doprava a zjistit, jak to ovlivní nastavení metriky.  

Napravo od grafu, klikněte na tlačítko **skóre datovou sadu** nebo **skóre datovou sadu, která porovnání** zvýraznit související křivky a zobrazit související metriky níže. V legendě pro křivky, "Skóre datovou sadu" odpovídá levý vstupní port [Evaluate Model] [ evaluate-model] modul – v našem případě je to model posíleného rozhodovacího stromu. "Skóre datovou sadu, která porovnání" odpovídá pravý vstupní port – model SVM v našem případě. Když kliknete na některý z těchto popisky, křivky pro tento model je zvýrazněn a odpovídající metriky se zobrazí, jak je znázorněno na následujícím obrázku.  

![Křivky roc s více TŘÍDAMI pro modely][4]

Prozkoumáním těchto hodnot můžete rozhodnout, které model je nejblíže získáte výsledky, které hledáte. Můžete přejít zpět a iterace experimentu změnou hodnoty parametrů v různých modelech. 

Vědy a grafiky interpretace tyto výsledky a optimalizace výkonu modelu sahá nad rámec tohoto návodu. Potřebujete další pomoc může přečíst následující články:
- [Jak vyhodnotit výkon modelu ve službě Azure Machine Learning](evaluate-model-performance.md)
- [Vybrat parametry pro optimalizaci algoritmů ve službě Azure Machine Learning](algorithm-parameters-optimize.md)
- [Interpretace výsledků modelu ve službě Azure Machine Learning](interpret-model-results.md)

> [!TIP]
> Pokaždé, když spuštění experimentu záznam danou iteraci, zůstane v historii spuštění. Můžete zobrazit tyto iterace a snadnému návratu na kteroukoli z nich, kliknutím na **zobrazit HISTORII spuštění** dole na plátně. Můžete také kliknout na **předchozího spuštění** v **vlastnosti** se vraťte na iteraci bezprostředně před tím máte otevřený.
> 
> Kliknutím můžete vytvořit kopii kteroukoli iteraci experimentu **uložit jako** dole na plátně. 
> Použít experiment **Souhrn** a **popis** vlastnosti, které chcete mít přehled, co jste se pokusili v iterace experimentu.
> 
> Další podrobnosti najdete v tématu [Správa iterací experimentů v nástroji Azure Machine Learning Studio](manage-experiment-iterations.md).  
> 
> 

- - -
**Další: [Nasazení webové služby](walkthrough-5-publish-web-service.md)**

[0]: ./media/walkthrough-4-train-and-evaluate-models/train-model-select-column.png
[1]: ./media/walkthrough-4-train-and-evaluate-models/experiment-with-train-model.png
[2]: ./media/walkthrough-4-train-and-evaluate-models/svm-model-added.png
[3]: ./media/walkthrough-4-train-and-evaluate-models/final-experiment.png
[4]: ./media/walkthrough-4-train-and-evaluate-models/roc-curves.png
[5]: ./media/walkthrough-4-train-and-evaluate-models/normalize-data-select-column.png
[6]: ./media/walkthrough-4-train-and-evaluate-models/score-model-connected.png
[7]: ./media/walkthrough-4-train-and-evaluate-models/both-score-models-added.png
[8]: ./media/walkthrough-4-train-and-evaluate-models/evaluate-model-added.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
