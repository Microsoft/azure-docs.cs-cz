---
title: Vytváření automatizovaných klasifikačních modelů ML
titleSuffix: Azure Machine Learning
description: Naučte se, jak naučit & nasazovat modely klasifikace pomocí rozhraní automatizovaného strojového učení pro Azure Machine Learning (Automated ML).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 02/04/2020
ms.openlocfilehash: 84d539f35919293522f05abdeabeca936138c140
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "79081620"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Kurz: vytvoření klasifikačního modelu pomocí automatizovaného ML v Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

V tomto kurzu se naučíte, jak vytvořit základní model klasifikace bez psaní jediného řádku kódu pomocí automatizovaného rozhraní machine learningu Azure Machine Learning. Tento model klasifikace předpovídá, jestli se klient přihlásí k odběru pevně stanoveného termínu s finanční institucí.

Pomocí automatizovaného strojového učení můžete automatizovat časově náročné úlohy. Automatizované Machine Learning rychle projde mnoho kombinací algoritmů a parametrů, které vám pomůžou najít nejlepší model na základě metriky úspěšnosti výběru.

V tomto kurzu se naučíte, jak provádět následující úlohy:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Azure Machine Learning.
> * Spusťte automatizovaný experiment strojového učení.
> * Zobrazit podrobnosti experimentu.
> * Nasazení modelu.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree).

* Stáhněte bankmarketing_train datový soubor [ **. csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) . Sloupec **y** indikuje, jestli se zákazník přihlásil k odběru pevně stanoveného termínu, který se později identifikuje jako cílový sloupec pro předpovědi v tomto kurzu. 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Azure Machine Learning pracovní prostor je základní prostředek v cloudu, který používáte k experimentování, výuce a nasazování modelů strojového učení. Přijedná se o vaše předplatné Azure a skupinu prostředků k snadno spotřebovanému objektu ve službě. 

Pracovní prostor můžete vytvořit prostřednictvím Azure Portal, webové konzoly pro správu prostředků Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> Poznamenejte si svůj **pracovní prostor** a **předplatné**. Budete je potřebovat, abyste se ujistili, že vytvoříte experiment na správném místě. 

## <a name="create-and-run-the-experiment"></a>Vytvoření a spuštění experimentu

Provedením následujících kroků experimentu a spuštění v Azure Machine Learning v https://ml.azure.comse jedná o konsolidované webové rozhraní, které zahrnuje nástroje strojového učení k provádění scénářů pro datové vědy u všech úrovní dovedností. Toto rozhraní není podporované v prohlížečích Internet Exploreru.

1. Přihlaste se k Azure Machine Learning v https://ml.azure.com.

1. Vyberte své předplatné a pracovní prostor, který jste vytvořili.

1. Vyberte **Začínáme**.

1. V levém podokně vyberte v části **Autor** možnost **automatizovaná ml** .

   Vzhledem k tomu, že se jedná o váš první automatizovaný experiment ML, zobrazí se prázdný seznam a odkazy na dokumentaci.

   ![Stránka Začínáme](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Vyberte **nový automatizovaný běh ml**. 

1. Vytvořte novou datovou sadu výběrem možnosti **místní soubory** z rozevíracího seznamu **+ vytvořit datovou sadu** . 

    1. Ve formuláři **základní informace** zadejte název datové sady a zadejte volitelný popis. Rozhraní Automated ML aktuálně podporuje pouze TabularDatasets, takže typ datové sady by měl být výchozí *tabulkou*.

    1. V levém dolním rohu vyberte **Další** .

    1. Na formuláři **úložiště dat a výběr souboru** vyberte výchozí úložiště dat, které se automaticky nastavilo během vytváření pracovního prostoru, **workspaceblobstore (Azure Blob Storage)** . Do tohoto místa nahrajete datový soubor, abyste ho mohli zpřístupnit vašemu pracovnímu prostoru.

    1. Vyberte **Procházet**.
    
    1. V místním počítači vyberte soubor **bankmarketing_train. csv** . Jedná se o soubor, který jste stáhli jako [požadavek](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Poskytněte datovou sadu jedinečný název a zadejte volitelný popis. 

    1. V levém dolním rohu vyberte **Další** a nahrajte ho do výchozího kontejneru, který se automaticky nastavil během vytváření pracovního prostoru.  
    
       Po dokončení nahrávání se formulář nastavení a náhled předem vyplní podle typu souboru. 
       
    1. Ověřte, zda je formulář **nastavení a náhled** vyplněný následujícím způsobem, a vyberte možnost **Další**.
        
        Pole|Popis| Hodnota pro kurz
        ---|---|---
        Formát souboru|Definuje rozložení a typ dat uložených v souboru.| Oddělených
        Oddělovač|Jeden nebo více znaků pro určení hranice mezi&nbsp; samostatné, nezávislé oblasti v prostém textu nebo jiných datových proudech. |Tečkou
        Kódování|Určuje, jaká bitová tabulka schématu znaků má být použita ke čtení datové sady.| UTF-8
        Záhlaví sloupců| Určuje, jakým způsobem bude zpracována záhlaví datové sady (pokud existuje).| Všechny soubory mají stejná záhlaví.
        Přeskočit řádky | Určuje, kolik, pokud nějaký z nich je v datové sadě vynecháno.| Žádná

    1. Formulář **schématu** umožňuje další konfiguraci dat pro tento experiment. V tomto příkladu vyberte přepínač přepínacího tlačítka pro funkci **day_of_week** , tak, aby se pro tento experiment nezahrnul. Vyberte **Další**.

        ![Konfigurace karty Preview](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)

    1. Na formuláři **potvrdit podrobnosti** ověřte, že se informace shodují s dříve vyplněnými **základními informacemi** o formulářích pro informace a **nastavení a ve verzi Preview** .
    1. Vyberte **vytvořit** a dokončete vytvoření datové sady.
    1. Jakmile se datová sada zobrazí v seznamu, vyberte ji.
    1. Přečtěte si **Náhled dat** a ujistěte se, že jste nezahrnuli **Day_of_week** pak vyberte **OK**.

    1. Vyberte **Další**.

1. Naplňte formulář pro **spuštění konfigurace** následujícím způsobem:
    1. Zadejte tento název experimentu: `my-1st-automl-experiment`

    1. Jako cílový sloupec vyberte **y** , co chcete předpovědět. Tento sloupec indikuje, jestli se klient přihlásil k odběru termínu nebo ne.
    1. Vyberte **vytvořit nový výpočetní** výkon a nakonfigurujte svůj cíl služby Compute. Výpočetní cíl je místní nebo cloudové prostředí, které se používá ke spuštění školicího skriptu nebo hostování nasazení služby. Pro tento experiment používáme cloudový výpočetní výkon. 

        Pole | Popis | Hodnota pro kurz
        ----|---|---
        Název výpočtu |Jedinečný název, který identifikuje váš výpočetní kontext.|automl – COMPUTE
        Velikost&nbsp;virtuálního&nbsp;ového počítače| Vyberte velikost virtuálního počítače pro výpočetní výkon.|Standard_DS12_V2
        Minimální/maximální počet uzlů (v rozšířených nastaveních)| Chcete-li profilovat data, je nutné zadat 1 nebo více uzlů.|Minimální počet uzlů: 1<br>Maximální počet uzlů: 6
  
        1. Pokud chcete získat cíl výpočtů, vyberte **vytvořit** . 

            **Dokončení této akce trvá několik minut.** 

        1. Po vytvoření vyberte nový cíl služby COMPUTE z rozevíracího seznamu.

    1. Vyberte **Další**.

1. Ve formuláři **typ úlohy a nastavení** vyberte jako typ úlohy strojového učení možnost **klasifikace** .

    1. Vyberte **Zobrazit další nastavení konfigurace** a vyplňte pole následujícím způsobem. Tato nastavení mají lepší kontrolu nad úlohou školení. V opačném případě se výchozí hodnoty aplikují na základě experimentů a výběrů dat.

        >[!NOTE]
        > V tomto kurzu nenastavíte prahovou hodnotu metriky nebo maximální počet jader na iteraci. Ani nebudete blokovat testování algoritmů.
   
        Další konfigurace&nbsp;|Popis|&nbsp;hodnoty pro&nbsp;kurz
        ------|---------|---
        Primární metrika| Metrika vyhodnocení, podle které se algoritmus strojového učení měří.|AUC_weighted
        Automaticky featurization| Umožňuje předzpracování. To zahrnuje automatické čištění dat, přípravu a transformaci, které generují syntetické funkce.| Povolení
        Blokované algoritmy | Algoritmy, které chcete vyloučit z úlohy školení| Žádná
        Výstupní kritérium| Pokud je splněno kritérium, úloha školení se zastaví. |&nbsp;úlohy školení&nbsp;čas (hodiny): 1 <br> &nbsp;prahová hodnota skóre&nbsp;metriky: žádné
        Ověření | Vyberte typ křížového ověření a počet testů.|Typ ověřování:<br>křížové ověření &nbsp;k-skládání&nbsp; <br> <br> Počet ověření: 2
        Souběžnost| Maximální počet paralelních iterací provedených na iteraci| Maximální&nbsp;souběžných&nbsp;ch iterací: 5
        
        Vyberte **Save** (Uložit).

1. Vyberte **Dokončit** pro spuštění experimentu. Po zahájení přípravy experimentu se otevře obrazovka s **podrobnostmi o spuštění** se **stavem spuštění** v horní části.

>[!IMPORTANT]
> Příprava na Příprava spuštění experimentu trvá **10-15 minut** .
> Po spuštění bude **pro každou iteraci trvat více než 2-3 minut**.  
> Pokud chcete zobrazit stav spuštění v průběhu experimentu, vyberte **aktualizovat** pravidelně.
>
> V produkčním prostředí byste pravděpodobně nemuseli trochu začít. Pro tento kurz ale doporučujeme začít zkoumat testované algoritmy na kartě **modely** , jak jsou dokončeny, zatímco ostatní stále běží. 

##  <a name="explore-models"></a>Prozkoumat modely

Přejděte na kartu **modely** a podívejte se na testované algoritmy (modely). Ve výchozím nastavení jsou modely seřazeny podle skóre metriky, jak jsou dokončeny. Pro účely tohoto kurzu je model, který vychází z nejvyšší úrovně na základě zvolené metriky **AUC_weighted** , v horní části seznamu.

Až budete čekat na dokončení všech modelů experimentů, vyberte **název algoritmu** dokončeného modelu a prozkoumejte jeho podrobnosti o výkonu. 

Následující navigace prochází pomocí karet **Podrobnosti modelu** a **vizualizace** k zobrazení vlastností vybraného modelu, metrik a grafů výkonu. 

![Podrobnosti spuštění iterace](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-best-model"></a>Nasazení nejlepšího modelu

Automatizované rozhraní Machine Learning umožňuje nasadit nejlepší model jako webovou službu v několika krocích. Nasazení je integrací modelu, takže může předpovídat nová data a identifikovat potenciální oblasti příležitostí. 

Pro tento experiment nasazení do webové služby znamená, že finanční instituce teď má iterativní a škálovatelné webové řešení pro identifikaci potenciálních zákazníků s dlouhodobým vkladem. 

Po dokončení spuštění přejděte zpět na stránku **podrobností o spuštění** a vyberte kartu **modely** .

V tomto kontextu experimentu se **VotingEnsemble** považuje za nejlepší model na základě metriky **AUC_weighted** .  Tento model nasadíme, ale doporučujeme, aby dokončení nasazení trvalo přibližně 20 minut. Proces nasazení zahrnuje několik kroků, včetně registrace modelu, generování prostředků a jejich konfigurace pro webovou službu.

1. V levém dolním rohu vyberte tlačítko **nasadit nejlepší model** .

1. Nasaďte podokno **nasadit model** následujícím způsobem:

    Pole| Hodnota
    ----|----
    Název nasazení| Moje automl – nasazení
    Popis nasazení| Moje první automatizované nasazení experimentu Machine Learning
    Typ výpočtu | Výběr instance služby Azure COMPUTE (ACI)
    Povolit ověřování| Dezaktivovat. 
    Použití vlastních nasazení| Dezaktivovat. Umožňuje automaticky vygenerovat výchozí soubor ovladače (skript bodování) a soubor prostředí. 
    
    V tomto příkladu používáme výchozí hodnoty uvedené v nabídce *Upřesnit* . 

1. Vyberte **Nasadit**.  

    V horní části obrazovky **spuštění** se zobrazí zelená zpráva o úspěchu a v podokně **doporučený model** se zobrazí stavová zpráva v části **stav nasazení**. Pro pravidelnou kontrolu stavu nasazení vyberte **aktualizovat** .
    
Nyní máte provozní webovou službu, která generuje předpovědi. 

Pokud chcete získat další informace o tom, jak používat novou webovou službu, a testovat předpovědi pomocí integrované Azure Machine Learning podpory Power BI, přejděte k [**dalším krokům**](#next-steps) .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Soubory nasazení jsou větší než data a experimenty, takže se o jejich uložení budou mnohem lépe ukládat. Odstraňte pouze soubory nasazení, abyste minimalizovali náklady na váš účet, nebo pokud chcete zachovat pracovní prostor a soubory experimentů. Jinak odstraňte celou skupinu prostředků, pokud neplánujete použít žádný ze souborů.  

### <a name="delete-the-deployment-instance"></a>Odstraní instanci nasazení.

Pokud chcete zachovat skupinu prostředků a pracovní prostor pro jiné kurzy a průzkum, odstraňte jenom instanci nasazení z Azure Machine Learning v https://ml.azure.com/. 

1. Přejít na Azure Machine Learning na https://ml.azure.com/. Přejděte do pracovního prostoru a vlevo pod podoknem **assety** vyberte **koncové body**. 

1. Vyberte nasazení, které chcete odstranit, a vyberte **Odstranit**. 

1. Vyberte **pokračovat**.

### <a name="delete-the-resource-group"></a>Odstranění skupiny prostředků

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu automatizovaného strojového učení jste pomocí automatizovaného rozhraní ML Azure Machine Learning vytvořili a nasadili klasifikační model. Další informace a další kroky najdete v těchto článcích:

> [!div class="nextstepaction"]
> [Využití webové služby](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Přečtěte si další informace o [automatizovaném strojovém učení](concept-automated-ml.md).
+ Další informace o metrikách a grafech klasifikace najdete v článku [vysvětlení výsledků automatizovaného strojového učení](how-to-understand-automated-ml.md#classification) . Další informace o [featurization](how-to-create-portal-experiments.md#featurization).
+ Přečtěte si další informace o [profilování dat](how-to-create-portal-experiments.md#profile).


>[!NOTE]
> Tato datová sada bank je k dispozici v rámci [licence Creative-@ (CCO: Public Domain)](https://creativecommons.org/publicdomain/zero/1.0/). Všechna práva k individuálnímu obsahu databáze jsou licencovaná v rámci [licence k obsahu databáze](https://creativecommons.org/publicdomain/zero/1.0/) a dostupná na [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Tato datová sada byla původně k dispozici v rámci [databáze UCI Machine Learning](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez a P. Rita. Přístup na základě dat, který vám umožní předpovědět úspěšnost bankového uvádění na trh. Systémy podpory pro rozhodování, Elsevier, 62:22-31, červen 2014.
