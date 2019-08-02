---
title: Vytvoření prvního automatizovaného experimentu machine learningu
titleSuffix: Azure Machine Learning service
description: Naučte se, jak pomocí automatizovaného strojového učení v Azure Portal naučit a nasazovat model klasifikace.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 07/23/2019
ms.openlocfilehash: 30dc731efdb6016f505b7a16860e0cb9c6480333
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716583"
---
# <a name="tutorial-use-automated-machine-learning-to-train-and-deploy-your-first-classification-model-preview"></a>Kurz: Použití automatizovaného strojového učení ke školení a nasazení prvního klasifikačního modelu (Preview)

V tomto kurzu se naučíte, jak vytvořit první automatizovaný experiment strojového učení v Azure Portal. Tento příklad vytvoří model klasifikace, který předpovídá, jestli se má klient přihlásit k odběru termínu vkladu s bankou. 

Díky funkcím automatizovaného strojového učení služby a Azure Portal spustíte proces automatizovaného strojového učení a pro vás dojde k výběru algoritmu a ladění parametrů. Automatizovaná technika strojového učení projde více kombinací algoritmů a jednoduchých parametrů, dokud nenajde nejlepší model na základě vašeho kritéria, a to vše bez nutnosti napsat jediný řádek kódu.

V tomto kurzu se seznámíte s následujícími úlohami:

> [!div class="checklist"]
> * Nakonfigurujte pracovní prostor služby Azure Machine Learning.
> * Vytvořte experiment.
> * Automatické učení klasifikačního modelu.
> * Zobrazit podrobnosti o školicím běhu
> * Nasazení modelu.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze služby Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Datový soubor **bankmarketing_train. csv** . Stáhněte si ho [tady](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>Vytvoření experimentu

1. Přejděte do levého podokna pracovního prostoru. V části **vytváření obsahu (Preview)** vyberte **automatizované Machine Learning** .

    ![Azure Portal navigační podokno](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

    Vzhledem k tomu, že se jedná o první experiment s automatizovaným Machine Learning, uvidíte **úvodní obrazovku automaticky Machine Learning** . 

1.  Vyberte **vytvořit experiment**. Pak jako název experimentu zadejte **My-1st-automl-experiment** .

1. Vyberte **vytvořit nový výpočetní** výkon a nakonfigurujte pro tento experiment výpočetní kontext.

    Pole| Value
    ---|---
    Název výpočetních prostředků služby Machine Learning| Zadejte jedinečný název, který identifikuje váš výpočetní kontext. V tomto příkladu používáme **automl-COMPUTE**.
    Velikost virtuálního počítače| Vyberte velikost virtuálního počítače pro výpočetní výkon. Používáme **Standard_DS12_V2**.
    Další nastavení| *Minimální uzel*: 1. Chcete-li povolit profilaci dat, je nutné mít nejméně jeden uzel. <br> *Maximální počet uzlů*: 6. 

    Pokud chcete začít vytvářet nové výpočetní prostředky, vyberte **vytvořit**. Toto může chvíli trvat. 

    Až se vytváření dokončí, vyberte v rozevíracím seznamu nový výpočet a pak vyberte **Další**.

1. V tomto kurzu použijeme výchozí účet úložiště a kontejner vytvořený s vaším novým výpočetním prostředím. Tím se automaticky naplní ve formuláři.

1. Vyberte **nahrát** a zvolte soubor **bankmarketing_train. csv** z místního počítače a nahrajte ho do výchozího kontejneru. Verze Public Preview podporuje jenom místní nahrávání souborů a účty Azure Blob Storage. Po dokončení nahrávání vyberte soubor ze seznamu. 

    [![Vybrat datový soubor](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. Karta **Náhled** nám umožňuje dále konfigurovat naše data pro tento experiment.

    Na kartě náhled označte, že data obsahují záhlaví. Služba ve výchozím nastavení zahrnuje všechny funkce (sloupce) pro školení. V tomto příkladu se posuňte doprava a **ignorujte** funkci **day_of_week** .

    ![Konfigurace karty Preview](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)


    >[!NOTE]
    > Profilování dat není k dispozici u výpočtů, které mají minimálně 0 uzlů.

1. Jako úkol předpovědi vyberte **klasifikace** .

1. Jako cílový sloupec vyberte **y** , ve kterém se má předpovědi sloupec. Tento sloupec indikuje, jestli se klient přihlásil k předplatnému termínu uložení nebo ne.

1. Rozbalte **Upřesnit nastavení** a vyplňte pole následujícím způsobem.

    Pokročilá nastavení|Hodnota
    ------|------
    Primární metrika| AUC_weighted 
    Výstupní kritéria| Pokud je splněno kterékoli z těchto kritérií, úloha školení skončí před úplným dokončením. <br> *Doba úlohy školení (minuty)* : 5  <br> *Maximální počet iterací*: 10 
    Předzpracování| Povolit předzpracování pomocí automatizovaného strojového učení. To zahrnuje automatické čištění dat, přípravu a transformaci, které generují syntetické funkce.
    Ověřování| Vyberte K skládání křížové ověření a 2 pro počet křížových validací. 
    Souběžnost| U maximálního počtu souběžných iterací vyberte 5.

   >[!NOTE]
   > V tomto experimentu nenastavíme prahovou hodnotu metriky nebo maximálního počtu iterací a neblokuje testování algoritmů.

1. Kliknutím na **Spustit** spustíte experiment.

   Po zahájení experimentu se v horní části zobrazí prázdná obrazovka s podrobnostmi o **spuštění** s následujícím stavem. Proces přípravy procesu trvá několik minut. Po dokončení přípravného procesu se stavová zpráva změní na **spuštěno.**

      ![Spustit přípravu](media/tutorial-1st-experiment-automated-ml/run-preparing.png)

##  <a name="view-experiment-details"></a>Zobrazit podrobnosti experimentu

Jak experiment probíhá, obrazovka s podrobnostmi o **spuštění** aktualizuje graf iterace a seznam pomocí různých iterací (modelů), které jsou spuštěny. Seznam iterací je v pořadí podle skóre metriky a ve výchozím nastavení model, který vychází z nejvyšší úrovně podle naší **AUC_weighted** metriky, je v horní části seznamu.

>[!TIP]
> Školicí úlohy můžou trvat několik minut, než se dokončí spuštění každého kanálu.

[![Spustit řídicí panel podrobností](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-model"></a>Nasazení modelu

Pro tento experiment se **VotingEnsemble** považuje za nejlepší model založený na metrikě **AUC_weighted** . Díky automatizovanému strojového učení v Azure Portal můžeme tento model nasadit jako webovou službu a pomocí jediného kliknutí předpovídat nová data. 

1. Na stránce s podrobnostmi o **spuštění** vyberte tlačítko **nasadit nejlepší model** .

1. Nasaďte **nejvhodnější podokno modelu nasazení** následujícím způsobem:

    Pole| Value
    ----|----
    Název nasazení| Moje automl – nasazení
    Popis nasazení| Moje první automatizované nasazení experimentu Machine Learning
    Skript bodování| AutoGenerate
    Skript prostředí| AutoGenerate
    
1. Vyberte **Nasadit**. Dokončení nasazení může trvat přibližně 20 minut.

    Po úspěšném dokončení nasazení se zobrazí následující zpráva.

    ![Nasazení dokončeno](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    A to je vše! Máte provozní webovou službu, která generuje předpovědi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Soubory nasazení jsou větší než data a experimenty, takže se budou lépe ukládat. Odstraňte jenom soubory nasazení, abyste minimalizovali náklady na váš účet, a pokud chcete zachovat pracovní prostor a soubory experimentů. Jinak odstraňte celou skupinu prostředků, pokud neplánujete použít žádný ze souborů.  

### <a name="delete-deployment-instance"></a>Odstranit instanci nasazení

Pokud chcete zachovat skupinu prostředků a pracovní prostor pro další kurzy a průzkum, odstraňte jenom instanci nasazení z Azure Portal. 

1. Přejděte do podokna **assets (prostředky** ) na levé straně a vyberte **nasazení**. 

1. Vyberte nasazení, které chcete odstranit, a vyberte **Odstranit**. 

1. Vyberte **pokračovat**.

### <a name="delete-resource-group"></a>Odstranit skupinu prostředků

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu automatizovaného strojového učení jste použili Azure Portal k vytvoření a nasazení klasifikačního modelu. Další informace a další kroky najdete v těchto článcích.

+ Jak [využívat webovou službu](how-to-consume-web-service.md).
+ Přečtěte si [](how-to-create-portal-experiments.md#preprocess)Další informace o předzpracování.
+ Přečtěte si další informace o [profilování dat](how-to-create-portal-experiments.md#profile).
+ Přečtěte si další informace o [automatizovaném strojovém učení](concept-automated-ml.md).

>[!NOTE]
> Tato datová sada bank je dostupná v rámci [Creative navýšení (CCO: Veřejná doména)](https://creativecommons.org/publicdomain/zero/1.0/). Všechna práva k individuálnímu obsahu databáze jsou licencovaná v rámci [licence na obsah databáze](https://creativecommons.org/publicdomain/zero/1.0/) a jsou dostupná na [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Tato datová sada je původně dostupná v rámci [databáze UCI Machine Learning](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
>  Citujte prosím následující práci: <br> [Moro et al., 2014] S. Moro, P. Cortez a P. Rita. Přístup na základě dat, který vám umožní předpovědět úspěšnost bankového uvádění na trh. Systémy podpory pro rozhodování, Elsevier, 62:22-31, červen 2014.
