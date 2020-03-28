---
title: 'Kurz 1: Předvídání úvěrového rizika'
titleSuffix: ML Studio (classic) - Azure
description: Podrobný kurz, který ukazuje, jak vytvořit prediktivní analytické řešení pro hodnocení úvěrového rizika v Azure Machine Learning Studio (klasické). Tento kurz je první částí třídílné série kurzů.  Ukazuje, jak vytvořit pracovní prostor, nahrát data a vytvořit experiment.
keywords: úvěrové riziko,řešení prediktivní analýzy,posouzení rizika
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 6fd8573c78d80c950bdeb41ec01e2835def3979a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79204252"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio-classic"></a>Kurz 1: Předvídání úvěrového rizika – Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

V tomto kurzu se podrobněpodíváte na proces vývoje řešení prediktivní analýzy. Jednoduchý model vyvíjíte ve Strojovém učení (klasické).  Potom nasadit model jako webovou službu Azure Machine Learning.  Tento nasazený model můžete provést předpovědi pomocí nových dat. Tento výukový program je **první částí třídílné série kurzů**.

Předpokládejme, že potřebujete předpovědět úvěrové riziko u jednotlivých zákazníků na základě údajů, které uvedli v žádosti o úvěr.  

Hodnocení úvěrového rizika je složitý problém, ale tento výukový program to trochu zjednoduší. Použijete ji jako příklad toho, jak můžete vytvořit prediktivní analytické řešení pomocí Microsoft Azure Machine Learning Studio (klasické). Pro toto řešení budete používat Azure Machine Learning Studio (klasické) a webovou službu Machine Learning.  

V tomto třídílném kurzu začnete s veřejně dostupnými údaji o úvěrovém riziku.  Potom vyvinout a trénovat prediktivní model.  Nakonec nasadit model jako webovou službu.

V této části tutoriálu si: 
 
> [!div class="checklist"]
> * Vytvoření pracovního prostoru machine learningového studia (klasického)
> * Nahrání existujících dat
> * Vytvoření experimentu

Tento experiment pak můžete použít k [trénování modelů v části 2](tutorial-part2-credit-risk-train.md) a potom je [nasadit v části 3](tutorial-part3-credit-risk-deploy.md).

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že jste použili Machine Learning Studio (klasické) alespoň jednou předtím a že máte nějaké znalosti konceptů strojového učení. Bere ale v úvahu, že nejste odborníkem ani na jedno.

Pokud jste nikdy předtím **nepoužívali Azure Machine Learning Studio (klasické),** možná budete chtít začít s rychlým startem, [Vytvořte svůj první experiment datové vědy v Azure Machine Learning Studio (klasické)](create-experiment.md). Rychlý start vás poprvé provede machine learningovým studiem (klasika). Ukáže vám základy toho, jak pomocí myši přetáhnout moduly do experimentu, vzájemně je propojit, spustit experiment a prohlédnout si výsledky.


> [!TIP] 
> Pracovní kopii experimentu, který vyvíjíte, najdete v tomto kurzu v [Galerii AI Azure](https://gallery.azure.ai). Přejděte do **[výukového programu – předvídejte úvěrové riziko](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** a kliknutím na **otevřít ve studiu** stáhněte kopii experimentu do pracovního prostoru Machine Learning Studio (klasické).
> 


## <a name="create-a-machine-learning-studio-classic-workspace"></a>Vytvoření pracovního prostoru machine learningového studia (klasického)

Chcete-li používat Machine Learning Studio (klasické), musíte mít pracovní prostor Microsoft Azure Machine Learning Studio (klasické). Tento pracovní prostor obsahuje nástroje potřebné k vytváření, správě a publikování experimentů.  

Pokud chcete vytvořit pracovní prostor, přečtěte si část [Vytvoření a sdílení pracovního prostoru Azure Machine Learning Studio (klasické).](create-workspace.md)

Po vytvoření pracovního prostoru otevřete Machine Learning[https://studio.azureml.net/Home](https://studio.azureml.net/Home)Studio (classic) ( ). Pokud máte více než jednu pracovní plochu, můžete vybrat pracovní plochu na panelu nástrojů v pravém horním rohu okna.

![Výběr pracovního prostoru ve studiu (klasický)](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> Pokud jste vlastníkem pracovního prostoru, můžete experimenty, na kterých pracujete, sdílet tak, že do pracovního prostoru pozvete ostatní. Můžete to udělat v Machine Learning Studio (klasické) na stránce **NASTAVENÍ.** Potřebujete pouze účet Microsoft nebo účet organizace pro každého uživatele.
> 
> Na stránce **NASTAVENÍ** klikněte na **UŽIVATELÉ**a v dolní části okna klikněte na **Pozvat další uživatele.**
> 

## <a name="upload-existing-data"></a><a name="upload"></a>Nahrání existujících dat

Chcete-li vytvořit prediktivní model pro úvěrové riziko, potřebujete data, která můžete použít k trénování a testování modelu. Pro účely tohoto kurzu budete používat "UCI Statlog (německá kreditní data) Data Set" z úložiště UC Irvine Machine Learning. Najdete ji zde:  
<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Budete používat soubor s názvem **german.data**. Stáhněte si tento soubor na místní pevný disk.  

Datová sada **german.data** obsahuje řádky 20 proměnných pro 1000 minulých žadatelů o úvěr. Těchto 20 proměnných představuje sadu funkcí datové sady *(vektor funkce),* která poskytuje identifikační charakteristiky pro každého žadatele o úvěr. Další sloupec v každém řádku představuje vypočtené úvěrové riziko žadatele, přičemž 700 žadatelů bylo označeno jako nízké úvěrové riziko a 300 jako vysoké riziko.

Web UCI poskytuje popis atributů vektoru funkce pro tato data. Tyto údaje zahrnují finanční informace, úvěrovou historii, stav zaměstnání a osobní údaje. U každého žadatele bylo uděleno binární hodnocení udávající, zda se jedná o nízké nebo vysoké úvěrové riziko. 

Tato data použijete k trénování modelu prediktivní analýzy. Po dokončení by měl být váš model schopen přijmout vektor funkce pro nového jedince a předpovědět, zda se jedná o nízké nebo vysoké úvěrové riziko.  

Tady je zajímavý zvrat.

Popis datové sady na webových stránkách UCI uvádí, co to stojí, pokud nesprávně klasifikovat osoby úvěrové riziko.
Pokud model předpovídá vysoké úvěrové riziko pro někoho, kdo je ve skutečnosti nízké úvěrové riziko, model provedl chybnou klasifikaci.

Obrácená chybná klasifikace je však pro finanční instituci pětkrát nákladnější: pokud model předpovídá nízké úvěrové riziko pro někoho, kdo je ve skutečnosti vysokým úvěrovým rizikem.

Takže chcete trénovat model tak, aby náklady na tento druhý typ chybné klasifikace je pětkrát vyšší než chybné klasifikace jiným způsobem.

Jeden jednoduchý způsob, jak to udělat při trénování modelu v experimentu je duplikování (pětkrát) ty položky, které představují někoho s vysokým úvěrovým rizikem. 

Potom pokud model chybně klasifikuje někoho jako nízké úvěrové riziko, když jsou ve skutečnosti vysoké riziko, model dělá stejné chybně klasifikace pětkrát, jednou za každý duplikát. Tím se zvýší náklady na tuto chybu ve výsledcích školení.


### <a name="convert-the-dataset-format"></a>Převedení formátu datové sady

Původní datová sada používá prázdný oddělený formát. Machine Learning Studio (klasické) funguje lépe se souborem csv (odděleným čárkami), takže převedete datovou sadu nahrazením mezer čárkami.  

Existuje mnoho způsobů, jak převést tato data. Jedním ze způsobů je použití následujícího příkazu prostředí Windows PowerShell:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Dalším způsobem je pomocí příkazu Unix sed:  

    sed 's/ /,/g' german.data > german.csv  

V obou případech jste vytvořili verzi dat oddělenou čárkou v souboru s názvem **german.csv,** který můžete použít v experimentu.

### <a name="upload-the-dataset-to-machine-learning-studio-classic"></a>Nahrání datové sady do Machine Learning Studia (klasické)

Po převodu dat do formátu CSV je třeba je nahrát do machine learningového studia (klasické). 

1. Otevřete domovskou stránku Machine[https://studio.azureml.net](https://studio.azureml.net)Learning Studio (klasická) ( ). 

2. Klikněte ![na](./media/tutorial-part1-credit-risk/menu.png) nabídku Menu v levém horním rohu okna, klikněte na **Azure Machine Learning**, vyberte **Studio**a přihlaste se.

3. Klikněte na **+NOVÝ** v dolní části okna.

4. Vyberte **položku DATASET**.

5. Vyberte **z místního souboru**.

    ![Přidání datové sady z místního souboru](./media/tutorial-part1-credit-risk/add-dataset.png)

6. V **dialogovém okně Nahrát novou datovou sadu** klikněte na Procházet a najděte soubor **german.csv,** který jste vytvořili.

7. Zadejte název datové sady. Pro tento kurz jej nazvěme "UCI německé údaje o kreditní kartě".

8. Pro datový typ vyberte **obecný soubor CSV bez záhlaví (.nh.csv)**.

9. Pokud chcete, přidejte popis.

10. Klikněte na značku **OK.**  

    ![Nahrání datové sady](./media/tutorial-part1-credit-risk/upload-dataset.png)

Tím se data nahrají do modulu datové sady, který můžete použít v experimentu.

Datové sady, které jste nahráli do Studia (klasické), můžete spravovat kliknutím na kartu **DATASETS** nalevo od okna Studio (klasické).

![Správa datových sad](./media/tutorial-part1-credit-risk/dataset-list.png)

Další informace o importu jiných typů dat do experimentu najdete v tématu [Import trénovacích dat do Azure Machine Learning Studio (klasické).](import-data.md)

## <a name="create-an-experiment"></a>Vytvoření experimentu

Dalším krokem v tomto kurzu je vytvoření experimentu v Machine Learning Studio (klasické), který používá datovou sadu, kterou jste nahráli.  

1. Ve studiu (klasika) klikněte v dolní části okna na **+NEW.**
1. Vyberte **EXPERIMENT**a pak vyberte "Prázdný experiment". 

    ![Vytvoření nového experimentu](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. Vhorní části plátna vyberte výchozí název experimentu a přejmenujte ho na něco smysluplného.

    ![Přejmenovat experiment](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > Je vhodné vyplnit **souhrn** a **popis** experimentu v podokně **Vlastnosti.** Tyto vlastnosti vám dávají možnost dokumentovat experiment tak, aby každý, kdo se na něj později podívá, pochopil vaše cíle a metodiku.
   > 
   > ![Vlastnosti experimentu](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. V paletě modulů nalevo od plátna experimentu rozbalte **položku Uložené datové sady**.
1. Najděte datovou sadu, kterou jste vytvořili v části **Moje datové sady,** a přetáhněte ji na plátno. Datovou sadu můžete také najít zadáním názvu do **vyhledávacího** pole nad paletou.  

    ![Přidání datové sady do experimentu](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>Příprava dat

Můžete zobrazit prvních 100 řádků dat a některé statistické informace pro celou datovou sadu: Klepněte na výstupní port datové sady (malý kruh v dolní části) a vyberte **možnost Vizualizovat**.  

Vzhledem k tomu, že datový soubor nebyl dodáván s záhlavími sloupců, studio (klasické) poskytlo obecné nadpisy (Col1, Col2 *atd.).* Dobré nadpisy nejsou nezbytné pro vytvoření modelu, ale usnadňují práci s daty v experimentu. Také při publikování tohoto modelu ve webové službě, nadpisy pomáhají identifikovat sloupce pro uživatele služby.  

Záhlaví sloupců můžete přidat pomocí modulu [Upravit metadata.][edit-metadata]

Modul [Upravit metadata][edit-metadata] slouží ke změně metadat přidružených k datové sadě. V takovém případě jej použijete k poskytnutí popisnějších názvů pro záhlaví sloupců. 

Chcete-li použít [metadata úprav][edit-metadata], nejprve určete, které sloupce chcete upravit (v tomto případě všechny).) Dále určíte akci, která má být provedena u těchto sloupců (v tomto případě změna záhlaví sloupců.)

1. Do palety modulů zadejte do pole **Hledat** "metadata". V seznamu modulů se zobrazí [metadata úprav.][edit-metadata]

1. Klikněte a přetáhněte modul [Upravit metadata][edit-metadata] na plátno a přetáhněte ho pod datovou sadu, kterou jste přidali dříve.

1. Připojte datovou sadu k [metadatům úprav][edit-metadata]: klepněte na výstupní port datové sady (malý kruh v dolní části datové sady), přetáhněte na vstupní port [Upravit metadata][edit-metadata] (malý kruh v horní části modulu) a poté uvolněte tlačítko myši. Datová sada a modul zůstávají připojeny i v případě, že se pohybujete na plátně.
 
    Experiment by nyní měl vypadat nějak takto:  

    ![Přidání metadat pro úpravy](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    Červený vykřičník označuje, že jste ještě nenastavili vlastnosti tohoto modulu. Uděláte to za chvíli.

    > [!TIP]
    > Kliknutím dvakrát na modul a zadáním textu je možné přidat k modulu komentář. To vám může pomoci rychle poznat, jaký je účel modulu v experimentu. V takovém případě poklepejte na modul [Upravit metadata][edit-metadata] a zadejte komentář "Přidat záhlaví sloupců". Kliknutím na libovolné místo na plátně zavřete textové pole. Chcete-li komentář zobrazit, klikněte na šipku dolů v modulu.
    > 
    > ![Modul Upravit metadata s přidaným komentářem](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. Vyberte [Upravit metadata][edit-metadata]a v podokně **Vlastnosti** napravo od plátna klikněte na **Vybrat volič sloupce spuštění**.

1. V dialogovém okně **Vybrat sloupce** vyberte všechny řádky v **dostupných sloupcích** a klepnutím na > je přesuňte do **vybraných sloupců**.
   Dialogové okno by mělo vypadat takto:

   ![Výběr sloupců se všemi vybranými sloupci](./media/tutorial-part1-credit-risk/select-columns.png)


1. Klikněte na značku **OK.**

1. V podokně **Vlastnosti** vyhledejte parametr **Nové názvy sloupců.** Do tohoto pole zadejte seznam názvů 21 sloupců v datové sadě oddělených čárkami a v pořadí sloupců. Názvy sloupců můžete získat z dokumentace k datové sadě na webu UCI nebo pro pohodlí můžete zkopírovat a vložit následující seznam:  

   ```   
   Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   ```

   Podokno Vlastnosti vypadá takto:

   ![Vlastnosti pro úpravu metadat](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

   > [!TIP]
   > Pokud chcete ověřit záhlaví sloupců, spusťte experiment (pod plátnem experimentu klikněte na **spustit).** Po dokončení spuštění (zelená značka zaškrtnutí se zobrazí v [části Upravit metadata][edit-metadata]), klepněte na výstupní port modulu [Upravit metadata][edit-metadata] a vyberte **možnost Vizualizovat**. Můžete zobrazit výstup libovolného modulu stejným způsobem a zobrazit průběh dat prostřednictvím experimentu.
   > 
   > 

### <a name="create-training-and-test-datasets"></a>Vytvoření trénovacích a testovacích datových sad

Potřebujete nějaká data k trénování modelu a některé k jeho testování.
Takže v dalším kroku experimentu rozdělíte datovou sadu do dvou samostatných datových sad: jednu pro trénování našeho modelu a jednu pro testování.

Chcete-li to provést, použijte modul [Rozdělit data.][split]  

1. Najděte modul [Rozdělit data,][split] přetáhněte ho na plátno a připojte ho k modulu [Upravit metadata.][edit-metadata]

1. Ve výchozím nastavení je poměr rozdělení 0,5 a je nastaven parametr **Randomized split.** To znamená, že náhodná polovina dat je výstup prostřednictvím jednoho portu modulu [Split Data][split] a polovina přes druhý. Můžete upravit tyto parametry, stejně jako **Random seed** parametr, chcete-li změnit rozdělení mezi trénovací a testovací data. V tomto příkladu je ponecháte tak, jak jsou.
   
   > [!TIP]
   > Vlastnost **Zlomek řádků v první výstupní datové sadě** určuje, kolik dat je výstup přes *levý* výstupní port. Pokud například nastavíte poměr na 0,7, bude 70 % dat výstupem přes levý port a 30 % přes pravý port.  
   > 
   > 

1. Poklepejte na modul [Rozdělit data][split] a zadejte komentář "Training/testing data split 50%". 

Výstupy modulu Split [Data][split] můžete použít, jak chcete, ale zvolte použít levý výstup jako trénovací data a správný výstup jako testovací data.  

Jak již bylo zmíněno v [předchozím kroku](tutorial-part1-credit-risk.md#upload), náklady na nesprávné klasifikaci vysokého úvěrového rizika jako nízkého jsou pětkrát vyšší než náklady na nesprávné klasifikaci nízkého úvěrového rizika jako vysoké. Chcete-li účet pro toto, vygenerujete novou datovou sadu, která odráží tuto funkci nákladů. V nové datové sadě je každý příklad vysokého rizika replikován pětkrát, zatímco každý příklad nízkého rizika není replikován.   

Tuto replikaci můžete provést pomocí kódu R:  

1. Najděte a přetáhněte modul [Spustit skript R][execute-r-script] na plátno experimentu. 

1. Připojte levý výstupní port modulu [Split Data][split] k prvnímu vstupnímu portu ("Dataset1") modulu Execute [R Script.][execute-r-script]

1. Poklepejte na modul [Spustit skript R][execute-r-script] a zadejte komentář "Nastavit úpravu nákladů".

1. V podokně **Vlastnosti** odstraňte výchozí text v parametru **Skript R** a zadejte tento skript:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![Skript R v modulu Spustit skript R](./media/tutorial-part1-credit-risk/execute-r-script.png)

Je třeba provést stejnou operaci replikace pro každý výstup modulu [Split Data][split] tak, aby trénovací a testovací data mají stejnou úpravu nákladů. Nejjednodušší způsob, jak to udělat, je duplikovat modul [Skript spustit R,][execute-r-script] který jste právě provedli, a připojit jej k jinému výstupnímu portu modulu [Split Data.][split]

1. Klepněte pravým tlačítkem myši na modul [Spustit skript R][execute-r-script] a vyberte příkaz **Kopírovat**.

1. Klepněte pravým tlačítkem myši na plátno experimentu a vyberte **vložit**.

1. Přetáhněte nový modul do polohy a potom připojte správný výstupní port modulu [Split Data][split] k prvnímu vstupnímu portu tohoto nového [modulu Spouštění skriptu R.][execute-r-script] 

1. V dolní části plátna klikněte na **Spustit**. 

> [!TIP]
> Kopie modulu Execute R Script obsahuje stejný skript jako původní modul. Když zkopírujete a vložíte modul na plátno, kopie si zachová všechny vlastnosti originálu.  
> 
>

Náš experiment nyní vypadá asi takto:

![Přidání skriptů split modulu a R](./media/tutorial-part1-credit-risk/experiment.png)

Další informace o používání skriptů R v experimentech naleznete v [tématu Rozšíření experimentu pomocí jazyka R](extend-your-experiment-with-r.md).


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste dokončili tyto kroky: 
 
> [!div class="checklist"]
> * Vytvoření pracovního prostoru machine learningového studia (klasického)
> * Nahrání existujících dat do pracovního prostoru
> * Vytvoření experimentu

Nyní jste připraveni trénovat a vyhodnocovat modely pro tato data.

> [!div class="nextstepaction"]
> [Kurz 2 – Trénování a vyhodnocování modelů](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
