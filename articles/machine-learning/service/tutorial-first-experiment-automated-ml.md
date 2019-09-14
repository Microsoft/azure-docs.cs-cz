---
title: Vytvoření prvního automatizovaného experimentu machine learningu
titleSuffix: Azure Machine Learning service
description: Naučte se, jak pomocí automatizovaného strojového učení na úvodní stránce pracovního prostoru Azure Machine Learning vytvořit klasifikační model a nasadit model klasifikace (Preview).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: e831b94099a8bdf96710fc8ae8915938bbe997c4
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984519"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Kurz: Vytvoření prvního modelu klasifikace pomocí automatizovaného strojového učení

V tomto kurzu se naučíte, jak vytvořit první automatizovaný experiment strojového učení prostřednictvím cílové stránky pracovního prostoru (Preview) bez psaní jediného řádku kódu. Tento příklad vytvoří model klasifikace, který předpovídá, jestli se klient přihlásí k odběru pevně stanoveného termínu s finanční institucí.

Pomocí automatizovaného strojového učení můžete automatizovat časově náročné úlohy. Automatizované Machine Learning rychle projde mnoho kombinací algoritmů a parametrů, které vám pomůžou najít nejlepší model na základě metriky úspěšnosti výběru.

V tomto kurzu se naučíte, jak provádět následující úlohy:

> [!div class="checklist"]
> * Vytvořte pracovní prostor služby Azure Machine Learning.
> * Spusťte automatizovaný experiment strojového učení.
> * Zobrazit podrobnosti experimentu.
> * Nasazení modelu.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree).

* Stáhněte si datový soubor [**bankmarketing_train. csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) . Sloupec **y** indikuje, jestli se zákazník přihlásil k odběru pevně stanoveného termínu, který se později identifikuje jako cílový sloupec pro předpovědi v tomto kurzu. 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Azure Machine Learning pracovní prostor je základní prostředek v cloudu, který používáte k experimentování, výuce a nasazování modelů strojového učení. Přijedná se o vaše předplatné Azure a skupinu prostředků k snadno spotřebovanému objektu ve službě. 

Pracovní prostor můžete vytvořit prostřednictvím Azure Portal, webové konzoly pro správu prostředků Azure. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Poznamenejte si svůj **pracovní prostor** a **předplatné**. Budete je potřebovat, abyste se ujistili, že vytvoříte experiment na správném místě. 

## <a name="create-and-run-the-experiment"></a>Vytvoření a spuštění experimentu

Na úvodní stránce pracovního prostoru jste dokončili následující kroky experimentování a spouštění, což je konsolidované rozhraní, které zahrnuje nástroje strojového učení, které slouží k provádění scénářů pro datové vědy u všech úrovní dovedností.

1. Přihlaste se na [úvodní stránku pracovního prostoru](https://ml.azure.com/workspaceportal/).

1. Vyberte své předplatné a pracovní prostor, který jste vytvořili.

1. Vyberte **Začínáme**.

1.  V části **vytváření obsahu** v levém podokně vyberte možnost **automatizovaná ml** .
Zobrazí se obrazovka **Začínáme** , protože se jedná o první experiment s automatizovaným Machine Learning.

    ![Azure Machine Learning Studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Vyberte **vytvořit experiment**. 

1. Jako název experimentu zadejte **My-1st-automl-experiment** .

1. Vyberte **vytvořit nový výpočetní**výkon. 

    1. Nakonfigurujte výpočetní kontext pro tento experiment.
        
        Pole | Value
        ----|---
        Název výpočetních prostředků služby Machine Learning |  Zadejte jedinečný název, který identifikuje váš výpočetní kontext. V tomto příkladu použijte **automl-COMPUTE**.
        Velikost virtuálního počítače| Vyberte velikost virtuálního počítače pro výpočetní výkon. Použijte výchozí **Standard_DS12_V2**.
        Další nastavení| *Minimální uzel*: 1. Chcete-li povolit profilaci dat, je nutné mít jeden nebo více uzlů. <br> *Maximální počet uzlů*: 6.
 
    1. Pokud chcete vytvořit nový výpočetní výkon, vyberte **vytvořit**. Dokončení této akce trvá několik minut. 

    1. Až se vytváření dokončí, vyberte v rozevíracím seznamu nový výpočetní výkon a pak vyberte **Další**.

    >[!NOTE]
    >V tomto kurzu použijete výchozí účet úložiště a kontejner vytvořený s novým výpočetním prostředím. Automaticky se naplní ve formuláři.

1. Vyberte **Odeslat z místního souboru**. Odsud vytvoříte novou datovou sadu se souborem **bankmarketing_train. csv** , který jste předtím stáhli pro tento kurz. 

    1. Vyberte **Procházet** a pak na místním počítači vyberte soubor **bankmarketing_train. csv** . 

    1. Poskytněte datovou sadu jedinečný název a zadejte volitelný popis. 

    1. Vyberte **Další** a nahrajte ho do výchozího kontejneru, který se automaticky nastavil během vytváření pracovního prostoru. Verze Public Preview podporuje pouze nahrávání místních souborů. 

    1. Po dokončení nahrávání se formulář **nastavení a náhled** vyplní inteligentně podle typu souboru. Zajistěte, aby byl formulář vyplněn následujícím způsobem.
        
        Pole|Value
        ---|---
        Formát souboru| Oddělených
        Oddělovač| Čárka
        Kódování| UTF-8
        Záhlaví sloupců| Všechny soubory mají stejná záhlaví.
        Přeskočit řádky | Žádné

        >[!NOTE]
        > Pokud se některé nastavení v tomto formuláři aktualizuje, verze Preview se odpovídajícím způsobem aktualizuje.

        Vyberte **Další**.
    

    1. Formulář **schématu** umožňuje další konfiguraci dat pro tento experiment. V tomto příkladu vyberte přepínač pro funkci **day_of_week** , a tak, aby se pro tento experiment nezahrnul. Vyberte **Hotovo**, aby se dokončilo nahrávání souboru a vytváření datové sady pro váš experiment.

        ![Konfigurace karty Preview](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

        
1. Jako úkol předpovědi vyberte **klasifikace** .

1. Jako cílový sloupec vyberte **y** , co chcete předpovědět. Tento sloupec indikuje, jestli se klient přihlásil k odběru termínu nebo ne.

1. Rozbalte položku **Pokročilá nastavení** a vyplňte pole následujícím způsobem.

    Pokročilá nastavení|Value
    ------|------
    Primární metrika| AUC_weighted 
    Výstupní kritéria| Pokud je splněno kterékoli z těchto kritérií, úloha školení skončí před úplným dokončením: <br> *Doba úlohy školení (minuty)* : 5  <br> *Maximální počet iterací*: 10 
    Předzpracování| Umožňuje předzpracování pomocí automatizovaného strojového učení. To zahrnuje automatické čištění dat, přípravu a transformaci, které generují syntetické funkce.
    Ověřování| Pro počet křížových validací vyberte K skládání křížového ověřování a **2** . 
    Souběžnost| U maximálního počtu souběžných iterací vyberte **5** .

   >[!NOTE]
   > Pro tento experiment nenastavíte prahovou hodnotu metriky nebo maximální počet jader na iterace. Neblokujte také algoritmy, které mají být testovány.

1. Pokud chcete experiment spustit, vyberte **Spustit** .

   Po zahájení experimentu se v horní části zobrazí prázdná obrazovka se stavovou zprávou.

Proces přípravy experimentu trvá několik minut. Po dokončení tohoto procesu se stavová zpráva změní na **spuštěno.**

##  <a name="view-experiment-details"></a>Zobrazit podrobnosti experimentu

Jak experiment probíhá, obrazovka aktualizuje **graf iterace** a **seznam iterací** o různé iterace (modely), které jsou spuštěny. Seznam iterací je v pořadí podle skóre metriky. Ve výchozím nastavení je model, který vychází z nejvyšší **AUC_weighted** metriky, v horní části seznamu.

>[!WARNING]
> Školicí úlohy povedou několik minut, než se dokončí spuštění každého kanálu.

[![Spustit řídicí panel podrobností](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>Nasazení modelu

Pomocí automatizovaného strojového učení na úvodní stránce pracovního prostoru můžete nasadit nejlepší model jako webovou službu pro předpověď nových dat a identifikaci potenciálních oblastí příležitostí. Pro tento experiment nasazení znamená, že finanční instituce teď má iterativní a škálovatelné řešení pro identifikaci potenciálních zákazníků s dlouhodobým vkladem.

V tomto kontextu experimentu se **VotingEnsemble** považuje za nejlepší model, a to na základě metriky **AUC_weighted** .  Tento model nasadíme, ale doporučujeme, aby dokončení nasazení trvalo přibližně 20 minut.

1. Na stránce **Podrobnosti o spuštění** vyberte v pravém horním rohu tlačítko **nasadit nejlepší model** .

1. Nasaďte **nejvhodnější podokno model nasazení** následujícím způsobem:

    Pole| Value
    ----|----
    Název nasazení| Moje automl – nasazení
    Popis nasazení| Moje první automatizované nasazení experimentu Machine Learning
    Skript bodování| AutoGenerate
    Skript prostředí| AutoGenerate
    
1. Vyberte **Nasadit**.

    Po úspěšném dokončení nasazení se zobrazí zpráva o dokončení nasazení.
    
Nyní máte provozní webovou službu, která generuje předpovědi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Soubory nasazení jsou větší než data a experimenty, takže se o jejich uložení budou mnohem lépe ukládat. Odstraňte pouze soubory nasazení, abyste minimalizovali náklady na váš účet, nebo pokud chcete zachovat pracovní prostor a soubory experimentů. Jinak odstraňte celou skupinu prostředků, pokud neplánujete použít žádný ze souborů.  

### <a name="delete-the-deployment-instance"></a>Odstraní instanci nasazení.

Pokud chcete zachovat skupinu prostředků a pracovní prostor pro další kurzy a průzkum, odstraňte jenom instanci nasazení z Azure Portal. 

1. Přejděte na [Azure Portal](https://portal.azure.com//). V podokně **assety (prostředky** ) přejděte do svého pracovního prostoru a vlevo vyberte **nasazení**. 

1. Vyberte nasazení, které chcete odstranit, a vyberte **Odstranit**. 

1. Vyberte **pokračovat**.

### <a name="delete-the-resource-group"></a>Odstranit skupinu prostředků

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu automatizovaného strojového učení jste pomocí cílové stránky pracovního prostoru vytvořili a nasadili klasifikační model. Další informace a další kroky najdete v těchto článcích:

> [!div class="nextstepaction"]
> [Využití webové služby](how-to-consume-web-service.md)

+ Přečtěte si další informace o [předzpracování](how-to-create-portal-experiments.md#preprocess).
+ Přečtěte si další informace o [profilování dat](how-to-create-portal-experiments.md#profile).
+ Přečtěte si další informace o [automatizovaném strojovém učení](concept-automated-ml.md).

>[!NOTE]
> Tato datová sada bank je dostupná v rámci [Creative navýšení (CCO: Veřejná doména)](https://creativecommons.org/publicdomain/zero/1.0/). Všechna práva k individuálnímu obsahu databáze jsou licencovaná v rámci [licence k obsahu databáze](https://creativecommons.org/publicdomain/zero/1.0/) a dostupná na [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Tato datová sada byla původně k dispozici v rámci [databáze UCI Machine Learning](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez a P. Rita. Přístup na základě dat, který vám umožní předpovědět úspěšnost bankového uvádění na trh. Systémy podpory pro rozhodování, Elsevier, 62:22-31, červen 2014.
