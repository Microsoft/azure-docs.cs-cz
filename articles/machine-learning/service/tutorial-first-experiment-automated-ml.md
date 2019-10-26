---
title: Vytvořte si první automatizovaný experiment klasifikace ML.
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí automatizovaného strojového učení na úvodní stránce pracovního prostoru Azure Machine Learning vytvořit klasifikační model a nasadit model klasifikace (Preview).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/26/2019
ms.openlocfilehash: 726ac906b5f237cfc40a8399b7b1bef3071f98f3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901805"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Kurz: vytvoření prvního modelu klasifikace pomocí automatizovaného strojového učení

V tomto kurzu se naučíte, jak vytvořit první automatizovaný experiment strojového učení prostřednictvím cílové stránky pracovního prostoru (Preview) bez psaní jediného řádku kódu. Tento příklad vytvoří model klasifikace, který předpovídá, jestli se klient přihlásí k odběru pevně stanoveného termínu s finanční institucí.

Pomocí automatizovaného strojového učení můžete automatizovat časově náročné úlohy. Automatizované Machine Learning rychle projde mnoho kombinací algoritmů a parametrů, které vám pomůžou najít nejlepší model na základě metriky úspěšnosti výběru.

V tomto kurzu se naučíte, jak provádět následující úlohy:

> [!div class="checklist"]
> * Vytvořte pracovní prostor Azure Machine Learning.
> * Spusťte automatizovaný experiment strojového učení.
> * Zobrazit podrobnosti experimentu.
> * Nasaďte model.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree).

* Stáhněte si datový soubor [**bankmarketing_train. csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) . Sloupec **y** indikuje, jestli se zákazník přihlásil k odběru pevně stanoveného termínu, který se později identifikuje jako cílový sloupec pro předpovědi v tomto kurzu. 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Azure Machine Learning pracovní prostor je základní prostředek v cloudu, který používáte k experimentování, výuce a nasazování modelů strojového učení. Přijedná se o vaše předplatné Azure a skupinu prostředků k snadno spotřebovanému objektu ve službě. 

Pracovní prostor můžete vytvořit prostřednictvím Azure Portal, webové konzoly pro správu prostředků Azure. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Poznamenejte si svůj **pracovní prostor** a **předplatné**. Budete je potřebovat, abyste se ujistili, že vytvoříte experiment na správném místě. 

## <a name="create-and-run-the-experiment"></a>Vytvoření a spuštění experimentu

Na úvodní stránce pracovního prostoru jste dokončili následující kroky experimentování a spouštění, což je konsolidované rozhraní, které zahrnuje nástroje strojového učení, které slouží k provádění scénářů pro datové vědy u všech úrovní dovedností. Cílová stránka pracovního prostoru není podporována v prohlížečích aplikace Internet Explorer.

1. Přihlaste se na [úvodní stránku pracovního prostoru](https://ml.azure.com/workspaceportal/).

1. Vyberte své předplatné a pracovní prostor, který jste vytvořili.

1. Vyberte **Začínáme**.

1. V levém podokně vyberte v části **Autor** možnost **automatizovaná ml** .

   Vzhledem k tomu, že se jedná o první automatizovaný experiment ML, uvidíte obrazovku Začínáme.

   ![Azure Machine Learning Studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Vyberte **vytvořit experiment**. 

1. Zadejte tento název experimentu: `my-1st-automl-experiment`

1. Vyberte **vytvořit nový výpočetní** výkon a nakonfigurujte svůj cíl služby Compute. Cíl výpočetní služby je místní nebo cloudové prostředí prostředků, které se používá ke spuštění školicího skriptu nebo hostování nasazení služby. V tomto experimentu používáme cloudový výpočetní výkon. 

   Pole | Popis | Hodnota pro kurz
   ----|---|---
   Název výpočtu |Jedinečný název, který identifikuje váš výpočetní kontext.|automl – COMPUTE
   Velikost&nbsp;virtuálního&nbsp;ového počítače| Vyberte velikost virtuálního počítače pro výpočetní výkon.|Standard_DS12_V2
   Minimální/maximální počet uzlů (v rozšířených nastaveních)| Chcete-li profilovat data, je nutné zadat 1 nebo více uzlů.|Minimální počet uzlů: 1<br>Maximální počet uzlů: 6

   >[!NOTE]
   >V tomto kurzu použijete výchozí účet úložiště a kontejner vytvořený s novým výpočetním prostředím. Automaticky se naplní ve formuláři.
    
1. Pokud chcete získat cíl výpočtů, vyberte **vytvořit** . 

   **Dokončení této akce trvá několik minut.** 

1. Po vytvoření vyberte v rozevíracím seznamu nový cíl výpočtů a vyberte **Další**.

1. Vyberte **Odeslat z místního souboru** a začněte vytvářet novou datovou sadu. 

    1. Vyberte **Procházet**.
    
    1. V místním počítači vyberte soubor **bankmarketing_train. csv** . Jedná se o soubor, který jste stáhli jako [požadavek](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Poskytněte datovou sadu jedinečný název a zadejte volitelný popis. 

    1. V levém dolním rohu vyberte **Další** a nahrajte ho do výchozího kontejneru, který se automaticky nastavil během vytváření pracovního prostoru. Verze Public Preview podporuje pouze nahrávání místních souborů. 
    
       Po dokončení nahrávání se formulář nastavení a náhled předem vyplní podle typu souboru. 
       
    1. Ověřte, zda je formulář **nastavení a náhled** vyplněný následujícím způsobem, a vyberte možnost **Další**.
        
        Pole|Popis| Hodnota pro kurz
        ---|---|---
        Formát souboru|Definuje rozložení a typ dat uložených v souboru.| Oddělených
        Oddělovač|Jeden nebo více znaků pro určení hranice mezi &nbsp; samostatné, nezávislé oblasti v prostém textu nebo jiných datových proudech. |Tečkou
        Encoding|Určuje, jaká bitová tabulka schématu znaků má být použita ke čtení datové sady.| UTF-8
        Záhlaví sloupců| Určuje, jakým způsobem bude zpracována záhlaví datové sady (pokud existuje).| Všechny soubory mají stejná záhlaví.
        Přeskočit řádky | Určuje, kolik, pokud nějaký z nich je v datové sadě vynecháno.| Žádné
    
        ![Konfigurace karty Preview](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

1. Jako úkol předpovědi vyberte **klasifikace** .

1. Jako cílový sloupec vyberte **y** , co chcete předpovědět. Tento sloupec indikuje, jestli se klient přihlásil k odběru termínu nebo ne.

1. Rozbalte položku **Pokročilá nastavení** a vyplňte pole následujícím způsobem. Tato nastavení mají lepší kontrolu nad úlohou školení. V opačném případě se výchozí hodnoty aplikují na základě experimentů a výběrů dat.

   >[!NOTE]
   > V tomto kurzu nenastavíte prahovou hodnotu metriky nebo maximální počet jader na iteraci. Ani nebudete blokovat testování algoritmů.
   
   Rozšířené &nbsp;settings|Popis|Hodnota &nbsp;for &nbsp;tutorial
   ------|---------|---
   Primární metrika| Metrika vyhodnocení, podle které se algoritmus strojového učení měří.|AUC_weighted
   Výstupní kritéria| Pokud je splněno kritérium, úloha školení se zastaví. |&nbsp;úlohy školení&nbsp;čas: 5 <br> <br> Max &nbsp; # &nbsp;of &nbsp;iterations&#58;10
   Předzpracování| Umožňuje předzpracování pomocí automatizovaného strojového učení. To zahrnuje automatické čištění dat, přípravu a transformaci, které generují syntetické funkce.| Povolení
   Typ ověření | Vyberte typ křížového ověření.|K skládání křížového ověřování
   Počet ověření | Počet testů. | 2 křížová ověření 
   Souběžnost| Maximální počet souběžných iterací.|5
   
1. Pokud chcete experiment spustit, vyberte **Spustit** . Po zahájení přípravy experimentu se zobrazí obrazovka se stavovou zprávou.

>[!IMPORTANT]
> Příprava na Příprava spuštění experimentu trvá **10-15 minut** . Po spuštění bude **pro každou iteraci trvat více než 2-3 minut**.  
>
> V produkčním prostředí byste pravděpodobně nemuseli trochu začít. Pro tento kurz ale doporučujeme začít zkoumat výsledky iterace, jak jsou dokončené, zatímco ostatní pořád běží. 

##  <a name="explore-iteration-results"></a>Prozkoumat výsledky iterace

Jak experiment probíhá, obrazovka aktualizuje **graf iterace** a **seznam iterací** pomocí různých iterací (modelů) vytvořených, jak byly dokončeny. Ve výchozím nastavení jsou iterace seřazené podle skóre metriky. Pro účely tohoto kurzu je model, který vychází z nejvyšší úrovně na základě zvolené metriky **AUC_weighted** , v horní části seznamu.

Při čekání na dokončení všech iterací experimentu vyberte **název** dokončené iterace a prozkoumejte její podrobnosti o výkonu. 

Následující příklad znázorňuje grafy a spouštění metrik vygenerovaných pro každou iteraci, jako je například křivka odvolání přesnosti, nejasná matice, hodnocení vážené přesnosti atd. 

![Podrobnosti spuštění iterace](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>Nasazení modelu

Automatizované Machine Learning na úvodní stránce pracovního prostoru umožňuje nasadit nejlepší model jako webovou službu v několika krocích. Nasazení je integrací modelu, takže může předpovídat nová data a identifikovat potenciální oblasti příležitostí. Pro tento experiment nasazení do webové služby znamená, že finanční instituce teď má iterativní a škálovatelné webové řešení pro identifikaci potenciálních zákazníků s dlouhodobým vkladem. 

Po dokončení spuštění přejděte zpátky na stránku s podrobnostmi o **grafu iterace** a **seznamu iterací** . 

V tomto kontextu experimentu se **VotingEnsemble** považuje za nejlepší model, a to na základě metriky **AUC_weighted** .  Tento model nasadíme, ale doporučujeme, aby dokončení nasazení trvalo přibližně 20 minut. Proces nasazení zahrnuje několik kroků, včetně registrace modelu, generování prostředků a jejich konfigurace pro webovou službu.

1. V pravém horním rohu vyberte tlačítko **nasadit nejlepší model** .

1. Nasaďte **nejvhodnější podokno model nasazení** následujícím způsobem:

    Pole| Hodnota
    ----|----
    Název nasazení| Moje automl – nasazení
    Popis nasazení| Moje první automatizované nasazení experimentu Machine Learning
    Skript bodování| AutoGenerate
    Skript prostředí| AutoGenerate
    
1. Vyberte **Nasadit**.  

    Po úspěšném dokončení nasazení se zobrazí zpráva o dokončení nasazení.
    
Nyní máte provozní webovou službu, která generuje předpovědi.

Pokud chcete získat další informace o tom, jak používat novou webovou službu, a testovat předpovědi pomocí integrované Azure Machine Learning podpory Power BI, přejděte k [**dalším krokům**](#next-steps) .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Soubory nasazení jsou větší než data a experimenty, takže se o jejich uložení budou mnohem lépe ukládat. Odstraňte pouze soubory nasazení, abyste minimalizovali náklady na váš účet, nebo pokud chcete zachovat pracovní prostor a soubory experimentů. Jinak odstraňte celou skupinu prostředků, pokud neplánujete použít žádný ze souborů.  

### <a name="delete-the-deployment-instance"></a>Odstraní instanci nasazení.

Pokud chcete zachovat skupinu prostředků a pracovní prostor pro další kurzy a průzkum, odstraňte jenom instanci nasazení z Azure Portal. 

1. Přejděte na [Azure Portal](https://portal.azure.com//). V podokně **assety (prostředky** ) přejděte do svého pracovního prostoru a vlevo vyberte **nasazení**. 

1. Vyberte nasazení, které chcete odstranit, a vyberte **Odstranit**. 

1. Vyberte **pokračovat**.

### <a name="delete-the-resource-group"></a>Odstranit skupinu prostředků

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu automatizovaného strojového učení jste pomocí cílové stránky pracovního prostoru vytvořili a nasadili klasifikační model. Další informace a další kroky najdete v těchto článcích:

> [!div class="nextstepaction"]
> [Využití webové služby](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ Přečtěte si další informace o [předzpracování](how-to-create-portal-experiments.md#preprocess).
+ Přečtěte si další informace o [profilování dat](how-to-create-portal-experiments.md#profile).
+ Přečtěte si další informace o [automatizovaném strojovém učení](concept-automated-ml.md).
+ Další informace o metrikách a grafech klasifikace najdete v článku [vysvětlení výsledků automatizovaného strojového učení](how-to-understand-automated-ml.md#classification) .

>[!NOTE]
> Tato datová sada bank je k dispozici v rámci [licence Creative-@ (CCO: Public Domain)](https://creativecommons.org/publicdomain/zero/1.0/). Všechna práva k individuálnímu obsahu databáze jsou licencovaná v rámci [licence k obsahu databáze](https://creativecommons.org/publicdomain/zero/1.0/) a dostupná na [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Tato datová sada byla původně k dispozici v rámci [databáze UCI Machine Learning](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez a P. Rita. Přístup na základě dat, který vám umožní předpovědět úspěšnost bankového uvádění na trh. Systémy podpory pro rozhodování, Elsevier, 62:22-31, červen 2014.
