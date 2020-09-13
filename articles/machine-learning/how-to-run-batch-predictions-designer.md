---
title: Spouštění dávkových predikcí s využitím návrháře služby Azure Machine Learning (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se naučit model a nastavit kanál předpovědi dávky pomocí návrháře. Nasaďte kanál jako parametrizovanou webovou službu, která se dá aktivovat z libovolné knihovny HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: e4ca191e50f4ca8aa5a11a36ec44e737cc2ef6bc
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661492"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer-preview"></a>Spouštění dávkových predikcí s využitím návrháře služby Azure Machine Learning (Preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

V tomto článku se naučíte, jak pomocí návrháře vytvořit kanál předpovědi pro dávku. Předpověď dávky umožňuje průběžné hodnocení velkých datových sad na vyžádání pomocí webové služby, kterou lze aktivovat z libovolné knihovny HTTP.

V tomto postupu se naučíte, jak provádět následující úlohy:

> [!div class="checklist"]
> * Vytvoření a publikování kanálu odvození dávky
> * Využití koncového bodu kanálu
> * Správa verzí koncových bodů

Informace o tom, jak pomocí sady SDK nastavit služby vyhodnocování služby Batch, najdete v doprovodném [postupu](how-to-run-batch-predictions.md).

## <a name="prerequisites"></a>Požadavky

Tento postup předpokládá, že už máte školicí kanál. Úvod do návrháře s průvodcem najdete v [části první kurz návrháře](tutorial-designer-automobile-price-train-score.md). 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-batch-inference-pipeline"></a>Vytvoření kanálu odvození dávky

Aby bylo možné vytvořit kanál Inferencing, musí být váš školicí kanál aspoň jednou spuštěný.

1. V pracovním prostoru otevřete kartu **Návrháře** .

1. Vyberte kanál školení, který bude vlakem modelu, který chcete použít k předpovědi.

1. **Odešlete** kanál.

    ![Odeslat kanál](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

Teď, když je kanál školení spuštěný, můžete vytvořit kanál odvození dávky.

1. Vedle **Odeslat**, vyberte Nový rozevírací seznam **vytvořit odvození kanálu**.

1. Vyberte **kanál odvození dávky**.

    ![Vytvořit kanál odvození dávky](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
Výsledkem je výchozí kanál odvození dávky. 

### <a name="add-a-pipeline-parameter"></a>Přidat parametr kanálu

Pokud chcete vytvořit předpovědi pro nová data, můžete buď ručně připojit jinou datovou sadu v tomto zobrazení konceptu kanálu, nebo vytvořit parametr pro datovou sadu. Parametry umožňují změnit chování procesu Batch Inferencing za běhu.

V této části vytvoříte parametr DataSet, který určí jinou datovou sadu pro vytvoření předpovědi.

1. Vyberte modul DataSet.

1. Zobrazí se podokno napravo od plátna. V dolní části podokna vyberte **nastavit jako parametr kanálu**.
   
    Zadejte název parametru nebo přijměte výchozí hodnotu.

    > [!div class="mx-imgBorder"]
    > ![Nastavit datovou sadu jako parametr kanálu](./media/how-to-run-batch-predictions-designer/set-dataset-as-pipeline-parameter.png)

## <a name="publish-your-batch-inference-pipeline"></a>Publikování kanálu odvození dávky

Teď jste připraveni nasadit kanál odvození. Tím se kanál nasadí a zpřístupní se ostatním uživatelům, kteří ho budou používat.

1. Vyberte tlačítko **Publikovat**.

1. V dialogovém okně, které se zobrazí, rozbalte rozevírací seznam pro **PipelineEndpoint**a vyberte **Nový PipelineEndpoint**.

1. Zadejte název koncového bodu a volitelný popis.

    V dolní části dialogového okna uvidíte parametr, který jste nakonfigurovali, výchozí hodnotu ID datové sady použité během školení.

1. Vyberte **Publikovat**.

![Publikování kanálu](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>Využití koncového bodu

Nyní máte publikovaný kanál s parametrem DataSet. Kanál bude používat školicí model vytvořený v kanálu školení k určení skóre datové sady, kterou poskytnete jako parametr.

### <a name="submit-a-pipeline-run"></a>Odeslání spuštění kanálu 

V této části nastavíte ruční spuštění kanálu a upravíte parametr kanálu tak, aby vyhodnocena nová data. 

1. Po dokončení nasazení přejdete do oddílu **Endpoints** .

1. Vyberte **koncové body kanálu**.

1. Vyberte název koncového bodu, který jste vytvořili.

![Odkaz na koncový bod](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. Vyberte **publikované kanály**.

    Tato obrazovka zobrazuje všechny publikované kanály publikované v rámci tohoto koncového bodu.

1. Vyberte kanál, který jste publikovali.

    Stránka Podrobnosti o kanálu vám ukáže detailní historii spuštění a informace o připojovacím řetězci pro váš kanál. 
    
1. Vyberte **Odeslat** pro vytvoření ručního spuštění kanálu.

    ![Podrobnosti kanálu](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. Změňte parametr tak, aby používal jinou datovou sadu.
    
1. Vyberte **Odeslat** a spusťte kanál.

### <a name="use-the-rest-endpoint"></a>Použití koncového bodu REST

Informace o využívání koncových bodů kanálu a publikovaného kanálu najdete v části **koncové body** .

Koncový bod REST koncového bodu kanálu můžete najít na panelu přehled spuštění. Voláním koncového bodu spotřebováváte jeho výchozí publikovaný kanál.

Publikovaný kanál můžete také využít na stránce **publikované kanály** . Vyberte publikovaný kanál a na panelu **přehledu publikovaných kanálů** napravo od grafu můžete najít koncový bod REST. 

Chcete-li provést volání REST, budete potřebovat hlavičku ověřování typu Bearer (OAuth 2,0). Další informace o nastavení ověřování pro váš pracovní prostor a o tom, jak se provádí parametrizované volání REST, najdete v následující [části kurzu](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) .

## <a name="versioning-endpoints"></a>Koncové body správy verzí

Návrhář přiřadí verzi každému dalšímu kanálu, který publikujete do koncového bodu. Můžete zadat verzi kanálu, kterou chcete ve volání REST spustit jako parametr. Pokud nezadáte číslo verze, bude Návrhář používat výchozí kanál.

Při publikování kanálu se můžete rozhodnout, že se pro tento koncový bod vytvoří nový výchozí kanál.

![Nastavení výchozího kanálu](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

Na kartě **publikované kanály** vašeho koncového bodu můžete také nastavit nový výchozí kanál.

![Nastavení výchozího kanálu na stránce publikovaného kanálu](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>Další kroky

Postupujte podle [kurzu](tutorial-designer-automobile-price-train-score.md) návrháře a Projděte a nasaďte regresní model.
''