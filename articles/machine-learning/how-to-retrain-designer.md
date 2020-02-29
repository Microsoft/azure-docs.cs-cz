---
title: Přeučení modelů pomocí návrháře Azure Machine Learning (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se přeškolovat modely pomocí publikovaných kanálů v Návrháři Azure Machine Learning (Preview).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 02/24/2020
ms.openlocfilehash: b69cd4c8459ffae363f02247b1f20488afe1cd9b
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921588"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Přeučení modelů pomocí návrháře Azure Machine Learning (Preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

V tomto postupu se naučíte, jak pomocí návrháře Azure Machine Learning znovu naučit model strojového učení. Zjistěte, jak používat publikované kanály k automatizaci pracovních postupů strojového učení pro rekurzi.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Naučte se model strojového učení.
> * Vytvořte parametr kanálu.
> * Publikujte svůj školicí kanál.
> * Přeškolujte svůj model.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree).

* Pracovní prostor Azure Machine Learning s SKU Enterprise.

Tento postup předpokládá, že máte základní znalosti o sestavování kanálů v návrháři. Úvodní Úvod k Návrháři dokončíte pomocí tohoto [kurzu](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Vzorový kanál

Kanál použitý v tomto článku je změněná verze, kterou najdete v [ukázce 3 – předpověď příjmu](how-to-designer-sample-classification-predict-income.md). Místo ukázkové datové sady používá modul [Import dat](algorithm-module-reference/import-data.md) , který vám ukáže, jak vytvořit model pomocí vlastních dat.

![Snímek obrazovky se změněným ukázkovým kanálem s polem, které zvýrazní modul importu dat](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Výuka modelu strojového učení

Pro přeučení modelu budete potřebovat počáteční model. V této části se dozvíte, jak vytvořit model a získat přístup k uloženému modelu pomocí návrháře.

1. Vyberte modul **Import dat** .
1. V podokně Vlastnosti zadejte zdroj dat.

    ![Snímek obrazovky znázorňující ukázkovou konfiguraci modulu import dat](./media/how-to-retrain-designer/import-data-settings.png)

    V tomto příkladu jsou data uložená v [úložišti Azure DataStore](how-to-access-data.md). Pokud ještě nemáte úložiště dat, můžete si ho vytvořit teď tak, že vyberete **nové úložiště dat**.

1. Zadejte cestu k datům. Můžete také vybrat **Procházet cesta** pro vizuální navigaci v úložišti dat. 

1. V horní části plátna vyberte **Spustit** .
    
    > [!NOTE]
    > Pokud jste pro tuto koncept kanálu už nastavili výchozí výpočet, kanál se spustí automaticky. Jinak můžete postupovat podle výzev v podokně nastavení, které se zobrazí jako první.

### <a name="locate-your-trained-model"></a>Vyhledejte svůj vycvičený model

Návrhář uloží do výchozího účtu úložiště všechny výstupy kanálů, včetně školených modelů. K vyškolený model můžete také přistupovat přímo v Návrháři:

1. Počkejte na dokončení běhu kanálu.

1. Vyberte modul **vlakového modelu** .

1. V podokně nastavení vyberte **výstupy + protokoly**.

1. Klikněte na ikonu **Zobrazit výstup** a postupujte podle pokynů v místním okně a vyhledejte trained model.

![Snímek obrazovky, který ukazuje, jak stáhnout vycvičený model](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>Vytvoření parametru kanálu

Přidejte parametry kanálu pro dynamické nastavení proměnných za běhu. Pro tento kanál přidejte parametr pro cestu školicích dat, abyste mohli svůj model přeškolit na novou datovou sadu.

1. Vyberte modul **Import dat** .
1. V podokně nastavení vyberte tři tečky nad polem **cesta** .
1. Vyberte možnost **Přidat do parametru kanálu**.
1. Zadejte název parametru a výchozí hodnotu.

    > [!NOTE]
    > Parametry kanálu můžete zkontrolovat a upravit výběrem **ikony nastavení ozubeného kola** vedle názvu konceptu kanálu. 

![Snímek obrazovky ukazující, jak vytvořit parametr kanálu](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Publikování školicího kanálu

Při publikování kanálu se vytvoří koncový bod kanálu. Koncové body kanálu umožňují opakovaně používat a spravovat kanály pro opakovatelnost a automatizaci. V tomto příkladu jste svůj kanál nastavili pro rekurzi.

1. Na plátně návrháře vyberte **publikovat** .
1. Vyberte nebo vytvořte nový koncový bod kanálu.

    > [!NOTE]
    > Můžete publikovat více kanálů do jednoho koncového bodu. Každému kanálu v koncovém bodu je přiděleno číslo verze, které můžete zadat při volání koncového bodu kanálu.

1. Vyberte **Publikovat**.

## <a name="retrain-your-model"></a>Přeškolování modelu

Teď, když máte publikovaný školicí kanál, můžete ho použít k revýuce modelu pomocí nových dat. Spuštění můžete odeslat z koncového bodu kanálu z portálu nebo programově.

### <a name="submit-runs-with-the-designer"></a>Odeslat běh pomocí návrháře

Pomocí následujících kroků můžete odeslat koncový bod kanálu z návrháře:

1. Přejít na stránku **koncové body** .

1. Vyberte kartu **koncové body kanálu** .

1. Vyberte koncový bod kanálu.

1. Vyberte kartu **publikované kanály** .

1. Vyberte kanál, který chcete spustit.

1. Vyberte **Run** (Spustit).

1. V dialogovém okně nastavení můžete zadat novou hodnotu cesty vstupních dat, která odkazuje na novou datovou sadu.

![Snímek obrazovky ukazující, jak nastavit spuštění s parametrizovaným kanálem v Návrháři](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-with-code"></a>Odeslat běh s kódem

Existuje několik způsobů, jak získat přístup ke koncovému bodu REST programově v závislosti na vašem vývojovém prostředí. Ukázky kódu, které ukazují, jak odeslat spuštění kanálu s parametry na kartě **využití** vašeho kanálu, najdete v tématu.

## <a name="next-steps"></a>Další kroky

Postupujte podle [kurzu](tutorial-designer-automobile-price-train-score.md) návrháře a Projděte a nasaďte regresní model.
