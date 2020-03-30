---
title: Spouštění dávkových predikcí s využitím návrháře služby Azure Machine Learning (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se, jak trénovat model a nastavit kanál predikce dávky pomocí návrháře. Nasaďte kanál jako parametrizovanou webovou službu, kterou lze aktivovat z libovolné knihovny HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 02/24/2020
ms.custom: Ignite2019
ms.openlocfilehash: 01d69bffcf2c17abceba8ba2e0893360bead8b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477217"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer-preview"></a>Spouštění dávkových predikcí s využitím návrháře služby Azure Machine Learning (Preview)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

V tomto článku se dozvíte, jak pomocí návrháře vytvořit kanál predikce dávky. Predikce dávek umožňuje průběžně skóre velké datové sady na vyžádání pomocí webové služby, která může být spuštěna z libovolné knihovny HTTP.

V tomto návodu se naučíte dělat následující úkoly:

> [!div class="checklist"]
> * Vytvoření a publikování kanálu odvození dávky
> * Využití koncového bodu kanálu
> * Správa verzí koncového bodu

Informace o tom, jak nastavit služby dávkového vyhošťování pomocí sady SDK, naleznete v doprovodných [postupech](how-to-run-batch-predictions.md).

## <a name="prerequisites"></a>Požadavky

Tento návod předpokládá, že již máte kanál školení. Pro průvodce úvod do návrháře, kompletní [část první designer tutorial](tutorial-designer-automobile-price-train-score.md). 

## <a name="create-a-batch-inference-pipeline"></a>Vytvoření kanálu odvození dávky

Váš kanál školení musí být spuštěn alespoň jednou, aby bylo možné vytvořit kanál odvození.

1. Přejděte na kartu **Návrhář** ve svém pracovním prostoru.

1. Vyberte trénovací kanál, který trénuje model, který chcete použít k predikci.

1. **Odešlete** potrubí.

    ![Odeslat potrubí](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

Teď, když byl spuštěn kanál školení, můžete vytvořit kanál odvození dávky.

1. Vedle **možnosti Odeslat**vyberte nový rozevírací **kanál Vytvoření odvození**.

1. Vyberte **kanál odvození dávky**.

    ![Vytvoření kanálu odvození dávky](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
Výsledkem je výchozí kanál odvození dávky. 

### <a name="add-a-pipeline-parameter"></a>Přidání parametru kanálu

Chcete-li vytvořit předpovědi na nová data, můžete buď ručně připojit jinou datovou sadu v tomto zobrazení konceptu kanálu nebo vytvořit parametr pro datovou sadu. Parametry umožňují změnit chování procesu odvození dávky za běhu.

V této části vytvoříte parametr datové sady pro určení jiné datové sady, na které mají být předpovědi.

1. Vyberte modul datové sady.

1. Vpravo od plátna se zobrazí podokno. V dolní části podokna vyberte **Nastavit jako parametr kanálu**.
   
    Zadejte název parametru nebo přijměte výchozí hodnotu.

## <a name="publish-your-batch-inferencing-pipeline"></a>Publikování kanálu dávkových odvození

Teď jste připraveni nasadit kanál odvození. To bude nasadit kanálu a zpřístupnit pro ostatní k použití.

1. Vyberte tlačítko **Publikovat**.

1. V zobrazeném dialogovém okně rozbalte rozevírací seznam **pro PipelineEndpoint**a vyberte **Nový pipelineendpoint**.

1. Zadejte název koncového bodu a volitelný popis.

    V dolní části dialogového okna uvidíte nakonfigurovaný parametr s výchozí hodnotou ID datové sady použité během trénování.

1. Vyberte **Publikovat**.

![Publikování kanálu](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>Využití koncového bodu

Nyní máte publikovaný kanál s parametrem datové sady. Kanál použije trénovaný model vytvořený v kanálu školení ke skóre datové sady, kterou zadáte jako parametr.

### <a name="submit-a-pipeline-run"></a>Odeslání spuštění kanálu 

V této části nastavíte ruční spuštění kanálu a změníte parametr kanálu tak, aby získával nová data. 

1. Po dokončení nasazení přejděte do části **Koncové body.**

1. Vyberte **koncové body kanálu**.

1. Vyberte název vytvořeného koncového bodu.

![Propojení koncového bodu](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. Vyberte **Publikované kanály**.

    Tato obrazovka zobrazuje všechny publikované kanály publikované pod tímto koncovým bodem.

1. Vyberte kanál, který jste publikovali.

    Stránka s podrobnostmi o kanálu zobrazuje podrobnou historii spuštění a informace o připojovacím řetězci pro váš kanál. 
    
1. Výběrem **možnosti Odeslat** vytvořte ruční spuštění kanálu.

    ![Podrobnosti o kanálu](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. Změňte parametr tak, aby používal jinou datovou sadu.
    
1. Chcete-li kanál **spustit,** vyberte odeslat.

### <a name="use-the-rest-endpoint"></a>Použití koncového bodu REST

Informace o tom, jak využívat koncové body kanálu a publikovaný kanál, najdete v části **Koncové body.**

Koncový bod REST koncového bodu kanálu najdete v panelu přehled spuštění. Voláním koncového bodu spotřebováváte jeho výchozí publikovaný kanál.

Publikovaný kanál můžete také spotřebovat na stránce **Publikované kanály.** Vyberte publikovaný kanál a vyhledejte jeho koncový bod REST. 

![Podrobnosti o koncovém bodu spočine](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

Chcete-li provést volání REST, budete potřebovat hlavičku ověřování typu nosiče OAuth 2.0. Další podrobnosti o nastavení ověřování pracovního prostoru a vytvoření parametrizovaného volání REST najdete v následující [části.](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)

## <a name="versioning-endpoints"></a>Koncové body správy verzí

Návrhář přiřadí verzi každému dalšímu kanálu, který publikujete do koncového bodu. Můžete zadat verzi kanálu, který chcete spustit jako parametr ve volání REST. Pokud nezadáte číslo verze, návrhář použije výchozí kanál.

Při publikování kanálu, můžete zvolit, aby se nový výchozí kanál pro tento koncový bod.

![Nastavení výchozího kanálu](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

Můžete také nastavit nový výchozí kanál na kartě **Publikované kanály** koncového bodu.

![Nastavení výchozího kanálu](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>Další kroky

Postupujte podle [kurzu](tutorial-designer-automobile-price-train-score.md) návrháře trénovat a nasazovat regresní model.
''