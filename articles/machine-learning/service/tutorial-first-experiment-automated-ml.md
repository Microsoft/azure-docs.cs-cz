---
title: Vytvoření prvního automatizovaného experimentu machine learningu
titleSuffix: Azure Machine Learning service
description: Naučte se, jak pomocí automatizovaného strojového učení v Azure Portal naučit a nasazovat model klasifikace.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 07/23/2019
ms.openlocfilehash: 7ef19db472b30d82f14a5dd650cb8f4cb1f3ed3a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990075"
---
# <a name="tutorial-use-automated-machine-learning-to-train-and-deploy-your-first-classification-model-preview"></a>Kurz: Použití automatizovaného strojového učení ke školení a nasazení prvního klasifikačního modelu (Preview)

V tomto kurzu se naučíte, jak vytvořit první automatizovaný experiment strojového učení v Azure Portal. Tento příklad vytvoří model klasifikace, který předpovídá, jestli se klient přihlásí k odběru termínu vkladu s bankou.

Díky funkcím automatizovaného strojového učení služby a Azure Portal zahájíte proces automatizovaného strojového učení. Výběr algoritmu a ladění parametrů se provádí za vás. Automatizovaná technika strojového učení projde více kombinací algoritmů a jednoduchých parametrů, dokud nenajde nejlepší model na základě vašeho kritéria, a to vše bez psaní jediného řádku kódu.

V tomto kurzu se seznámíte s následujícími úlohami:

> [!div class="checklist"]
> * Nakonfigurujte pracovní prostor služby Azure Machine Learning.
> * Vytvořte experiment.
> * Automaticky naučit model klasifikace.
> * Zobrazit podrobnosti o školicím běhu
> * Nasazení modelu.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree).

* Datový soubor **bankmarketing_train. csv** . [Stáhněte si ho](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>Vytvoření experimentu

1. Přejít k levému podoknu pracovního prostoru. V části **vytváření obsahu (Preview)** vyberte **Automated Machine Learning** .

    ![Azure Portal navigační podokno](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

    Vzhledem k tomu, že se jedná o první experiment s automatizovaným Machine Learning, uvidíte **úvodní obrazovku automaticky Machine Learning** . 

1. Vyberte **vytvořit experiment**. Pak jako název experimentu zadejte **My-1st-automl-experiment** .

1. Vyberte **vytvořit nový výpočetní** výkon a nakonfigurujte pro tento experiment výpočetní kontext.

    Pole| Value
    ---|---
    Název výpočetních prostředků služby Machine Learning| Zadejte jedinečný název, který identifikuje váš výpočetní kontext. V tomto příkladu používáme **automl-COMPUTE**.
    Velikost virtuálního počítače| Vyberte velikost virtuálního počítače pro výpočetní výkon. Používáme **Standard_DS12_V2**.
    Další nastavení| *Minimální uzel*: 1. Chcete-li povolit profilaci dat, je nutné mít jeden nebo více uzlů. <br> *Maximální počet uzlů*: 6. 

    Pokud chcete vytvořit nový výpočetní výkon, vyberte **vytvořit**. To může chvíli trvat. 

    Až se vytváření dokončí, vyberte v rozevíracím seznamu nový výpočetní výkon a pak vyberte **Další**.

1. V tomto kurzu použijeme výchozí účet úložiště a kontejner vytvořený s vaším novým výpočetním prostředím. Automaticky se naplní ve formuláři.

1. Vyberte **nahrát** a zvolte soubor **bankmarketing_train. csv** z místního počítače a nahrajte ho do výchozího kontejneru. Verze Public Preview podporuje jenom místní nahrávání souborů a účty úložiště Azure Blob. Po dokončení nahrávání vyberte soubor ze seznamu. 

    [![Vybrat datový soubor](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

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

      ![Spustit přípravu](media/tutorial-1st-experiment-automated-ml/run-preparing.png)
      
Proces přípravy experimentu trvá několik minut. Po dokončení procesu se stavová zpráva změní na spuštěno.

##  <a name="view-experiment-details"></a>Zobrazit podrobnosti experimentu

Jak experiment probíhá, obrazovka s podrobnostmi o **spuštění** aktualizuje graf iterace a seznam pomocí různých iterací (modelů), které jsou spuštěny. Seznam iterací je v pořadí podle skóre metriky. Ve výchozím nastavení je model, který vychází z nejvyšší **AUC_weighted** metriky, v horní části seznamu.

>[!TIP]
> Školicí úlohy povedou několik minut, než se dokončí spuštění každého kanálu.

[![Spustit řídicí panel podrobností](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>Nasazení modelu

Pro tento experiment se **VotingEnsemble** považuje za nejlepší model, a to na základě metriky **AUC_weighted** . Pomocí automatizovaného strojového učení v Azure Portal můžeme tento model nasadit jako webovou službu a předpovídat si nová data. 

1. Na stránce s podrobnostmi o **spuštění** vyberte tlačítko **nasadit nejlepší model** .

1. Nasaďte **nejvhodnější podokno model nasazení** následujícím způsobem:

    Pole| Value
    ----|----
    Název nasazení| Moje automl – nasazení
    Popis nasazení| Moje první automatizované nasazení experimentu Machine Learning
    Skript bodování| AutoGenerate
    Skript prostředí| AutoGenerate
    
1. Vyberte **Nasadit**. Dokončení nasazení trvá přibližně 20 minut.

    Po úspěšném dokončení nasazení se zobrazí následující zpráva:

    ![Nasazení dokončeno](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    A to je vše! Máte provozní webovou službu, která generuje předpovědi.

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

+ Naučte se [využívat webovou službu](how-to-consume-web-service.md).
+ Přečtěte si [](how-to-create-portal-experiments.md#preprocess)Další informace o předzpracování.
+ Přečtěte si další informace o [profilování dat](how-to-create-portal-experiments.md#profile).
+ Přečtěte si další informace o [automatizovaném strojovém učení](concept-automated-ml.md).

>[!NOTE]
> Tato datová sada bank je dostupná v rámci [Creative navýšení (CCO: Veřejná doména)](https://creativecommons.org/publicdomain/zero/1.0/). Všechna práva k individuálnímu obsahu databáze jsou licencovaná v rámci [licence k obsahu databáze](https://creativecommons.org/publicdomain/zero/1.0/) a dostupná na [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Tato datová sada byla původně k dispozici v rámci [databáze UCI Machine Learning](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> Citujte prosím následující práci: <br> [Moro et al., 2014] S. Moro, P. Cortez a P. Rita. Přístup na základě dat, který vám umožní předpovědět úspěšnost bankového uvádění na trh. Systémy podpory pro rozhodování, Elsevier, 62:22-31, červen 2014.
