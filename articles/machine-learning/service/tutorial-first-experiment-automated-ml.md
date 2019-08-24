---
title: Vytvoření prvního automatizovaného experimentu machine learningu
titleSuffix: Azure Machine Learning service
description: Naučte se, jak pomocí automatizovaného strojového učení v Azure Portal naučit a nasazovat model klasifikace.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 08/14/2019
ms.openlocfilehash: 01228dc01b8006a0a2476ddbbd6fa8ff430e280a
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982762"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Kurz: Vytvoření prvního modelu klasifikace pomocí automatizovaného strojového učení

V tomto kurzu se naučíte, jak vytvořit první automatizovaný experiment strojového učení ve Azure Portal (Preview), aniž byste museli psát jediný řádek kódu. Tento příklad vytvoří model klasifikace, který předpovídá, jestli se klient přihlásí k odběru pevně stanoveného termínu s finanční institucí.

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

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-and-run-the-experiment"></a>Vytvoření a spuštění experimentu

Tento postup vás provede procesem nastavování z výběru dat a výběru primárního typu metriky a modelu. 

1. Přejít k levému podoknu pracovního prostoru. V části **vytváření obsahu (Preview)** vyberte **Automated Machine Learning** .
Zobrazí se úvodní obrazovka s **automatickým Machine Learning** , protože se jedná o první experiment s automatizovaným Machine Learningem.

    ![Azure Portal navigační podokno](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

1. Vyberte **vytvořit experiment**. Pak jako název experimentu zadejte **My-1st-automl-experiment** .

1. Vyberte **vytvořit nový výpočetní** výkon a nakonfigurujte pro tento experiment výpočetní kontext.

    Pole| Value
    ---|---
    Název výpočetních prostředků služby Machine Learning| Zadejte jedinečný název, který identifikuje váš výpočetní kontext. V tomto příkladu používáme **automl-COMPUTE**.
    Velikost virtuálního počítače| Vyberte velikost virtuálního počítače pro výpočetní výkon. Používáme **Standard_DS12_V2**.
    Další nastavení| *Minimální uzel*: 1. Chcete-li povolit profilaci dat, je nutné mít jeden nebo více uzlů. <br> *Maximální počet uzlů*: 6. 

    Pokud chcete vytvořit nový výpočetní výkon, vyberte **vytvořit**. To může chvíli trvat. 

    Až se vytváření dokončí, vyberte v rozevíracím seznamu nový výpočetní výkon a pak vyberte **Další**.

    >[!NOTE]
    >V tomto kurzu použijeme výchozí účet úložiště a kontejner vytvořený s vaším novým výpočetním prostředím. Automaticky se naplní ve formuláři.

1. Vyberte **nahrát** a zvolte soubor **bankmarketing_train. csv** z místního počítače a nahrajte ho do výchozího kontejneru. Verze Public Preview podporuje jenom místní nahrávání souborů a účty úložiště Azure Blob. Po dokončení nahrávání vyberte soubor ze seznamu. 

1. Karta **Náhled** nám umožňuje dále konfigurovat naše data pro tento experiment.

    Na kartě **Náhled** označte, že data obsahují záhlaví. Služba ve výchozím nastavení zahrnuje všechny funkce (sloupce) pro školení. V tomto příkladu se posuňte doprava a **ignorujte** funkci **day_of_week** .

    ![Konfigurace karty Preview](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)

    >[!NOTE]
    > Profilování dat není k dispozici u výpočtů s nulovými minimálními uzly.

1. Jako úkol předpovědi vyberte **klasifikace** .

1. Jako cílový sloupec vyberte **y** , kde chceme udělat předpovědi. Tento sloupec indikuje, jestli se klient přihlásil k odběru termínu nebo ne.

1. Rozbalte položku **Pokročilá nastavení** a vyplňte pole následujícím způsobem.

    Pokročilá nastavení|Value
    ------|------
    Primární metrika| AUC_weighted 
    Výstupní kritéria| Pokud je splněno kterékoli z těchto kritérií, úloha školení skončí před úplným dokončením: <br> *Doba úlohy školení (minuty)* : 5  <br> *Maximální počet iterací*: 10 
    Předzpracování| Umožňuje předzpracování pomocí automatizovaného strojového učení. To zahrnuje automatické čištění dat, přípravu a transformaci, které generují syntetické funkce.
    Ověřování| Pro počet křížových validací vyberte K skládání křížového ověřování a **2** . 
    Souběžnost| U maximálního počtu souběžných iterací vyberte **5** .

   >[!NOTE]
   > Pro tento experiment nenastavíme prahovou hodnotu metriky nebo maximální počet jader na iterace. Neblokuje se také testování algoritmů.

1. Pokud chcete experiment spustit, vyberte **Spustit** .

   Po zahájení experimentu se v horní části zobrazí prázdná obrazovka s podrobnostmi o **spuštění** s následujícím stavem.
      
Proces přípravy experimentu trvá několik minut. Po dokončení procesu se stavová zpráva změní na spuštěno.

##  <a name="view-experiment-details"></a>Zobrazit podrobnosti experimentu

Jak experiment probíhá, obrazovka s podrobnostmi o **spuštění** aktualizuje graf iterace a seznam pomocí různých iterací (modelů), které jsou spuštěny. Seznam iterací je v pořadí podle skóre metriky. Ve výchozím nastavení je model, který vychází z nejvyšší **AUC_weighted** metriky, v horní části seznamu.

>[!TIP]
> Školicí úlohy povedou několik minut, než se dokončí spuštění každého kanálu.

[![Spustit řídicí panel podrobností](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>Nasazení modelu

Pomocí automatizovaného strojového učení v Azure Portal můžeme nasadit nejlepší model jako webovou službu pro předpověď nových dat a identifikaci potenciálních oblastí příležitostí. Pro tento experiment nasazení znamená, že finanční instituce teď má iterativní a škálovatelné řešení pro identifikaci potenciálních zákazníků s dlouhodobým vkladem.

V tomto kontextu experimentu se **VotingEnsemble** považuje za nejlepší model, a to na základě metriky **AUC_weighted** .  Tento model nasadíme, ale doporučujeme, aby dokončení nasazení trvalo přibližně 20 minut.

1. Na stránce s podrobnostmi o **spuštění** vyberte tlačítko **nasadit nejlepší model** .

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

1. V levém podokně **assety** a vyberte **nasazení**. 

1. Vyberte nasazení, které chcete odstranit, a vyberte **Odstranit**. 

1. Vyberte **pokračovat**.

### <a name="delete-the-resource-group"></a>Odstranit skupinu prostředků

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu automatizovaného strojového učení jste použili Azure Portal k vytvoření a nasazení klasifikačního modelu. Další informace a další kroky najdete v těchto článcích:

> [!div class="nextstepaction"]
> [Využití webové služby](how-to-consume-web-service.md)

+ Přečtěte si [](how-to-create-portal-experiments.md#preprocess)Další informace o předzpracování.
+ Přečtěte si další informace o [profilování dat](how-to-create-portal-experiments.md#profile).
+ Přečtěte si další informace o [automatizovaném strojovém učení](concept-automated-ml.md).

>[!NOTE]
> Tato datová sada bank je dostupná v rámci [Creative navýšení (CCO: Veřejná doména)](https://creativecommons.org/publicdomain/zero/1.0/). Všechna práva k individuálnímu obsahu databáze jsou licencovaná v rámci [licence k obsahu databáze](https://creativecommons.org/publicdomain/zero/1.0/) a dostupná na [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Tato datová sada byla původně k dispozici v rámci [databáze UCI Machine Learning](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> Citujte prosím následující práci: <br> [Moro et al., 2014] S. Moro, P. Cortez a P. Rita. Přístup na základě dat, který vám umožní předpovědět úspěšnost bankového uvádění na trh. Systémy podpory pro rozhodování, Elsevier, 62:22-31, červen 2014.
