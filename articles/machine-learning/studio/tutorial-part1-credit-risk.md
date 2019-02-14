---
title: 'Kurz 1: Předvídání úvěrového rizika'
titleSuffix: Azure Machine Learning Studio
description: Podrobný kurz ukazuje, jak vytvořit řešení prediktivní analýzy pro posuzování úvěrového rizika v nástroji Azure Machine Learning Studio. Tento kurz je první částí z třídílné série.  Ukazuje, jak vytvořit pracovní prostor, odešle data a vytvořit nový experiment.
keywords: úvěrové riziko,řešení prediktivní analýzy,posouzení rizika
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 6789e1d6e514ce107ab96fddbaa582ccbe922bbb
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243718"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio"></a>Kurz 1: Předpovědět úvěrové riziko - Azure Machine Learning Studio

V tomto kurzu provedete rozšířené pohled na proces vývoje řešení prediktivní analýzy. Při vývoji jednoduchý model v nástroji Machine Learning Studio.  Potom tento model nasadíme jako webové služby Azure Machine Learning.  Tento model nasazený můžete vytvářet predikce na nová data. Tento kurz je **první částí z třídílné série kurzů**.

Předpokládejme, že potřebujete předpovědět úvěrové riziko u jednotlivých zákazníků na základě údajů, které uvedli v žádosti o úvěr.  

Posouzení úvěrového rizika je komplexní problém, ale v tomto kurzu se tak si ho Zjednodušte trochu. Budete ho používat jako příklad, jak můžete vytvořit řešení prediktivní analýzy pomocí služby Microsoft Azure Machine Learning. Budete používat Azure Machine Learning Studio a webovou službou Machine Learning pro toto řešení.  

V tomto kurzu třemi částmi začnete s veřejně dostupnými daty úvěrového rizika.  Vyvíjejte a natrénujeme prediktivní model.  Nakonec tento model nasadíme jako webovou službu.

V této části kurzu jste: 
 
> [!div class="checklist"]
> * Vytvoření pracovního prostoru Machine Learning Studio
> * Nahrání existujících dat
> * Vytvoření experimentu

Pak můžete použít pro tento experiment [trénování modelů v části 2](tutorial-part2-credit-risk-train.md) a potom [nasaďte je do 3. část](tutorial-part3-credit-risk-deploy.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že jste použili Machine Learning Studio alespoň jednou před a že rozumíte některé konceptům strojového učení. Bere ale v úvahu, že nejste odborníkem ani na jedno.

Pokud jste nikdy **Azure Machine Learning Studio** dříve, můžete chtít spustit v rychlém startu, [vytvoření vašeho prvního experimentu datové vědy v nástroji Azure Machine Learning Studio](create-experiment.md). Rychlý start vás provede Machine Learning Studio poprvé. Ukáže vám základy toho, jak pomocí myši přetáhnout moduly do experimentu, vzájemně je propojit, spustit experiment a prohlédnout si výsledky.


> [!TIP] 
> Můžete najít pracovní kopii experimentu, který používáte k vývoji tohoto kurzu [galerii Azure AI](https://gallery.azure.ai). Přejděte na **[kurz – předpovědět úvěrové riziko](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** a klikněte na tlačítko **Open in Studio** a stáhněte kopii experimentu do pracovního prostoru Machine Learning Studio.
> 


## <a name="create-a-machine-learning-studio-workspace"></a>Vytvoření pracovního prostoru Machine Learning Studio

Pokud chcete používat Machine Learning Studio, musíte mít pracovní prostor Microsoft Azure Machine Learning Studio. Tento pracovní prostor obsahuje nástroje potřebné k vytváření, správě a publikování experimentů.  

Chcete-li vytvořit pracovní prostor, přečtěte si téma [vytváření a sdílení pracovního prostoru služby Azure Machine Learning](create-workspace.md).

Po vytvoření pracovního prostoru, otevřete Machine Learning Studio ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)). Pokud máte více než jeden pracovní prostor, můžete vybrat pracovní prostor v panelu nástrojů v pravém horním rohu okna.

![Vyberte pracovní prostor v sadě Studio](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> Pokud jste vlastník pracovního prostoru, můžete sdílet experimenty, kterou právě pracujete ostatní pozváním do pracovního prostoru. Můžete to provést v nástroji Machine Learning Studio na **nastavení** stránky. Stačí účet Microsoft nebo účtu organizace pro každého uživatele.
> 
> Na **nastavení** klikněte na **uživatelé**, pak klikněte na tlačítko **POZVAT další uživatele** v dolní části okna.
> 

## <a name="upload"></a>Nahrání existujících dat

K vývoji prediktivního modelu pro úvěrové riziko, potřebujete data, která můžete použít k trénování a pak model otestujeme. Pro účely tohoto kurzu budete používat "UCI Statlog (německé daty o úvěrovém) Data Set" v úložišti UC Irvine Machine Learning. Najdete ho tady:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Budete používat soubor s názvem **german.data**. Stáhněte si tento soubor na místní pevný disk.  

**German.data** datová sada obsahuje řádky 20 proměnných pro 1000 posledních žadatel o kredit. Tyto proměnné 20 představují sadu funkcí datové sady ( *funkce vector*), poskytující charakteristiky identifikace pro každý kredit žadatel. Další sloupce v jednotlivých řádcích představuje žadatele počítané úvěrové riziko s vysokým rizikem označeno jako nízké úvěrové riziko a 300 700 uchazeči.

Web UCI poskytuje popis atributů vektoru funkce pro tato data. Tato data zahrnují finančním informacím, historie kreditu, stav pracovní a osobní údaje. Pro každý žadatel binární hodnocení byl daný označující, zda jsou nízké nebo vysoké úvěrové riziko. 

Tato data použijete k natrénování modelu prediktivní analýzy. Až budete hotovi, váš model by měl být moct přijmout vektor funkce pro nové uživatele a předpovědět, jestli uživatel je nízké nebo vysoké úvěrové riziko.  

Tady je zajímavé prvkem.

Popis datové sady na webu UCI uvádí, co to stojí Pokud misclassify osoby úvěrové riziko.
Pokud tento model předpovídá vysoké úvěrové riziko u někoho, kdo je ve skutečnosti nízké úvěrové riziko, model chybnou provedl.

Reverzní chybnou je pětkrát dražší do finanční instituce, ale: Pokud tento model předpovídá nízké úvěrové riziko u někoho, kdo je ve skutečnosti vysoké úvěrové riziko.

Ano budete chtít trénování modelu, tak, aby náklady na tento druhý typ. chybnou pětkrát větší než misclassifying jiným způsobem.

Jeden jednoduchý způsob, jak to provést, když trénujete model v experimentu je tak, že duplikujete (pětkrát) ty položky, které představují někdo s vysoké úvěrové riziko. 

Potom Pokud model misclassifies někdo jako nízké úvěrové riziko, když jsou ve skutečnosti vysoce rizikové, model nemá tento stejný chybnou pětkrát, jednou pro každý duplicitní. Tím se zvýší náklady na této chyby ve výsledcích školení.


### <a name="convert-the-dataset-format"></a>Převést formát datové sady

Původní datové sady používá formát oddělených prázdné. Machine Learning Studio lépe funguje s hodnotami oddělenými čárkami (CSV) souboru, tak budete převést datové sady nahrazením mezer čárkami.  

Existuje mnoho způsobů, jak převést tato data. Jedním ze způsobů je pomocí následujícího příkazu Windows Powershellu:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Dalším způsobem je použití příkazu sed systému Unix:  

    sed 's/ /,/g' german.data > german.csv  

V obou případech vytvoříte oddělených čárkou verzi dat do souboru s názvem **german.csv** , který vám pomůže v experimentu.

### <a name="upload-the-dataset-to-machine-learning-studio"></a>Nahrajte datovou sadu do nástroje Machine Learning Studio

Po převedení dat do formátu CSV, budete muset nahrát do nástroje Machine Learning Studio. 

1. Otevřete domovskou stránku Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Klikněte na nabídku ![nabídky](./media/tutorial-part1-credit-risk/menu.png) v levém horním rohu okna, klikněte na tlačítko **Azure Machine Learning**vyberte **Studio**a přihlaste se.

3. Klikněte na tlačítko **+ nová** v dolní části okna.

4. Vyberte **datovou sadu**.

5. Vyberte **z místního souboru**.

    ![Přidat datovou sadu z místního souboru](./media/tutorial-part1-credit-risk/add-dataset.png)

6. V **nahrát novou datovou sadu** dialogového okna, klikněte na tlačítko Procházet a najít **german.csv** souborů, které jste vytvořili.

7. Zadejte název pro tuto datovou sadu. Pro účely tohoto kurzu říkat "Údajů o kreditních kartách UCI němčina".

8. Datový typ, vyberte **obecný soubor CSV neobsahuje záhlaví (. nh.csv)**.

9. Pokud chcete přidáte popis.

10. Klikněte na tlačítko **OK** zaškrtávací políčko.  

    ![Nahrajte datovou sadu](./media/tutorial-part1-credit-risk/upload-dataset.png)

To nahrává data do datové sady modul, který vám pomůže v jednom experimentu.

Můžete spravovat datové sady, které jste odeslali do Studio kliknutím **datových sad** karty na levé straně okna nástroje Studio.

![Správa datových sad](./media/tutorial-part1-credit-risk/dataset-list.png)

Další informace o importu dalších typů dat do experimentu najdete v tématu [Import cvičných dat do Azure Machine Learning Studio](import-data.md).

## <a name="create-an-experiment"></a>Vytvoření experimentu

Dalším krokem v tomto kurzu je vytvořit nový experiment v Machine Learning Studio, který používá datovou sadu, kterou jste nahráli.  

1. V sadě Studio, klikněte na tlačítko **+ nová** v dolní části okna.
1. Vyberte **EXPERIMENT**a pak vyberte "Prázdný Experiment". 

    ![Vytvoření nového experimentu](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. Vyberte v horní části plátna výchozí název experimentu a přejmenujte jej na něco smysluplného.

    ![Přejmenování experimentu](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > Je dobrým zvykem vyplnit **Souhrn** a **popis** experimentu v **vlastnosti** podokně. Tyto vlastnosti vám poskytnou příležitost k dokumentaci testu tak, aby každý, kdo ho později zkoumá rozumět cíle a metodologie.
   > 
   > ![Vlastnosti experimentu](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. Paletě modulů nalevo od plátna experimentu rozbalte **uložení datové sady**.
1. Vyhledejte datovou sadu, které jste vytvořili v části **Moje datové sady** a přetáhněte ji na plátno. Datovou sadu můžete najít také zadáním názvu v **hledání** pole výše na paletě.  

    ![Datovou sadu přidat do experimentu](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>Příprava dat

Můžete zobrazit prvních 100 řádků dat a některé statistické informace pro celou datovou sadu: Klikněte na výstupní port datové sady (malý kruh v dolní části) a vyberte **vizualizovat**.  

Protože datový soubor nebyl dodán záhlaví sloupců, Studio poskytuje obecný záhlaví (Sloupec1, Sloupec2, *atd*). Dobré záhlaví nejsou nezbytné pro vytvoření modelu, ale jejich usnadňují práci s daty v experimentu. Také až nakonec publikovat tento model ve webové službě, záhlaví pomoci při identifikaci sloupce, které chcete uživatele služby.  

Můžete přidat pomocí záhlaví sloupců [upravit Metadata] [ edit-metadata] modulu.

Můžete použít [upravit Metadata][edit-metadata] modulu změnit metadata spojená s datovou sadu. V takovém případě vám to zadat další popisné názvy záhlaví sloupců. 

Chcete-li použít [upravit Metadata][edit-metadata], Nejdřív musíte určit sloupce, které chcete upravit (v tomto případě všechny z nich.) Dále určete akci, která má být proveden na těchto sloupců (v tomto případě změna záhlaví sloupců.)

1. V palety modulů zadejte "metadat" **hledání** pole. [Upravit Metadata] [ edit-metadata] se zobrazí v seznamu modulů.

1. Klikněte na tlačítko a přetáhněte ji [upravit Metadata] [ edit-metadata] modulů na plátno a klesne pod datové sady, které jste přidali dříve.

1. Připojit datovou sadu, která [upravit Metadata][edit-metadata]: klikněte na výstupní port datové sady (přeškrtnutým kroužkem v dolní části datové sady), přetáhněte do vstupní port [upravit Metadata][edit-metadata] (přeškrtnutým kroužkem v horní části modulu), pak uvolnění tlačítka myši. Datové sady a modul zůstanou připojené i v případě, že přesouváte buď na plátně.
 
    Experiment by teď měl vypadat přibližně takto:  

    ![Přidání upravit Metadata](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    Červený vykřičník označuje, že nebyly nastavení vlastností pro tento modul ještě. Uděláte to teď.

    > [!TIP]
    > Kliknutím dvakrát na modul a zadáním textu je možné přidat k modulu komentář. To vám může pomoci rychle poznat, jaký je účel modulu v experimentu. V tomto případě klikněte dvakrát na [upravit Metadata] [ edit-metadata] modulů a typů komentář "přidat záhlaví sloupců". Klikněte kamkoli jinam na plátně zavřete do textového pole. Pokud chcete zobrazit komentář, klikněte na šipku dolů v modulu.
    > 
    > ![Upravit Metadata modulu s byl přidán komentář.](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. Vyberte [upravit Metadata][edit-metadata]a **vlastnosti** klikněte na podokno na pravé straně plátna **spustit selektor sloupců**.

1. V **vyberte sloupce, které** dialogovém okně Vybrat všechny řádky v **dostupných sloupců** a klikněte na > a přesunout jej do **vybrané sloupce**.
   Dialogové okno by měl vypadat nějak takto:

   ![Selektor sloupců s vybrány všechny sloupce](./media/tutorial-part1-credit-risk/select-columns.png)


1. Klikněte na tlačítko **OK** zaškrtávací políčko.

1. Zpátky **vlastnosti** podokně vyhledejte **nové názvy sloupců** parametru. V tomto poli zadejte seznam názvů 21 sloupců v datové sadě, oddělené čárkami a pořadí sloupců. Názvy sloupců můžete získat z datové sady dokumentace na webu UCI nebo pro usnadnění práce můžete zkopírovat a vložit následující seznam:  

  ```   
  Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
  ```

  V podokně vlastností vypadá takto:

  ![Vlastnosti pro upravit Metadata](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

  > [!TIP]
  > Pokud chcete ověřit záhlaví sloupců, spustit experiment (klikněte na tlačítko **spustit** pod plátnem experimentu). Po jeho dokončení spuštění (zelená značka zaškrtnutí se zobrazí na [upravit Metadata][edit-metadata]), klikněte na výstupní port modulu [upravit Metadata][edit-metadata] modulu a vyberte **vizualizovat**. Zobrazit výstup jakýkoli modul stejným způsobem, chcete-li zobrazit průběh dat prostřednictvím experimentu.
  > 
  > 

### <a name="create-training-and-test-datasets"></a>Vytvoření trénovací a testovací datové sady

Potřebujete data pro trénování modelu a některé a otestovat ho.
Abyste v dalším kroku testu můžete datové sady rozdělit na dvě samostatné datové sady: jeden pro školení náš model a jeden pro testování.

K tomuto účelu můžete použít [rozdělení dat] [ split] modulu.  

1. Najít [rozdělení dat][split] modulu, přetáhněte na plátno a propojte jej s [upravit Metadata][edit-metadata] modulu.

1. Ve výchozím nastavení, poměr rozdělení je 0,5 a **Randomized rozdělení** je nastaven parametr. To znamená, že náhodný poloviční dat je výstup prostřednictvím jeden port [rozdělení dat] [ split] modulu a polovinu prostřednictvím druhé. Můžete upravit tyto parametry, jakož i **náhodná počáteční hodnota** parametr, chcete-li změnit rozdělení mezi trénování a testování data. V tomto příkladu je jako ponecháte-je.
   
   > [!TIP]
   > Vlastnost **podíl řádků v první výstupní sadě dat** Určuje, kolik dat je výstup prostřednictvím *levé* výstupní port. Například pokud nastavíte na 0,7 poměr, pak 70 % modulů pro data je výstup prostřednictvím levý port a z 30 % přes správný port.  
   > 
   > 

1. Dvakrát klikněte [rozdělení dat] [ split] modul a zadejte komentář, "trénování a testování data rozdělit 50 %". 

Můžete použít na výstupy [rozdělení dat] [ split] modulu však, jako jsou, ale můžeme se rozhodnout použít levý výstupní jako trénovacích dat a právo výstupní data jako testování.  

Jak je uvedeno v [předchozího kroku](tutorial-part1-credit-risk.md#upload), je pětkrát vyšší než náklady na misclassifying nízké úvěrové riziko jako vysoce misclassifying vysoké úvěrové riziko jako s nízkými náklady. Aby se zohlednily to, generovat novou datovou sadu, která zohledňuje, tato funkce náklady. V nová datová sada se každý příklad vysoké riziko replikuje pětkrát, když není replikován každý příklad s nízkým rizikem.   

Tato replikace můžete provést pomocí kódu R:  

1. Najděte a přetáhněte [spustit skript jazyka R] [ execute-r-script] modulů na plátno experimentu. 

1. Propojte levý výstupní port modulu [rozdělení dat] [ split] modulu na první vstupní port ("Dataset1") [spustit skript jazyka R] [ execute-r-script] modulu.

1. Dvakrát klikněte [spustit skript jazyka R] [ execute-r-script] modul a zadejte komentář, "Set nákladů úpravu".

1. V **vlastnosti** podokno, odstraňte výchozí text v **skript jazyka R** parametr a zadejte tento skript:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![Skript jazyka R v modulu spustit skript jazyka R](./media/tutorial-part1-credit-risk/execute-r-script.png)

Je třeba provést operaci replikace pro každé výstup [rozdělení dat] [ split] modulu tak, aby stejné úpravy náklady na trénovací a testovací data. Nejjednodušší způsob, jak to provést, je tak, že duplikujete [spustit skript jazyka R] [ execute-r-script] modulu, které jste právě vytvořili a připojení k druhému výstupní port [rozdělení dat] [ split] modulu.

1. Klikněte pravým tlačítkem myši [spustit skript jazyka R] [ execute-r-script] modul a vyberte **kopírování**.

1. Klikněte pravým tlačítkem na plátno experimentu a vyberte **vložit**.

1. Přetáhněte nový modul na místo a potom propojte pravý výstupní port modulu [rozdělení dat] [ split] modulu první vstupní port této nové [spustit skript jazyka R] [ execute-r-script] modulu. 

1. V dolní části plátna, klikněte na tlačítko **spustit**. 

> [!TIP]
> Kopírovat skript R spustit modul obsahuje stejný skript jako původní modul. Když zkopírujete a vložíte modulu na plátně, kopie uchovává všechny vlastnosti původní.  
> 
>

Naše experiment teď vypadá přibližně takto:

![Přidání modulu rozdělení a skripty jazyka R](./media/tutorial-part1-credit-risk/experiment.png)

Další informace o používání skriptů jazyka R ve své experimenty, naleznete v tématu [rozšíření experimentů pomocí R](extend-your-experiment-with-r.md).


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste dokončili tyto kroky: 
 
> [!div class="checklist"]
> * Vytvoření pracovního prostoru Machine Learning Studio
> * Nahrání existujících dat do pracovního prostoru
> * Vytvoření experimentu

Nyní jste připraveni k natrénování a vyhodnocení modelů pro tato data.

> [!div class="nextstepaction"]
> [Kurz 2 – trénování a vyhodnocení modelů](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/