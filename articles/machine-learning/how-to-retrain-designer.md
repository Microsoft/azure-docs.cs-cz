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
ms.openlocfilehash: 264b169eefde18880f50feae2554aa3ca7037b1f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368158"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Přeučení modelů pomocí návrháře Azure Machine Learning (Preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

V tomto článku se dozvíte, jak pomocí návrháře Azure Machine Learning znovu naučit model strojového učení. Zjistěte, jak používat publikované kanály k automatizaci pracovních postupů strojového učení pro rekurzi.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Naučte se model strojového učení.
> * Vytvořte parametr kanálu.
> * Publikujte svůj školicí kanál.
> * Přeškolujte svůj model.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree).
* Pracovní prostor Azure Machine Learning s SKU Enterprise.

V tomto článku se předpokládá, že máte základní znalosti o sestavování kanálů v návrháři. Úvodní Úvod k Návrháři dokončíte pomocí tohoto [kurzu](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Vzorový kanál

Kanál použitý v tomto článku je změněná verze, kterou najdete v [ukázce 3: předpověď příjmů](how-to-designer-sample-classification-predict-income.md). Místo ukázkové datové sady používá modul [Import dat](algorithm-module-reference/import-data.md) , který vám ukáže, jak pomocí vašich vlastních dat proškolit model.

![Snímek obrazovky zobrazující upravený vzorový kanál s polem, které zvýrazní modul importu dat](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Výuka modelu strojového učení

Pro přeučení modelu budete potřebovat počáteční model. V této části se dozvíte, jak naučit model a přistupovat k uloženému modelu pomocí návrháře.

1. Vyberte modul **Import dat** .
1. V podokně Vlastnosti zadejte zdroj dat.

   ![Snímek obrazovky, který zobrazuje ukázkovou konfiguraci modulu import dat](./media/how-to-retrain-designer/import-data-settings.png)

   V tomto příkladu jsou data uložená v [úložišti Azure DataStore](how-to-access-data.md). Pokud ještě nemáte úložiště dat, můžete si ho vytvořit teď tak, že vyberete **nové úložiště dat**.

1. Zadejte cestu k datům. Můžete také vybrat **Procházet cestu** a přejít tak do úložiště dat. 
1. V horní části plátna vyberte **Spustit** .
    
   > [!NOTE]
   > Pokud jste pro tuto koncept kanálu už nastavili výchozí výpočet, kanál se spustí automaticky. V opačném případě můžete podle pokynů v podokně nastavení sadu nyní nastavit.

### <a name="find-your-trained-model"></a>Najděte si vyškolený model

Návrhář uloží do výchozího účtu úložiště všechny výstupy kanálů, včetně školených modelů. K vyškolený model můžete také přistupovat přímo v Návrháři:

1. Počkejte na dokončení běhu kanálu.
1. Vyberte modul **vlakového modelu** .
1. V podokně nastavení vyberte **výstupy + protokoly**.
1. Vyberte ikonu **Zobrazit výstup** a postupujte podle instrukcí v místním okně a vyhledejte trained model.

![Snímek obrazovky, který ukazuje stažení naučeného modelu](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>Vytvoření parametru kanálu

Přidejte parametry kanálu pro dynamické nastavení proměnných za běhu. Pro tento kanál přidejte parametr pro cestu školicích dat, abyste mohli svůj model přeškolit na novou datovou sadu.

1. Vyberte modul **Import dat** .
1. V podokně nastavení vyberte tři tečky nad polem **cesta** .
1. Vyberte možnost **Přidat do parametru kanálu**.
1. Zadejte název parametru a výchozí hodnotu.

   > [!NOTE]
   > Parametry kanálu můžete zkontrolovat a upravit výběrem ikony **Nastavení** ozubeného kola vedle názvu konceptu kanálu. 

![Snímek obrazovky, který ukazuje, jak vytvořit parametr kanálu](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Publikování školicího kanálu

Při publikování kanálu se vytvoří koncový bod kanálu. Koncové body kanálu umožňují opakovaně používat a spravovat kanály pro opakovatelnost a automatizaci. V tomto příkladu jste nastavili svůj kanál pro přeškolení.

1. Na plátně návrháře vyberte **publikovat** .
1. Vyberte nebo vytvořte koncový bod kanálu.

   > [!NOTE]
   > Můžete publikovat více kanálů do jednoho koncového bodu. Každému kanálu v koncovém bodu je přiděleno číslo verze, které můžete zadat při volání koncového bodu kanálu.

1. Vyberte **Publikovat**.

## <a name="retrain-your-model"></a>Přeškolování modelu

Teď, když máte publikovaný školicí kanál, můžete ho použít k revýuce modelu pomocí nových dat. Spuštění můžete odeslat z koncového bodu kanálu z Azure Portal nebo je odeslat programově.

### <a name="submit-runs-by-using-the-designer"></a>Odeslat běh pomocí návrháře

Pomocí následujících kroků můžete odeslat koncový bod kanálu z návrháře:

1. Přejít na stránku **koncové body** .
1. Vyberte kartu **koncové body kanálu** .
1. Vyberte koncový bod kanálu.
1. Vyberte kartu **publikované kanály** .
1. Vyberte kanál, který chcete spustit.
1. Vyberte **Odeslat**.
1. V dialogovém okně nastavení můžete zadat novou hodnotu pro hodnotu cesty vstupních dat. Tato hodnota odkazuje na novou datovou sadu.

![Snímek obrazovky, který ukazuje, jak nastavit spuštění parametrizovaného kanálu v Návrháři](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Odeslat běh pomocí kódu

Koncový bod REST publikovaného kanálu můžete najít na panelu přehled. Zavoláním koncového bodu můžete publikovaný kanál přesměrovat.

Chcete-li provést volání REST, budete potřebovat hlavičku ověřování typu Bearer (OAuth 2,0). Informace o nastavení ověřování pro váš pracovní prostor a o tom, jak provést parametrizované volání REST, najdete v tématu [vytvoření kanálu Azure Machine Learning pro dávkové vyhodnocování](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Další kroky

Postupujte podle [kurzu návrháře](tutorial-designer-automobile-price-train-score.md) a Projděte a nasaďte regresní model.
