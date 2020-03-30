---
title: Přeškolit modely pomocí návrháře Azure Machine Learning (preview)
titleSuffix: Azure Machine Learning
description: Zjistěte, jak rekvalifikovat modely s publikovanými kanály v návrháři Azure Machine Learning (preview).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 02/24/2020
ms.openlocfilehash: 264b169eefde18880f50feae2554aa3ca7037b1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368158"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Přeučování modelů s využitím návrháře služby Azure Machine Learning (Preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

V tomto článku s návody se dozvíte, jak pomocí návrháře Azure Machine Learning použít k přeškolení modelu strojového učení. Přečtěte si, jak používat publikované kanály k automatizaci pracovních postupů strojového učení pro rekvalifikaci.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Trénování modelu strojového učení.
> * Vytvořte parametr kanálu.
> * Publikujte svůj tréninkový kanál.
> * Přeškolit model.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree).
* Pracovní prostor Azure Machine Learning s podnikovou skladovou položkou.

Tento článek předpokládá, že máte základní znalosti o vytváření kanálů v návrháři. Pro průvodce úvod do návrháře, dokončení [kurzu](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Ukázkový kanál

Kanál použitý v tomto článku je změněná verze, která je nalezena ve [vzorku 3: Predikce příjmů](how-to-designer-sample-classification-predict-income.md). Používá modul [Import dat](algorithm-module-reference/import-data.md) namísto ukázkové datové sady, aby vám ukázal, jak trénovat model pomocí vlastních dat.

![Snímek obrazovky s upraveným ukázkovým kanálem s rámečkem zvýrazňujícím modul Import dat](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>Trénování modelu strojového učení

Chcete-li přeškolit model, potřebujete počáteční model. V této části se dozvíte, jak trénovat model a přístup k uloženému modelu pomocí návrháře.

1. Vyberte modul **Importovat data.**
1. V podokně vlastností zadejte zdroj dat.

   ![Snímek obrazovky, který zobrazuje ukázkovou konfiguraci modulu Import dat](./media/how-to-retrain-designer/import-data-settings.png)

   V tomto příkladu jsou data uložena v [úložišti dat Azure](how-to-access-data.md). Pokud ještě nemáte úložiště dat, můžete ho nyní vytvořit výběrem **nového úložiště dat**.

1. Zadejte cestu k datům. Můžete také vybrat **Procházet cestu** k procházení úložiště dat. 
1. V horní části plátna vyberte **Spustit.**
    
   > [!NOTE]
   > Pokud jste již nastavili výchozí výpočetní prostředky pro tuto pracovní verzi kanálu, kanál se spustí automaticky. V opačném případě můžete podle pokynů v podokně nastavení nastavit jeden nyní.

### <a name="find-your-trained-model"></a>Najděte svůj trénovaný model

Návrhář uloží všechny výstupy kanálu, včetně trénovaných modelů, do výchozího účtu úložiště. K trénovaného modelu můžete také přistupovat přímo v návrháři:

1. Počkejte na dokončení spuštění kanálu.
1. Vyberte modul **Model vlaku.**
1. V podokně nastavení vyberte **Výstupy+protokoly**.
1. Vyberte ikonu **Zobrazit výstup** a podle pokynů v rozbalovacím okně vyhledejte trénovaný model.

![Snímek obrazovky, který ukazuje, jak stáhnout trénovaný model](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>Vytvoření parametru kanálu

Přidejte parametry kanálu pro dynamické nastavení proměnných za běhu. Pro tento kanál přidejte parametr pro cestu trénovací data, takže můžete přeškolit model na novou datovou sadu.

1. Vyberte modul **Importovat data.**
1. V podokně nastavení vyberte tři tečky nad polem **Cesta.**
1. Vyberte **Přidat do parametru kanálu**.
1. Zadejte název parametru a výchozí hodnotu.

   > [!NOTE]
   > Parametry kanálu můžete zkontrolovat a upravit tak, že vedle názvu pracovní verze kanálu vyberete ikonu Ozubená kola **Nastavení.** 

![Snímek obrazovky, který ukazuje, jak vytvořit parametr kanálu](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>Publikování kanálu školení

Když publikujete kanál, vytvoří koncový bod kanálu. Koncové body kanálu umožňují znovu použít a spravovat vaše kanály pro opakovatelnost a automatizaci. V tomto příkladu jste nastavili kanál pro rekvalifikaci.

1. Nad plátnem návrháře vyberte **Publikovat.**
1. Vyberte nebo vytvořte koncový bod kanálu.

   > [!NOTE]
   > Do jednoho koncového bodu můžete publikovat více kanálů. Každému kanálu v koncovém bodě je přiděleno číslo verze, které můžete zadat při volání koncového bodu kanálu.

1. Vyberte **Publikovat**.

## <a name="retrain-your-model"></a>Přeškolit model

Teď, když máte publikovaný kanál školení, můžete jej použít k přetrénování modelu pomocí nových dat. Spuštění můžete odeslat z koncového bodu kanálu z portálu Azure nebo je odeslat programově.

### <a name="submit-runs-by-using-the-designer"></a>Odeslat spuštění pomocí návrháře

Pomocí následujících kroků odešlete koncový bod kanálu z návrháře:

1. Přejděte na stránku **Koncové body.**
1. Vyberte kartu **Koncové body kanálu.**
1. Vyberte koncový bod kanálu.
1. Vyberte kartu **Publikované kanály.**
1. Vyberte kanál, který chcete spustit.
1. Vyberte **Odeslat**.
1. V dialogovém okně nastavení můžete zadat novou hodnotu pro hodnotu cesty vstupních dat. Tato hodnota odkazuje na novou datovou sadu.

![Snímek obrazovky, který ukazuje, jak nastavit parametrizovaný kanál spustit v návrháři](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Odeslat spuštění pomocí kódu

Koncový bod REST publikovaného kanálu najdete v panelu přehledu. Voláním koncového bodu můžete znovu zařazovat publikovaný kanál.

Chcete-li volat REST, potřebujete hlavičku ověřování typu nosiče OAuth 2.0. Informace o nastavení ověřování v pracovním prostoru a vytvoření parametrizovaného volání REST najdete [v tématu Vytvoření kanálu Azure Machine Learning pro dávkové vyhodnocování](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Další kroky

Postupujte podle [kurzu návrháře](tutorial-designer-automobile-price-train-score.md) trénovat a nasazovat regresní model.
