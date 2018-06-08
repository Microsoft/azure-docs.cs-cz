---
title: 'Krok 4: Natrénování a vyhodnocení prediktivní analýzy modely | Microsoft Docs'
description: 'Krok 4 vývoj prediktivního řešení návod: Train, stanovení skóre a vyhodnotit více modely v Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: d905f6b3-9201-4117-b769-5f9ed5ee1cac
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 95937ee74be3f6facbc482c3cfd80a8af9128ea3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836005"
---
# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Krok 4 průvodce: Natrénování a vyhodnocení modelů prediktivní analýzy
Toto téma obsahuje čtvrtý krok tohoto průvodce, [vývoj řešení prediktivní analýzy v Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Vytvoření pracovního prostoru Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Nahrání existujících dat](walkthrough-2-upload-data.md)
3. [Vytvoření nového experimentu](walkthrough-3-create-new-experiment.md)
4. **Natrénování a vyhodnocení modelů**
5. [Nasazení webové služby](walkthrough-5-publish-web-service.md)
6. [Přístup k webové službě](walkthrough-6-access-web-service.md)

- - -
Jednou z výhod používání Azure Machine Learning Studio pro vytváření modelů machine learning je schopnost zkuste více než jeden typ modelu v daný okamžik v jednom experimentu a porovnejte výsledky. Tento typ experimentování vám pomůže najít nejlepší řešení pro váš problém.

V experimentu, který jsme se vývojem v tomto návodu jsme vytvoříte dva různé typy modely a potom porovnejte jejich vyhodnocování výsledky se rozhodnout, který algoritmus chceme používat v našem konečný experiment.  

Existují různé modely, které jsme může vybírat. Chcete-li zobrazit dostupné modely, rozbalte **Machine Learning** uzlu paletě modulů a potom rozbalte **inicializovat Model** a uzly pod ní. Pro účely tohoto experimentu, jsme vyberete [Two-Class Support Vector Machine][two-class-support-vector-machine] (SVM) a [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] moduly.    

> [!TIP]
> Potřebujete pomoc při rozhodování o tom, který algoritmus Machine Learning nejlepší vyhovuje konkrétní problém se snažíte vyřešit, najdete v části [jak zvolit algoritmy pro Microsoft Azure Machine Learning](algorithm-choice.md).
> 
> 

## <a name="train-the-models"></a>Cvičení modely

Obě přidáme [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] modulu a [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modulu v tohoto experimentu.

### <a name="two-class-boosted-decision-tree"></a>Two-Class Boosted Decision Tree

Nejprve nastavíme modelu vylepšené rozhodovací strom.

1. Najít [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] modulu paletě modulů a přetáhněte na plátno.

2. Najít [Train Model] [ train-model] modulu, přetáhněte na plátno a potom se připojte výstup [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] modul vlevo vstupní port [Train Model] [ train-model] modulu.
   
   [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] modulu inicializuje obecné modelu a [trénování modelu] [ train-model] používá Cvičná data pro učení model. 

3. Připojit levém výstup doleva [spustit skript jazyka R][execute-r-script] modulu vpravo vstupní port [Train Model][train-model] \(jsme se rozhodli v modulu [Krok 3](walkthrough-3-create-new-experiment.md) tohoto průvodce používat dat pocházejících z levé strany modulu rozdělení dat pro školení).
   
   > [!TIP]
   > Společnost Microsoft nepotřebuje dvou vstupních hodnot a jeden z výstupů [spustit skript jazyka R] [ execute-r-script] modul pro tento experiment, takže jsme můžete je nechat odpojit. 
   > 
   > 

Tato část experimentu nyní vypadat třeba takto:  

![Cvičení modelu][1]

Nyní potřebujeme říct [Train Model] [ train-model] modulu, že má být model předpovědět úvěrové riziko hodnota.

1. Vyberte [Train Model] [ train-model] modulu. V **vlastnosti** podokně klikněte na tlačítko **spustit selektor sloupců**.

2. V **vyberte jeden sloupec** dialogové okno, zadejte "úvěrového rizika" v poli vyhledávání ve **dostupné sloupce**, vyberte "Úvěrového rizika" níže a klikněte na tlačítko se šipkou vpravo (**>**) přesunout "Úvěrové riziko" do **vybrané sloupce**. 

    ![Vyberte sloupec úvěrové riziko u modulu Train Model][0]

3. Klikněte **OK** zaškrtnutí.

### <a name="two-class-support-vector-machine"></a>Support Vector Machine (SVM) se dvěma třídami

V dalším kroku nastavíme SVM modelu.  

První, trochu vysvětlení SVM. Vylepšené rozhodovací stromy fungují dobře u funkce libovolného typu. Ale vzhledem k tomu, že modul SVM generuje lineární třídění, nemá model, který generuje nejlepší Chyba testu, pokud všechny číselné funkce mají stejné měřítko. Všechny číselné funkce převést na stejné měřítko, používáme transformace "Tanh" (s [normalizaci dat] [ normalize-data] modulu). To transformuje naše čísla do rozsahu [0, 1]. Modul SVM převede řetězec funkce kategorií funkcí a pak na binární funkce 0 nebo 1, proto jsme nemusíte ručně transformace funkce řetězec. Také jsme nechcete transformace sloupci úvěrové riziko (sloupec 21) – je číselné, ale je hodnota jsme se tréninku modelu k předvídání, takže je potřeba ho ponecháte.  

Pokud chcete nastavit SVM modelu, postupujte takto:

1. Najít [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modulu paletě modulů a přetáhněte na plátno.

2. Klikněte pravým tlačítkem myši [Train Model] [ train-model] modulu, vyberte **kopie**a potom klikněte pravým tlačítkem na plátno a vyberte **vložení**. Kopii [Train Model] [ train-model] modul má stejné výběr sloupce jako původní.

3. Připojit výstup [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modulu vlevo vstupní port druhý [Train Model] [ train-model] modulu.

4. Najít [normalizaci dat] [ normalize-data] modulu a přetáhněte na plátno.

5. Připojit levém výstup doleva [spustit skript jazyka R] [ execute-r-script] modulu vstup tohoto modulu (Všimněte si, že na výstupní port modulu může být připojen k více než jeden další modul).

6. Připojit levý výstupní port modulu [normalizaci dat] [ normalize-data] modulu vpravo vstupní port druhý [Train Model] [ train-model] modulu.

Tato část naší experiment by teď měl vypadat přibližně takto:  

![Cvičení druhý modelu][2]  

Teď nakonfigurovat [normalizaci dat] [ normalize-data] modul:

1. Kliknutím vyberte [normalizaci dat] [ normalize-data] modulu. V **vlastnosti** podokně, vyberte **Tanh** pro **transformace metoda** parametr.

2. Klikněte na tlačítko **spustit selektor sloupců**, vyberte možnost "Žádné sloupce" pro **začít s**, vyberte **zahrnout** v první rozevíracího seznamu, vyberte **typ sloupce** v druhé rozevírací seznam a vyberte **číselné** v třetí rozevírací nabídce. Určuje transformaci všechny číselné sloupce (a pouze číslice).

3. Kliknutím na symbol plus (+) napravo od tento řádek – tím se vytvoří řádek rozevíracích seznamů. Vyberte **vyloučit** v první rozevíracího seznamu, vyberte **názvy sloupců** v druhé rozevírací nabídce a zadejte "Úvěrového rizika" v textové pole. Určuje, že sloupec úvěrové riziko třeba ji ignorovat (je potřeba to udělat, protože tento sloupec je číselná a proto by transformován Pokud jsme ho nebylo vyloučit).

4. Klikněte **OK** zaškrtnutí.  

    ![Vyberte sloupce pro modul normalizaci dat][5]

[Normalizaci dat] [ normalize-data] modulu je teď nastavená na provedení Tanh transformace na všechny číselné sloupce s výjimkou sloupci úvěrové riziko.  

## <a name="score-and-evaluate-the-models"></a>Stanovení skóre a vyhodnocení modelů

Používáme testování data, která byla oddělených se [rozdělení dat] [ split] modul skóre pro naše trénované modely. Jsme pak můžete porovnat výsledky dva modely zobrazíte, který generuje lepší výsledky.  

### <a name="add-the-score-model-modules"></a>Přidání modulů Score Model

1. Najít [Score Model] [ score-model] modulu a přetáhněte na plátno.

2. Připojení [Train Model] [ train-model] modul, který je připojen k [Two-Class Boosted Decision Tree] [ two-class-boosted-decision-tree] modulu vlevo vstupní port [Score Model] [ score-model] modulu.

3. Připojit právo [spustit skript jazyka R] [ execute-r-script] modulu (naše testování data) napravo vstupní port [Score Model] [ score-model] modulu.

    ![Modul určení skóre modelu připojení][6]
   
   [Score Model] [ score-model] modulu teď můžete provést platební informace z testovacích dat, spusťte ho pomocí modelu a porovnat předpovědi modelu generuje se sloupci skutečné platební riziko v testování data.

4. Zkopírujte a vložte [Score Model] [ score-model] modul k vytváření druhé kopie.

5. Připojit výstup SVM modelu (to znamená, výstupní port modulu [Train Model] [ train-model] modul, který je připojen k [Two-Class Support Vector Machine] [ two-class-support-vector-machine] modulu) pro vstupní port druhý [Score Model] [ score-model] modulu.

6. V případě modelu SVM máme provést stejné transformace na testovací data, jako jsme to udělali pro Cvičná data. Proto zkopírujte a vložte [normalizaci dat] [ normalize-data] modul k vytváření druhé kopie a připojte ho k právo [spustit skript jazyka R] [ execute-r-script] modulu.

7. Připojit levý výstupní druhého [normalizaci dat] [ normalize-data] modulu vpravo vstupní port druhý [Score Model] [ score-model] modulu.

    ![Obě Score Model moduly připojení][7]

### <a name="add-the-evaluate-model-module"></a>Přidání modulu Evaluate Model

K vyhodnocení oba vyhodnocování výsledky a porovnat je, používáme [Evaluate Model] [ evaluate-model] modulu.  

1. Najít [Evaluate Model] [ evaluate-model] modulu a přetáhněte na plátno.

2. Připojit výstupní port modulu [Score Model] [ score-model] modulu přidruženého k modelu vylepšené rozhodovací strom pro levý vstupní port [Evaluate Model] [ evaluate-model] modulu.

3. Připojení dalších [Score Model] [ score-model] modulu vpravo vstupní port.  

    ![Vyhodnocení modelu modulu připojení][8]

### <a name="run-the-experiment-and-check-the-results"></a>Spusťte experiment a Kontrola výsledků

Chcete-li spustit experiment, klikněte na tlačítko **spustit** tlačítko níže na plátno. To může trvat několik minut. Na roztočený ukazatel na každý modul ukazuje, zda je spuštěná a poté zobrazí zelená značka zaškrtnutí po dokončení modulu. Pokud všechny moduly mají zaškrtnuto, experimentu bylo dokončeno.

Experiment by teď měl vypadat přibližně takto:  

![Vyhodnocení obou modelů][3]

Pokud chcete zkontrolovat výsledky, klikněte na výstupní port modulu [Evaluate Model] [ evaluate-model] modul a vyberte **vizualizovat**.  

[Evaluate Model] [ evaluate-model] modulu vytvoří dvojici křivek a metriky, které vám umožní porovnat výsledky dva scored modely. Můžete zobrazit výsledky jako křivek příjemce operátor vlastnosti (ROC), křivek přesnost nebo odvolání nebo křivek navýšení. Další data zobrazí zahrnuje matice nejasnostem, kumulativní hodnoty pro oblasti pod křivky (AUC) a další metriky. Můžete změnit prahovou hodnotu posunutím jezdce doleva nebo doprava a v tématu Jak ovlivňuje sadu metriky.  

Napravo od grafu, klikněte na tlačítko **skóre pro Magnitudu datovou sadu** nebo **skóre pro Magnitudu datovou sadu, která porovnat** zvýrazněte přidružené křivky a zobrazení přidružené metriky níže. V legendě pro křivky "Skóre pro Magnitudu datovou sadu" odpovídá levý vstupní port [Evaluate Model] [ evaluate-model] modul – v našem případě je to modelu vylepšené rozhodovací strom. "Skóre pro magnitudu datovou sadu, která porovnat" odpovídá pravý vstupní port - SVM modelu v našem případě. Když kliknete na jednu z těchto popisky, křivky pro tento model je označený a odpovídající metriky se zobrazí, jak je znázorněno na následujícím obrázku.  

![Křivek ROC pro modely][4]

Prověřením tyto hodnoty můžete rozhodnout, které model je nejblíže k budete výsledků, které hledáte. Můžete se vrátit zpět a iterovat experimentu změnou hodnoty parametrů v různých modelech. 

Vědecké účely a obrázky interpretace těchto výsledků a ladění výkonu modelu je mimo rámec tohoto návodu. Potřebujete další pomoc můžou číst v následujících článcích:
- [Postup vyhodnocení modelu výkon v Azure Machine Learning](evaluate-model-performance.md)
- [Vyberte parametry pro Optimalizujte algoritmy v Azure Machine Learning](algorithm-parameters-optimize.md)
- [Interpretovat výsledky modelu v Azure Machine Learning](interpret-model-results.md)

> [!TIP]
> Při každém spuštění záznam této iterace experimentu je uchovat v historii spustit. Můžete zobrazit tyto iterací a vrátit zpět do některé z nich, klepnutím **zobrazit HISTORII BĚHŮ** níže na plátno. Můžete také kliknout na **předchozí spustit** v **vlastnosti** podokno a vraťte iterace bezprostředně předcházející ten budete mít otevřete.
> 
> Kliknutím můžete vytvořit kopii kteroukoli iteraci experimentu **uložit jako** níže na plátno. 
> Použít experiment **Souhrn** a **popis** vlastnosti, které chcete uchovávat informace o jste zkusili ve vašem iterací experimentu.
> 
> Další podrobnosti najdete v tématu [Správa iterací experimentu v nástroji Azure Machine Learning Studio](manage-experiment-iterations.md).  
> 
> 

- - -
**Další krok: [nasazení webové služby](walkthrough-5-publish-web-service.md)**

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
