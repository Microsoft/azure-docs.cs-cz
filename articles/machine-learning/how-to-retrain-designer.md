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
ms.date: 04/06/2020
ms.openlocfilehash: 721e5414fc4753cd5d58a17fc7ed51ea99868778
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810346"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Přeučování modelů s využitím návrháře služby Azure Machine Learning (Preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

V tomto článku s návody se dozvíte, jak pomocí návrháře Azure Machine Learning použít k přeškolení modelu strojového učení. Publikované kanály použijete k automatizaci pracovního postupu a nastavení parametrů pro trénování modelu na nových datech. 

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Trénování modelu strojového učení.
> * Vytvořte parametr kanálu.
> * Publikujte svůj tréninkový kanál.
> * Přeškolte model novými parametry.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor Azure Machine Learning s podnikovou skladovou položkou.
* Datová sada přístupná návrháři. To může být jeden z následujících:
   * Registrovaná datová sada Azure Machine Learning
    
     **-nebo-**
   * Datový soubor uložený v úložišti dat Azure Machine Learning.
   
Informace o přístupu k datům pomocí návrháře naleznete v tématu [Jak importovat data do návrháře](how-to-designer-import-data.md).

Tento článek také předpokládá, že máte základní znalosti o vytváření kanálů v návrháři. Pro průvodce úvod, dokončení [kurzu](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Ukázkový kanál

Kanál použitý v tomto článku je změněná verze [Sample 3: Předpověď příjmů](samples-designer.md#classification-samples). Kanál používá modul [Import dat](algorithm-module-reference/import-data.md) namísto ukázkové datové sady, aby vám ukázal, jak trénovat modely pomocí vlastních dat.

![Snímek obrazovky s upraveným ukázkovým kanálem s rámečkem zvýrazňujícím modul Import dat](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Vytvoření parametru kanálu

Vytvořte parametry kanálu pro dynamické nastavení proměnných za běhu. V tomto příkladu změníte cestu trénovací data z pevné hodnoty na parametr, takže můžete přeškolit model na různá data.

1. Vyberte modul **Importovat data.**

    > [!NOTE]
    > Tento příklad používá import dat modul pro přístup k datům v registrovanéúložiště dat. Můžete však postupovat podle podobných kroků, pokud používáte alternativní vzory přístupu k datům.

1. V podokně podrobností modulu vyberte vpravo od plátna zdroj dat.

1. Zadejte cestu k datům. Můžete také vybrat **procházet cestu** procházet strom souborů. 

1. Najeďte myší na pole **Cesta** a vyberte tři tečky nad polem **Cesta,** které se zobrazí.

    ![Snímek obrazovky, který ukazuje, jak vytvořit parametr kanálu](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Vyberte **Přidat do parametru kanálu**.

1. Zadejte název parametru a výchozí hodnotu.

   > [!NOTE]
   > Parametry kanálu můžete zkontrolovat a upravit tak, že vedle názvu pracovní verze kanálu vyberete ikonu Ozubená kola **Nastavení.** 

1. Vyberte **Uložit**.

1. Odešlete spuštění kanálu.

## <a name="find-a-trained-model"></a>Vyhledání trénovaného modelu

Návrhář uloží veškerý výstup kanálu, včetně trénovaných modelů, do výchozího účtu úložiště pracovního prostoru. K trénovaným modelům můžete také přistupovat přímo v návrháři:

1. Počkejte na dokončení spuštění kanálu.
1. Vyberte modul **Model vlaku.**
1. V podokně podrobností modulu vpravo od plátna vyberte **Výstupy + protokoly**.
1. Model najdete v **jiných výstupech** spolu s protokoly spuštění.
1. Případně vyberte ikonu **Zobrazit výstup.** Zde můžete podle pokynů v dialogovém okně přejít přímo do úložiště dat. 

![Snímek obrazovky, který ukazuje, jak stáhnout trénovaný model](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>Publikování kanálu školení

Publikujte kanál do koncového bodu kanálu, abyste je v budoucnu snadno znovu použili. Koncový bod kanálu vytvoří koncový bod REST k vyvolání kanálu v budoucnu. V tomto příkladu koncový bod kanálu umožňuje znovu použít kanál k přetrénování modelu na různých datech.

1. Nad plátnem návrháře vyberte **Publikovat.**
1. Vyberte nebo vytvořte koncový bod kanálu.

   > [!NOTE]
   > Do jednoho koncového bodu můžete publikovat více kanálů. Každý kanál v daném koncovém bodě je uveden číslo verze, které můžete zadat při volání koncového bodu kanálu.

1. Vyberte **Publikovat**.

## <a name="retrain-your-model"></a>Přeškolit model

Teď, když máte publikovaný kanál školení, můžete jej použít k přetrénování modelu na nová data. Spuštění můžete odeslat z koncového bodu kanálu z pracovního prostoru studia nebo programově.

### <a name="submit-runs-by-using-the-designer"></a>Odeslat spuštění pomocí návrháře

Pomocí následujících kroků odešlete parametrizované koncového bodu kanálu z návrháře:

1. Přejděte na stránku **Koncové body** v pracovním prostoru studia.
1. Vyberte kartu **Koncové body kanálu.** Potom vyberte koncový bod kanálu.
1. Vyberte kartu **Publikované kanály.** Potom vyberte verzi kanálu, kterou chcete spustit.
1. Vyberte **Odeslat**.
1. V dialogovém okně nastavení můžete zadat hodnoty parametrů pro spuštění. V tomto příkladu aktualizujte datovou cestu pro trénování modelu pomocí datové sady mimo USA.

![Snímek obrazovky, který ukazuje, jak nastavit parametrizovaný kanál spustit v návrháři](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Odeslat spuštění pomocí kódu

Koncový bod REST publikovaného kanálu najdete v panelu přehledu. Voláním koncového bodu můžete znovu zařazovat publikovaný kanál.

Chcete-li volat REST, potřebujete hlavičku ověřování typu nosiče OAuth 2.0. Informace o nastavení ověřování v pracovním prostoru a vytvoření parametrizovaného volání REST najdete [v tématu Vytvoření kanálu Azure Machine Learning pro dávkové vyhodnocování](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak vytvořit koncový bod parametrizovaného trénovacího kanálu pomocí návrháře.

Úplný návod, jak můžete nasadit model, abyste vytvořili předpovědi, najdete v [kurzu návrháře,](tutorial-designer-automobile-price-train-score.md) kde můžete trénovat a nasazovat regresní model.
