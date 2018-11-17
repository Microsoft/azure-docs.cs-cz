---
title: 'Krok 3: Vytvoření nového experimentu Machine Learning | Dokumentace Microsoftu'
description: 'Krok 3 vývoj prediktivního řešení návod: vytvoření nového výukového experimentu v nástroji Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.custom: (previous ms.author hshapiro)
ms.author: amlstudiodocs
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
ms.openlocfilehash: 2fdeab83d1e668fbbb68155c1695ffb40d71c15b
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824434"
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
Dalším krokem v tomto návodu je vytvořit nový experiment v Machine Learning Studio, který používá datovou sadu, který jsme nahráli.  

1. V sadě Studio, klikněte na tlačítko **+ nová** v dolní části okna.
2. Vyberte **EXPERIMENT**a pak vyberte "Prázdný Experiment". 

    ![Vytvoření nového experimentu][0]

2. Vyberte v horní části plátna výchozí název experimentu a přejmenujte jej na něco smysluplného.

    ![Přejmenování experimentu][5]
   
   > [!TIP]
   > Je dobrým zvykem vyplnit **Souhrn** a **popis** experimentu v **vlastnosti** podokně. Tyto vlastnosti vám poskytnou příležitost k dokumentaci testu tak, aby každý, kdo ho později zkoumá rozumět cíle a metodologie.
   > 
   > ![Vlastnosti experimentu][6]
   > 
3. Paletě modulů nalevo od plátna experimentu rozbalte **uložení datové sady**.
4. Vyhledejte datovou sadu, které jste vytvořili v části **Moje datové sady** a přetáhněte ji na plátno. Datovou sadu můžete najít také zadáním názvu v **hledání** pole výše na paletě.  

    ![Datovou sadu přidat do experimentu][7]

## <a name="prepare-the-data"></a>Příprava dat
Můžete zobrazit prvních 100 řádků dat a některé statistické informace pro celou datovou sadu: klikněte na výstupní port datové sady (malý kruh v dolní části) a vyberte **vizualizovat**.  

Protože datový soubor nebyl dodán záhlaví sloupců, Studio poskytuje obecný záhlaví (Sloupec1, Sloupec2, *atd*). Dobré záhlaví nejsou nezbytné pro vytvoření modelu, ale jejich usnadňují práci s daty v experimentu. Také když jsme se nakonec publikovat tento model ve webové službě, záhlaví pomoci při identifikaci sloupce, které chcete uživatele služby.  

Přidáme záhlaví sloupců pomocí [upravit Metadata][edit-metadata] modulu.
Můžete použít [upravit Metadata][edit-metadata] modulu změnit metadata spojená s datovou sadu. V tomto případě ho používáme k poskytování více popisné názvy pro záhlaví sloupců. 

Chcete-li použít [upravit Metadata][edit-metadata], Nejdřív musíte určit sloupce, které chcete upravit (v tomto případě všechny z nich.) Dále určete akci, která má být proveden na těchto sloupců (v tomto případě změna záhlaví sloupců.)

1. V palety modulů zadejte "metadat" **hledání** pole. [Upravit Metadata] [ edit-metadata] se zobrazí v seznamu modulů.

2. Klikněte na tlačítko a přetáhněte ji [upravit Metadata][edit-metadata] modulu na plátno a umístěte jej pod datovou sadu jsme přidali dříve.

3. Připojit datovou sadu, která [upravit Metadata][edit-metadata]: klikněte na výstupní port datové sady (přeškrtnutým kroužkem v dolní části datové sady), přetáhněte do vstupní port [upravit Metadata][edit-metadata] (přeškrtnutým kroužkem v horní části modulu), pak uvolnění tlačítka myši. Datové sady a modul zůstanou připojené i v případě, že přesouváte buď na plátně.
   
   Experiment by teď měl vypadat přibližně takto:  
   
   ![Přidání upravit Metadata][1]
   
   Červený vykřičník označuje, že jsme nebyly dosud nastavit vlastnosti pro tento modul. Provedeme to teď.
   
   > [!TIP]
   > Kliknutím dvakrát na modul a zadáním textu je možné přidat k modulu komentář. To vám může pomoci rychle poznat, jaký je účel modulu v experimentu. V tomto případě klikněte dvakrát na [upravit Metadata] [ edit-metadata] modulů a typů komentář "přidat záhlaví sloupců". Klikněte kamkoli jinam na plátně zavřete do textového pole. Pokud chcete zobrazit komentář, klikněte na šipku dolů v modulu.
   > 
   > ![Upravit Metadata modulu s byl přidán komentář.][8]
   > 
4. Vyberte [upravit Metadata][edit-metadata]a **vlastnosti** klikněte na podokno na pravé straně plátna **spustit selektor sloupců**.

5. V **vyberte sloupce, které** dialogovém okně Vybrat všechny řádky v **dostupných sloupců** a klikněte na > a přesunout jej do **vybrané sloupce**.
   Dialogové okno by měl vypadat nějak takto:

   ![Selektor sloupců s vybrány všechny sloupce][2]

6. Klikněte na tlačítko **OK** zaškrtávací políčko.

7. Zpátky **vlastnosti** podokně vyhledejte **nové názvy sloupců** parametru. V tomto poli zadejte seznam názvů 21 sloupců v datové sadě, oddělené čárkami a pořadí sloupců. Názvy sloupců můžete získat z datové sady dokumentace na webu UCI nebo pro usnadnění práce můžete zkopírovat a vložit následující seznam:  
   
       Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   
   V podokně vlastností vypadá takto:
   
   ![Vlastnosti pro upravit Metadata][3]

> [!TIP]
> Pokud chcete ověřit záhlaví sloupců, spustit experiment (klikněte na tlačítko **spustit** pod plátnem experimentu). Po jeho dokončení spuštění (zelená značka zaškrtnutí se zobrazí na [upravit Metadata][edit-metadata]), klikněte na výstupní port modulu [upravit Metadata][edit-metadata] modulu a vyberte **vizualizovat**. Zobrazit výstup jakýkoli modul stejným způsobem, chcete-li zobrazit průběh dat prostřednictvím experimentu.
> 
> 

## <a name="create-training-and-test-datasets"></a>Vytvoření trénovací a testovací datové sady
Potřebujeme nějaká data pro trénování modelu a některé a otestovat ho.
Abyste v dalším kroku experimentu jsme datové sady rozdělit na dvě samostatné datové sady: jeden pro školení náš model a jeden pro testování.

K tomuto účelu použijeme [rozdělení dat] [ split] modulu.  

1. Najít [rozdělení dat][split] modulu, přetáhněte na plátno a propojte jej s [upravit Metadata][edit-metadata] modulu.

2. Ve výchozím nastavení, poměr rozdělení je 0,5 a **Randomized rozdělení** je nastaven parametr. To znamená, že náhodný poloviční dat je výstup prostřednictvím jeden port [rozdělení dat] [ split] modulu a polovinu prostřednictvím druhé. Můžete upravit tyto parametry, jakož i **náhodná počáteční hodnota** parametr, chcete-li změnit rozdělení mezi trénování a testování data. V tomto příkladu nezpracováváme a necháváme ji jako-je.
   
   > [!TIP]
   > Vlastnost **podíl řádků v první výstupní sadě dat** Určuje, kolik dat je výstup prostřednictvím *levé* výstupní port. Například pokud nastavíte na 0,7 poměr, pak 70 % modulů pro data je výstup prostřednictvím levý port a z 30 % přes správný port.  
   > 
   > 

3. Dvakrát klikněte [rozdělení dat] [ split] modul a zadejte komentář, "trénování a testování data rozdělit 50 %". 

Můžeme použít výstupů [rozdělení dat] [ split] modulu jsme, jako jsou, ale můžeme se rozhodnout použít levý výstupní jako trénovacích dat a právo výstupní data jako testování.  

Jak je uvedeno v [předchozího kroku](walkthrough-2-upload-data.md), je pětkrát vyšší než náklady na misclassifying nízké úvěrové riziko jako vysoce misclassifying vysoké úvěrové riziko jako s nízkými náklady. Pro to, aby se vygeneruje nová datová sada, která zohledňuje, tato funkce nákladů. V nová datová sada se každý příklad vysoké riziko replikuje pětkrát, když není replikován každý příklad s nízkým rizikem.   

Můžeme udělat tento replikace požívajících kód R:  

1. Najděte a přetáhněte [spustit skript jazyka R] [ execute-r-script] modulů na plátno experimentu. 

2. Propojte levý výstupní port modulu [rozdělení dat] [ split] modulu na první vstupní port ("Dataset1") [spustit skript jazyka R] [ execute-r-script] modulu.

3. Dvakrát klikněte [spustit skript jazyka R] [ execute-r-script] modul a zadejte komentář, "Set nákladů úpravu".

4. V **vlastnosti** podokno, odstraňte výchozí text v **skript jazyka R** parametr a zadejte tento skript:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![Skript jazyka R v modulu spustit skript jazyka R][9]

Budeme muset udělat operaci replikace pro každé výstup [rozdělení dat] [ split] modulu tak, aby stejné úpravy náklady na trénovací a testovací data. Nejjednodušší způsob, jak to provést, je tak, že duplikujete [spustit skript jazyka R] [ execute-r-script] modulu jsme právě vytvořili a připojení k druhému výstupní port [rozdělení dat] [ split] modulu.

1. Klikněte pravým tlačítkem myši [spustit skript jazyka R] [ execute-r-script] modul a vyberte **kopírování**.

2. Klikněte pravým tlačítkem na plátno experimentu a vyberte **vložit**.

3. Přetáhněte nový modul na místo a potom propojte pravý výstupní port modulu [rozdělení dat] [ split] modulu první vstupní port této nové [spustit skript jazyka R] [ execute-r-script] modulu. 

4. V dolní části plátna, klikněte na tlačítko **spustit**. 

> [!TIP]
> Kopírovat skript R spustit modul obsahuje stejný skript jako původní modul. Když zkopírujete a vložíte modulu na plátně, kopie uchovává všechny vlastnosti původní.  
> 
> 

Naše experiment teď vypadá přibližně takto:

![Přidání modulu rozdělení a skripty jazyka R][4]

Další informace o používání skriptů jazyka R ve své experimenty, naleznete v tématu [rozšíření experimentů pomocí R](extend-your-experiment-with-r.md).

**Další krok: [trénování a vyhodnocení modelů](walkthrough-4-train-and-evaluate-models.md)**

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
