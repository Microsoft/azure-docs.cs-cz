---
title: 'Krok 3: Vytvoření nového experimentu Machine Learning | Microsoft Docs'
description: 'Krok 3 vývoj prediktivního řešení návod: vytvoření nového experimentu školení v Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 660e3c27-55ef-4c33-a4e9-dff4d1224630
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 95000f9fb57b95bf1edcda9abfba3668b5f5b523
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835735"
---
# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>Krok 3 průvodce: Vytvoření nového experimentu služby Azure Machine Learning
Toto je třetí krok tohoto průvodce, [vývoj řešení prediktivní analýzy v Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Vytvoření pracovního prostoru Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Nahrání existujících dat](walkthrough-2-upload-data.md)
3. **Vytvoření nového experimentu**
4. [Natrénování a vyhodnocení modelů](walkthrough-4-train-and-evaluate-models.md)
5. [Nasazení webové služby](walkthrough-5-publish-web-service.md)
6. [Přístup k webové službě](walkthrough-6-access-web-service.md)

- - -
Dalším krokem v tomto návodu je vytvoření experimentu v Machine Learning Studio používající datové sady, které jsme nahrát.  

1. V nástroji Studio klikněte na tlačítko **+ nový** v dolní části okna.
2. Vyberte **EXPERIMENTU**a potom vyberte "Prázdný Experiment". 

    ![Vytvoření nového experimentu][0]

2. Vyberte výchozí název v horní části na plátno experimentu a přejmenujte jej na něco smysluplného.

    ![Přejmenujte experimentu][5]
   
   > [!TIP]
   > Je dobrým zvykem vyplnit **Souhrn** a **popis** experimentu v **vlastnosti** podokně. Tyto vlastnosti získáte možnost dokumentu experimentu tak, aby každý, kdo ho později zabývá pochopili záměry a metody.
   > 
   > ![Vlastnosti experimentu][6]
   > 
3. Paletě modulů nalevo od plátna experimentu rozbalte **uložit datové sady**.
4. Vyhledá se datová jste vytvořili v části **Moje datové sady** a přetáhněte na plátno. Datovou sadu můžete také vyhledat zadáním názvu v **vyhledávání** pole výše paletě.  

    ![Přidejte datovou sadu experimentu][7]

## <a name="prepare-the-data"></a>Příprava dat
Můžete zobrazit prvních 100 řádků dat a některé statistické informace pro celou datovou sadu: klikněte na výstupní port datové sady (přeškrtnutým kroužkem dole) a vyberte **vizualizovat**.  

Protože datový soubor nebyl dodán záhlaví sloupců, Studio poskytl obecné záhlaví (Sloupec1, Sloupec2, *atd*). Dobrý záhlaví nejsou nezbytně nutné, aby vytvoření modelu, ale jejich usnadnili práci s daty v rámci experimentu. Navíc pokud jsme nakonec publikovat tento model ve webové službě, záhlaví pomoci při identifikaci sloupce, které chcete uživatele služby.  

Přidáme záhlaví sloupců pomocí [upravit Metadata][edit-metadata] modulu.
Můžete použít [upravit Metadata][edit-metadata] modulu změnit metadata spojená s datovou sadu. V takovém případě jsme můžete zadat další popisné názvy záhlaví sloupců. 

Chcete-li použít [upravit Metadata][edit-metadata], nejprve zadejte sloupce, které chcete upravit (v tomto případě všechny z nich.) Potom zadejte akci prováděnou na tyto sloupce (v tomto případě změna záhlaví sloupců.)

1. Paletě modulů, zadejte "metadata" **vyhledávání** pole. [Upravit Metadata] [ edit-metadata] se zobrazí v seznamu modulů.

2. Klikněte na tlačítko a přetáhněte ji [upravit Metadata][edit-metadata] modulu na plátno a umístěte jej pod datovou sadu jsme přidali dříve.

3. Připojit datovou sadu, která [upravit Metadata][edit-metadata]: klikněte na výstupní port datové sady (přeškrtnutým kroužkem v dolní části datové sady), přetáhněte do vstupní port [upravit Metadata][edit-metadata] (přeškrtnutým kroužkem v horní části modulu), pak uvolnění tlačítka myši. Datovou sadu a modul zůstanou připojená, i když přesouváte buď na plátně.
   
   Experiment by teď měl vypadat přibližně takto:  
   
   ![Přidání metadat úpravy][1]
   
   Červený vykřičník označuje, že jsme nebyly dosud nastavit vlastnosti pro tento modul. Provedeme tento další.
   
   > [!TIP]
   > Kliknutím dvakrát na modul a zadáním textu je možné přidat k modulu komentář. To vám může pomoci rychle poznat, jaký je účel modulu v experimentu. V takovém případě dvakrát klikněte [upravit Metadata] [ edit-metadata] modul a zadejte komentář "přidat záhlaví sloupců". Klikněte na tlačítko kdekoliv jinde na plátně zavřete textového pole. Pokud chcete zobrazit komentář, klikněte na šipku dolů na modul.
   > 
   > ![Upravit komentář přidat Metadata modulu][8]
   > 
4. Vyberte [upravit Metadata][edit-metadata]a v **vlastnosti** napravo od plátna, klikněte na tlačítko **spustit selektor sloupců**.

5. V **vyberte sloupce** dialogovém okně, vyberte všechny řádky v **dostupné sloupce** a klikněte na tlačítko > přesunout jejich **vybrané sloupce**.
   Dialogové okno by měl vypadat takto:

   ![Selektor sloupců s vybrané všechny sloupce][2]

6. Klikněte **OK** zaškrtnutí.

7. Zpět v **vlastnosti** podokně, podívejte se **nové názvy sloupců** parametr. V tomto poli zadejte seznam názvů pro 21 sloupců v datové sadě, oddělených čárkami a pořadí sloupců. Názvy sloupců můžete získat z dokumentace datové sady na webu UCI nebo ke zvýšení pohodlí můžete zkopírovat a vložit v následujícím seznamu:  
   
       Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   
   V podokně Vlastnosti vypadá takto:
   
   ![Vlastnosti pro úpravy metadat][3]

> [!TIP]
> Pokud chcete ověřit záhlaví sloupců, spustit experiment (klikněte na tlačítko **spustit** níže na plátno experimentu). Po jeho dokončení spuštění (zelená značka zaškrtnutí se zobrazí na [upravit Metadata][edit-metadata]), klikněte na výstupní port modulu [upravit Metadata][edit-metadata] modulu a vyberte **vizualizovat**. Zobrazí se výstup libovolný modul stejným způsobem, chcete-li zobrazit průběh dat přes experimentu.
> 
> 

## <a name="create-training-and-test-datasets"></a>Vytvoření školení a testovací datové sady
Potřebujeme některá data pro trénování modelu a některé to vyzkoušíte.
Proto v dalším kroku experiment, jsme datovou sadu rozdělit na dvě samostatné datové sady: jeden pro cvičení našeho modelu a jeden pro testování ho.

K tomuto účelu používáme [rozdělení dat] [ split] modulu.  

1. Najít [rozdělení dat][split] modulu, přetáhněte na plátno a propojte jej s [upravit Metadata][edit-metadata] modulu.

2. Výchozí hodnota je 0,5 poměr rozdělení a **Randomized rozdělení** parametr je nastaven. Znamená, že náhodných polovina dat je výstup prostřednictvím jednoho portu [rozdělení dat] [ split] modulu a polovina prostřednictvím dalších. Můžete upravit tyto parametry společně s **náhodná počáteční hodnota** parametr, chcete-li změnit rozdělení mezi trénování a testování data. V tomto příkladu jsme je jako ponechte-je.
   
   > [!TIP]
   > Vlastnost **podíl řádků v první výstupní sadě dat** Určuje, kolik dat je výstup prostřednictvím *levém* výstupní port. Například pokud nastavíte poměr 0,7, pak 70 % dat je výstup prostřednictvím levý port a z 30 % prostřednictvím pravý port.  
   > 
   > 

3. Dvakrát klikněte [rozdělení dat] [ split] modul a zadejte komentář, "školení nebo testování dat rozdělení 50 %". 

Můžeme použít výstupů [rozdělení dat] [ split] modulu jsme jako, ale můžeme se rozhodnete použít levý výstupní jako Cvičná data a právo výstupní data jako testování.  

Jak je uvedeno v [předchozího kroku](walkthrough-2-upload-data.md), je pětkrát vyšší než náklady na misclassifying nízkou úvěrové riziko jako vysoce misclassifying vysoké úvěrové riziko jako nízkými náklady. Aby se zohlednily to, se vygeneruje nová datová sada, která odráží tuto funkci náklady. V nová datová sada se každý příklad vysoce rizikové replikují pětkrát, zatímco se replikují každý příklad nízké riziko.   

Provedeme tato replikace pomocí kódu jazyka R:  

1. Najděte a přetáhněte [spustit skript jazyka R] [ execute-r-script] modulu na plátno experimentu. 

2. Připojit levý výstupní port modulu [rozdělení dat] [ split] modulu první vstupní port ("Dataset1") [spustit skript jazyka R] [ execute-r-script] modulu.

3. Dvakrát klikněte [spustit skript jazyka R] [ execute-r-script] modul a zadejte komentář, "Sada úprava nákladů".

4. V **vlastnosti** podokně odstranit výchozí text v **skript jazyka R** parametr a zadejte tento skript:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![R skript v modulu spustit skript jazyka R][9]

Je potřeba udělat stejná operace replikace pro každé výstup [rozdělení dat] [ split] modulu tak, aby data školení a testování stejné úpravu náklady. Nejjednodušší způsob je pomocí duplikování [spustit skript jazyka R] [ execute-r-script] modulu jsme provedli a jejím propojením s druhým výstupní port [rozdělení dat] [ split] modulu.

1. Klikněte pravým tlačítkem myši [spustit skript jazyka R] [ execute-r-script] modul a vyberte **kopie**.

2. Klikněte pravým tlačítkem na plátno experimentu a vyberte **vložení**.

3. Přetáhněte nového modulu do pozice a potom se připojte správné výstupní port modulu [rozdělení dat] [ split] modulu první vstupní port této nové [spustit skript jazyka R] [ execute-r-script] modulu. 

4. V dolní části plátna, klikněte na tlačítko **spustit**. 

> [!TIP]
> Kopii modulu spustit skript jazyka R obsahuje stejný skriptu jako původní modul. Když je kopírujete a vkládáte modul na plátně, bude pro kopii uchovává všechny vlastnosti původní.  
> 
> 

Naše experimentu nyní vypadat třeba takto:

![Přidání modulu rozdělení a skripty R][4]

Další informace o použití skripty R v experimentů najdete v tématu [rozšíření experimentů pomocí R](extend-your-experiment-with-r.md).

**Další krok: [Train a vyhodnocení modelů](walkthrough-4-train-and-evaluate-models.md)**

[0]: ./media/walkthrough-3-create-new-experiment/create-new-experiment.png
[5]: ./media/walkthrough-3-create-new-experiment/rename-experiment.png
[6]: ./media/walkthrough-3-create-new-experiment/experiment-properties.png
[7]: ./media/walkthrough-3-create-new-experiment/add-dataset-to-experiment.png
[8]: ./media/walkthrough-3-create-new-experiment/edit-metadata-with-comment.png
[9]: ./media/walkthrough-3-create-new-experiment/execute-r-script.png
[1]: ./media/walkthrough-3-create-new-experiment/experiment-with-edit-metadata-module.png
[2]: ./media/walkthrough-3-create-new-experiment/select-columns.png
[3]: ./media/walkthrough-3-create-new-experiment/edit-metadata-properties.png
[4]: ./media/walkthrough-3-create-new-experiment/experiment.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
