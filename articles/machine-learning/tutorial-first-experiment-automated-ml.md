---
title: Vytváření automatizovaných klasifikačních modelů ML
titleSuffix: Azure Machine Learning
description: Výukové & nasazení modelů klasifikace bez psaní kódu pomocí automatizovaného rozhraní machine learningu Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 12/21/2020
ms.custom: automl
ms.openlocfilehash: d0e236891e48a20adf1901d2f95a90ae25969c49
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210842"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Kurz: vytvoření klasifikačního modelu pomocí automatizovaného ML v Azure Machine Learning

Naučte se vytvářet [jednoduchý model klasifikace](concept-automated-ml.md#classification) bez psaní jediného řádku kódu pomocí automatizovaného strojového učení v Azure Machine Learning Studiu. Tento model klasifikace předpovídá, jestli se klient přihlásí k odběru pevně stanoveného termínu s finanční institucí.

Pomocí automatizovaného strojového učení můžete automatizovat časově náročné úlohy. Automatizované Machine Learning rychle projde mnoho kombinací algoritmů a parametrů, které vám pomůžou najít nejlepší model na základě metriky úspěšnosti výběru.

V tomto kurzu nebudete psát žádný kód, k provedení školení použijete rozhraní studia.  Naučíte se, jak provádět následující úlohy:

> [!div class="checklist"]
> * Vytvořte pracovní prostor Azure Machine Learning.
> * Jak spouštět experimenty automatizovaného strojového učení.
> * Zobrazit podrobnosti experimentu.
> * Nasazení modelu

Vyzkoušejte taky automatizované Machine Learning pro tyto typy modelů:

* Příklad předběžného kódu pro prognózování najdete v tématu [kurz: prognózování poptávky & AutoML](tutorial-automated-ml-forecast.md).
* Příklad kódu regresního modelu naleznete v [kurzu: Použití automatizovaného strojového učení k předvídání taxislužby tarifů](tutorial-auto-train-models.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree).

* Stáhněte [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) datový soubor. Sloupec **y** indikuje, jestli se zákazník přihlásil k odběru pevně stanoveného termínu, který se později identifikuje jako cílový sloupec pro předpovědi v tomto kurzu. 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Azure Machine Learning pracovní prostor je základní prostředek v cloudu, který používáte k experimentování, výuce a nasazování modelů strojového učení. Přijedná se o vaše předplatné Azure a skupinu prostředků k snadno spotřebovanému objektu ve službě. 

Existuje mnoho způsobů, jak [vytvořit pracovní prostor](how-to-manage-workspace.md). V tomto kurzu vytvoříte pracovní prostor prostřednictvím Azure Portal, webové konzoly pro správu prostředků Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Poznamenejte si svůj **pracovní prostor** a **předplatné**. Budete je potřebovat, abyste se ujistili, že vytvoříte experiment na správném místě. 

## <a name="sign-in-to-the-studio"></a>Přihlásit se k studiu

Provedete následující kroky experimentu a spustíte je pomocí nástroje Azure Machine Learning Studio v rámci https://ml.azure.com , konsolidovaného webového rozhraní, které zahrnuje nástroje strojového učení, k provádění scénářů pro datové vědy u všech úrovní dovedností. Aplikace Studio není podporována v prohlížečích aplikace Internet Explorer.

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com).

1. Vyberte své předplatné a pracovní prostor, který jste vytvořili.

1. Vyberte **Začínáme**.

1. V levém podokně vyberte v části **Autor** možnost **automatizovaná ml** .

   Vzhledem k tomu, že se jedná o váš první automatizovaný experiment ML, zobrazí se prázdný seznam a odkazy na dokumentaci.

   ![Stránka Začínáme](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Vyberte **+ Nový Automated ml Run**. 

## <a name="create-and-load-dataset"></a>Vytvořit a načíst datovou sadu

Před konfigurací experimentu nahrajte datový soubor do svého pracovního prostoru ve formě Azure Machine Learning datové sady. V takovém případě vám umožní zajistit, aby vaše data byla pro váš experiment vhodně formátována.

1. Vytvořte novou datovou sadu výběrem možnosti **místní soubory** z rozevíracího seznamu  **+ vytvořit datovou sadu** . 

    1. Ve formuláři **základní informace** zadejte název datové sady a zadejte volitelný popis. Rozhraní Automated ML aktuálně podporuje pouze TabularDatasets, takže typ datové sady by měl být výchozí *tabulkou*.

    1. V levém dolním rohu vyberte **Další** .

    1. Na formuláři **úložiště dat a výběr souboru** vyberte výchozí úložiště dat, které se automaticky nastavilo během vytváření pracovního prostoru, **workspaceblobstore (Azure Blob Storage)**. Do tohoto místa nahrajete datový soubor, abyste ho mohli zpřístupnit vašemu pracovnímu prostoru.

    1. Vyberte **Procházet**.
    
    1. Vyberte soubor **bankmarketing_train.csv** na místním počítači. Jedná se o soubor, který jste stáhli jako [požadavek](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Poskytněte datovou sadu jedinečný název a zadejte volitelný popis. 

    1. V levém dolním rohu vyberte **Další** a nahrajte ho do výchozího kontejneru, který se automaticky nastavil během vytváření pracovního prostoru.  
    
       Po dokončení nahrávání se formulář nastavení a náhled předem vyplní podle typu souboru. 
       
    1. Ověřte, zda je formulář **nastavení a náhled** vyplněný následujícím způsobem, a vyberte možnost **Další**.
        
        Pole|Description| Hodnota pro kurz
        ---|---|---
        Formát souboru|Definuje rozložení a typ dat uložených v souboru.| Oddělených
        Oddělovač|Jeden nebo více znaků pro určení hranice mezi &nbsp; oddělenými a nezávislými oblastmi v prostém textu nebo v jiných datových proudech. |Čárka
        Encoding|Určuje, jaká bitová tabulka schématu znaků má být použita ke čtení datové sady.| UTF-8
        Záhlaví sloupců| Určuje, jakým způsobem bude zpracována záhlaví datové sady (pokud existuje).| Všechny soubory mají stejná záhlaví.
        Přeskočit řádky | Určuje, kolik, pokud nějaký z nich je v datové sadě vynecháno.| Žádné

    1. Formulář **schématu** umožňuje další konfiguraci dat pro tento experiment. V tomto příkladu vyberte přepínač pro **day_of_week**, tak, aby se nezahrnul. Vyberte **Další**.
         ![Formulář schématu](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)
    1. Na formuláři **potvrdit podrobnosti** ověřte, že se informace shodují s dříve vyplněnými **základními informacemi, úložištěm dat a výběrem souborů** a **nastaveními a náhledem** .
    
    1. Vyberte **vytvořit** a dokončete vytvoření datové sady.
    
    1. Jakmile se datová sada zobrazí v seznamu, vyberte ji.
    
    1. Přečtěte si **Náhled dat**  a ujistěte se, že jste nezahrnuli **Day_of_week** pak vyberte **Zavřít**.

    1. Vyberte  **Další**.

## <a name="configure-run"></a>Konfigurace běhu

Po načtení a konfiguraci dat můžete nastavit experiment. Tato instalace zahrnuje úkoly návrhu, jako je například, výběr velikosti prostředí COMPUTE a určení toho, který sloupec chcete odhadnout. 

1. Zvolte přepínač **vytvořit nový** .

1. Naplňte formulář pro **spuštění konfigurace** následujícím způsobem:
    1. Zadejte tento název experimentu: `my-1st-automl-experiment`

    1. Jako cílový sloupec vyberte **y** , co chcete předpovědět. Tento sloupec indikuje, jestli se klient přihlásil k odběru termínu nebo ne.
    
    1. Vyberte **+ vytvořit nový výpočetní** výkon a nakonfigurujte svůj cíl služby Compute. Výpočetní cíl je místní nebo cloudové prostředí, které se používá ke spuštění školicího skriptu nebo hostování nasazení služby. Pro tento experiment používáme cloudový výpočetní výkon. 
        1. Naplňte formulář **virtuálního počítače** a nastavte výpočetní výkon.

            Pole | Description | Hodnota pro kurz
            ----|---|---
            &nbsp;Priorita virtuálního počítače &nbsp; |Vyberte prioritu, kterou by měl váš experiment mít.| Vyhrazená
            &nbsp;Typ virtuálního počítače &nbsp;| Vyberte typ virtuálního počítače pro výpočetní výkon.|PROCESOR (jednotka ústředního zpracování)
            &nbsp;Velikost virtuálního počítače &nbsp;| Vyberte velikost virtuálního počítače pro výpočetní výkon. Seznam doporučených velikostí je k dispozici na základě vašich dat a typu experimentu. |Standard_DS12_V2
        
        1. Výběrem možnosti **Další** naplňte **formulář Konfigurovat nastavení**.
        
            Pole | Description | Hodnota pro kurz
            ----|---|---
            Název výpočetních prostředků |  Jedinečný název, který identifikuje váš výpočetní kontext. | automl – COMPUTE
            Minimální/maximální počet uzlů| Chcete-li profilovat data, je nutné zadat 1 nebo více uzlů.|Minimální počet uzlů: 1<br>Maximální počet uzlů: 6
            Počet sekund nečinnosti před horizontálním navýšení kapacity | Doba nečinnosti před tím, než se cluster automaticky škáluje na minimální počet uzlů.|120 (výchozí)
            Rozšířená nastavení | Nastavení pro konfiguraci a autorizaci virtuální sítě pro svůj experiment.| Žádné               

        1. Vyberte **vytvořit** a vytvořte tak cíl služby Compute. 

            **Dokončení této akce trvá několik minut.** 

             ![Stránka Nastavení](./media/tutorial-first-experiment-automated-ml/compute-settings.png)

        1. Po vytvoření vyberte nový cíl služby COMPUTE z rozevíracího seznamu.

    1. Vyberte **Další**.

1. Ve formuláři **typ úlohy a nastavení** dokončete instalaci automatizovaného experimentu ml zadáním typu úlohy Machine Learning a nastavení konfigurace.
    
    1.  Jako typ úlohy strojového učení vyberte **klasifikace** .

    1. Vyberte **Zobrazit další nastavení konfigurace** a vyplňte pole následujícím způsobem. Tato nastavení mají lepší kontrolu nad úlohou školení. V opačném případě se výchozí hodnoty aplikují na základě experimentů a výběrů dat.

        Další &nbsp; Konfigurace|Description|Hodnota &nbsp; pro &nbsp; kurz
        ------|---------|---
        Primární metrika| Metrika vyhodnocení, podle které se algoritmus strojového učení měří.|AUC_weighted
        Vysvětlete nejlepší model| Automaticky zobrazuje vysvětlení nejlepšího modelu vytvořeného pomocí automatizovaného ML.| Povolit
        Blokované algoritmy | Algoritmy, které chcete vyloučit z úlohy školení| Žádné
        Výstupní kritérium| Pokud je splněno kritérium, úloha školení se zastaví. |&nbsp;Čas úlohy školení &nbsp; (hodiny): 1 <br> &nbsp;Prahová hodnota skóre metriky &nbsp; : žádné
        Ověřování | Vyberte typ křížového ověření a počet testů.|Typ ověřování:<br>&nbsp;k skládání &nbsp; křížového ověřování <br> <br> Počet ověření: 2
        Souběžnost| Maximální počet paralelních iterací provedených na iteraci| Maximální počet &nbsp; souběžných &nbsp; iterací: 5
        
        Vyberte **Uložit**.
    
1. Vyberte **Dokončit** pro spuštění experimentu. Po zahájení přípravy experimentu se otevře obrazovka s **podrobnostmi o spuštění**  se **stavem spuštění** v horní části. Tento stav se aktualizuje v průběhu experimentu. Oznámení se zobrazí také v pravém horním rohu studia, aby se informovalo o stavu experimentu.

>[!IMPORTANT]
> Příprava na Příprava spuštění experimentu trvá **10-15 minut** .
> Po spuštění bude **pro každou iteraci trvat více než 2-3 minut**.  <br> <br>
> V produkčním prostředí byste pravděpodobně nemuseli trochu začít. Pro tento kurz ale doporučujeme začít zkoumat testované algoritmy na kartě **modely** , jak jsou dokončeny, zatímco ostatní stále běží. 

##  <a name="explore-models"></a>Prozkoumat modely

Přejděte na kartu **modely** a podívejte se na testované algoritmy (modely). Ve výchozím nastavení jsou modely seřazeny podle skóre metriky, jak jsou dokončeny. Pro účely tohoto kurzu je model, který vychází z nejvyšší úrovně na základě zvolené metriky **AUC_weighted** , v horní části seznamu.

Až budete čekat na dokončení všech modelů experimentů, vyberte **název algoritmu** dokončeného modelu a prozkoumejte jeho podrobnosti o výkonu. 

Následující naviguje **Podrobnosti** a karty **metriky** k zobrazení vlastností, metrik a výkonu vybraného modelu. 

![Podrobnosti spuštění iterace](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="model-explanations"></a>Vysvětlení modelu

Počkejte, až se modely dokončí, můžete se také podívat na vysvětlení modelů a zjistit, které datové funkce (RAW nebo inženýr) ovlivnily předpovědi konkrétního modelu. 

Tato vysvětlení modelu je možné vygenerovat na vyžádání a jsou shrnuty na řídicím panelu vysvětlení modelu, který je součástí karty **vysvětlení (Preview)** .

Chcete-li vygenerovat vysvětlení modelu, 
 
1. Vyberte **Spustit 1** v horní části pro přechod zpět na obrazovku **modely** . 
1. Vyberte kartu **modely** .
1. Pro tento kurz vyberte první MaxAbsScaler model **LightGBM** .
1. V horní části vyberte tlačítko **vysvětlit model** . Napravo se zobrazí podokno **vysvětlený model** . 
1. Vyberte **automl – výpočetní** prostředí, které jste předtím vytvořili. Tento výpočetní cluster iniciuje podřízený běh pro generování vysvětlení modelu.
1. V dolní části vyberte **vytvořit** . K hornímu okraji obrazovky se zobrazí zelená zpráva o úspěchu. 
    >[!NOTE]
    > Spuštění vysvětlení trvá přibližně 2-5 minut.
1. Vyberte tlačítko **vysvětlení (Preview)** . Tato karta se vyplní, jakmile se spuštění vysvětlení dokončí.
1. Na levé straně rozbalte podokno a vyberte řádek, který je v části **funkce** **nezpracovaný** . 
1. Na pravé straně vyberte kartu **důležitost agregované funkce** . Tento graf znázorňuje, které datové funkce ovlivnily předpovědi vybraného modelu. 

    V tomto příkladu se zdá, že *Doba trvání* má největší vliv na předpovědi tohoto modelu.
    
    ![Řídicí panel vysvětlení modelu](media/tutorial-first-experiment-automated-ml/model-explanation-dashboard.png)

## <a name="deploy-the-best-model"></a>Nasazení nejlepšího modelu

Automatizované rozhraní Machine Learning umožňuje nasadit nejlepší model jako webovou službu v několika krocích. Nasazení je integrací modelu, takže může předpovídat nová data a identifikovat potenciální oblasti příležitostí. 

Pro tento experiment nasazení do webové služby znamená, že finanční instituce teď má iterativní a škálovatelné webové řešení pro identifikaci potenciálních zákazníků s dlouhodobým vkladem. 

Zkontrolujte, jestli je váš experiment dokončený. Provedete to tak, že v horní části obrazovky vyberete **Spustit 1** a vrátíte se na nadřazenou stránku spuštění. V levém horním rohu obrazovky se zobrazí stav **dokončeno** . 

Po dokončení experimentu se stránka s **podrobnostmi** vyplní pomocí oddílu **nejlepšího Shrnutí modelu** . V tomto kontextu experimentu se **VotingEnsemble** považuje za nejlepší model na základě metriky **AUC_weighted** .  

Tento model nasadíme, ale doporučujeme, aby dokončení nasazení trvalo přibližně 20 minut. Proces nasazení zahrnuje několik kroků, včetně registrace modelu, generování prostředků a jejich konfigurace pro webovou službu.

1. Výběrem **VotingEnsemble** otevřete stránku specifickou pro daný model.

1. V levém horním rohu vyberte tlačítko **nasadit** .

1. Nasaďte podokno **nasadit model** následujícím způsobem:

    Pole| Hodnota
    ----|----
    Název nasazení| Moje automl – nasazení
    Popis nasazení| Moje první automatizované nasazení experimentu Machine Learning
    Typ výpočetních prostředků | Výběr instance služby Azure COMPUTE (ACI)
    Povolit ověřování| Zakázat. 
    Použití vlastních nasazení| Zakázat. Umožňuje automatické vygenerování výchozího souboru ovladače (skriptu bodování) a souboru prostředí. 
    
    V tomto příkladu používáme výchozí hodnoty uvedené v nabídce *Upřesnit* . 

1. Vyberte **Nasadit**.  

    V horní části obrazovky **spuštění** se zobrazí zelená zpráva o úspěchu a v podokně **Souhrn modelu** se zobrazí stavová zpráva v části **stav nasazení**. Pro pravidelnou kontrolu stavu nasazení vyberte **aktualizovat** .
    
Nyní máte provozní webovou službu, která generuje předpovědi. 

Pokud chcete získat další informace o tom, jak používat novou webovou službu, a testovat předpovědi pomocí integrované Azure Machine Learning podpory Power BI, přejděte k [**dalším krokům**](#next-steps) .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Soubory nasazení jsou větší než data a experimenty, takže se o jejich uložení budou mnohem lépe ukládat. Odstraňte pouze soubory nasazení, abyste minimalizovali náklady na váš účet, nebo pokud chcete zachovat pracovní prostor a soubory experimentů. Jinak odstraňte celou skupinu prostředků, pokud neplánujete použít žádný ze souborů.  

### <a name="delete-the-deployment-instance"></a>Odstraní instanci nasazení.

\/Pokud chcete zachovat skupinu prostředků a pracovní prostor pro jiné kurzy a průzkum, odstraňte jenom instanci nasazení z Azure Machine Learning na https:/ml.Azure.com/. 

1. Přejít na [Azure Machine Learning](https://ml.azure.com/). Přejděte do pracovního prostoru a vlevo pod podoknem **assety** vyberte **koncové body**. 

1. Vyberte nasazení, které chcete odstranit, a vyberte **Odstranit**. 

1. Vyberte **pokračovat**.

### <a name="delete-the-resource-group"></a>Odstranění skupiny prostředků

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu automatizovaného strojového učení jste pomocí automatizovaného rozhraní ML Azure Machine Learning vytvořili a nasadili klasifikační model. Další informace a další kroky najdete v těchto článcích:

> [!div class="nextstepaction"]
> [Využívání webové služby](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ Přečtěte si další informace o [automatizovaném strojovém učení](concept-automated-ml.md).
+ Další informace o metrikách klasifikace a grafech najdete v článku [vysvětlení výsledků automatizovaného strojového učení](how-to-understand-automated-ml.md) .
+ Přečtěte si další informace o [featurization](how-to-configure-auto-features.md#featurization).
+ Přečtěte si další informace o [profilování dat](how-to-connect-data-ui.md#profile).


>[!NOTE]
> Tato datová sada bank je k dispozici v rámci [licence Creative-@ (CCO: Public Domain)](https://creativecommons.org/publicdomain/zero/1.0/). Všechna práva k individuálnímu obsahu databáze jsou licencovaná v rámci [licence k obsahu databáze](https://creativecommons.org/publicdomain/zero/1.0/) a dostupná na [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Tato datová sada byla původně k dispozici v rámci [databáze UCI Machine Learning](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez a P. Rita. „A Data-Driven Approach to Predict the Success of Bank Telemarketing“ (Předpovídání úspěchu bankovního telemarketingu na základě dat). Decision Support Systems, Elsevier, 62:22-31, červen 2014
