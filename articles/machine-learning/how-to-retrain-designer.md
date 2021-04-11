---
title: Použití parametrů kanálu k reučení modelů v Návrháři
titleSuffix: Azure Machine Learning
description: Přeučení modelů s publikovanými kanály a parametry kanálu v Návrháři Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 03/06/2021
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: ed5f59fc1d751f92a7c016aa3586497659f778fd
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962947"
---
# <a name="use-pipeline-parameters-to-retrain-models-in-the-designer"></a>Použití parametrů kanálu k reučení modelů v Návrháři


V tomto článku se dozvíte, jak pomocí nástroje Azure Machine Learning Designer přeškolit model strojového učení pomocí parametrů kanálu. Publikované kanály použijete k automatizaci pracovního postupu a nastavení parametrů, abyste mohli svůj model proškolit na nových datech. Parametry kanálu umožňují znovu použít existující kanály pro různé úlohy.  

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Naučte se model strojového učení.
> * Vytvořte parametr kanálu.
> * Publikujte svůj školicí kanál.
> * Přeškolení modelu o nové parametry.

## <a name="prerequisites"></a>Požadavky

* Pracovní prostor Azure Machine Learning
* Dokončete část 1 této série, [Transformujte data v Návrháři](how-to-designer-transform-data.md) .

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

Tento článek také předpokládá, že máte znalosti o sestavování kanálů v návrháři. Úvod do průvodce najdete v tomto [kurzu](tutorial-designer-automobile-price-train-score.md). 

### <a name="sample-pipeline"></a>Vzorový kanál

Kanál použitý v tomto článku je upravená verze [předpovědi pro příjem](samples-designer.md#classification) kanálu na domovské stránce návrháře. Kanál používá místo ukázkové datové sady modul [Import dat](algorithm-module-reference/import-data.md) , který vám ukáže, jak naučit modely pomocí vlastních dat.

![Snímek obrazovky zobrazující upravený vzorový kanál s polem, které zvýrazní modul importu dat](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>Vytvoření parametru kanálu

Parametry kanálu slouží k vytváření univerzálních kanálů, které je možné znovu odeslat později pomocí různých hodnot parametrů. Některé běžné scénáře aktualizují datové sady nebo některé parametry technologie Hyper-v pro přeškolení. Vytvořte parametry kanálu pro dynamické nastavení proměnných za běhu. 

Parametry kanálu lze přidat do zdrojů dat nebo parametrů modulu v kanálu. Po odeslání kanálu lze zadat hodnoty těchto parametrů.

V tomto příkladu změníte cestu školicích dat z pevné hodnoty na parametr, abyste mohli model přeškolit na různá data. V závislosti na vašem případu použití můžete také přidat další parametry modulu jako parametry kanálu.

1. Vyberte modul **Import dat** .

    > [!NOTE]
    > V tomto příkladu se používá modul importu dat pro přístup k datům v registrovaném úložišti dat. Pokud však používáte alternativní vzory přístupu k datům, můžete postupovat podle podobných kroků.

1. V podokně podrobností modulu napravo od plátna vyberte zdroj dat.

1. Zadejte cestu k datům. Můžete také vybrat **Procházet cesta** pro procházení stromu souborů. 

1. MouseOver pole **cesta** a vyberte tři tečky nad polem **cesta** , která se zobrazí.

1. Vyberte možnost **Přidat do parametru kanálu**.

1. Zadejte název parametru a výchozí hodnotu.

   ![Snímek obrazovky, který ukazuje, jak vytvořit parametr kanálu](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. Vyberte **Uložit**.

   > [!NOTE]
   > Parametr modulu můžete také odpojit od parametru kanálu v podokně podrobností modulu, podobně jako přidání parametrů kanálu.
   >
   > Parametry kanálu můžete zkontrolovat a upravit výběrem ikony **Nastavení** ozubeného kola vedle názvu konceptu kanálu. 
   >    - Po odpojení můžete parametr kanálu odstranit v podokně **upravena vlastním nastavením** .
   >    - V podokně **Nastavení** můžete také přidat parametr kanálu a pak ho použít na některém z parametrů modulu.

1. Odešlete běh kanálu.

## <a name="publish-a-training-pipeline"></a>Publikování školicího kanálu

Publikování kanálu do koncového bodu kanálu, aby bylo možné kanály snadno znovu použít v budoucnu. Koncový bod kanálu vytvoří koncový bod REST pro vyvolání kanálu v budoucnu. V tomto příkladu vám koncový bod kanálu umožňuje znovu použít váš kanál a přeškolit model na různých datech.

1. Na plátně návrháře vyberte **publikovat** .
1. Vyberte nebo vytvořte koncový bod kanálu.

   > [!NOTE]
   > Můžete publikovat více kanálů do jednoho koncového bodu. Každému kanálu v daném koncovém bodu je přiděleno číslo verze, které můžete zadat při volání koncového bodu kanálu.

1. Vyberte **Publikovat**.

## <a name="retrain-your-model"></a>Přeškolování modelu

Teď, když máte publikovaný školicí kanál, můžete ho použít k revýuce modelu na nová data. Spuštění můžete odeslat z koncového bodu kanálu z pracovního prostoru studia nebo programově.

### <a name="submit-runs-by-using-the-studio-portal"></a>Odeslat běh pomocí portálu studia

Následující postup použijte k odeslání parametrizovaného koncového bodu kanálu na portálu Studio:

1. V pracovním prostoru studia přejdete na stránku **koncové body** .
1. Vyberte kartu **koncové body kanálu** . Pak vyberte svůj koncový bod kanálu.
1. Vyberte kartu **publikované kanály** . Pak vyberte verzi kanálu, kterou chcete spustit.
1. Vyberte **Odeslat**.
1. V dialogovém okně nastavení můžete zadat hodnoty parametrů pro spuštění. V tomto příkladu aktualizujte cestu k datům pro vytvoření modelu pomocí datové sady, která není US.

![Snímek obrazovky, který ukazuje, jak nastavit spuštění parametrizovaného kanálu v Návrháři](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>Odeslat běh pomocí kódu

Koncový bod REST publikovaného kanálu můžete najít na panelu přehled. Zavoláním koncového bodu můžete publikovaný kanál přesměrovat.

Chcete-li provést volání REST, budete potřebovat hlavičku ověřování typu Bearer (OAuth 2,0). Informace o nastavení ověřování pro váš pracovní prostor a o tom, jak provést parametrizované volání REST, najdete v tématu [vytvoření kanálu Azure Machine Learning pro dávkové vyhodnocování](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint).

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvořit parametrizovaný koncový bod školicího kanálu pomocí návrháře.

Úplný návod, jak nasadit model a vytvořit předpovědi, najdete v [kurzu návrháře](tutorial-designer-automobile-price-train-score.md) , který vám umožní naučit a nasazovat regresní model.

Informace o tom, jak publikovat a odeslat koncový bod kanálu pomocí sady SDK, najdete v [tomto článku](how-to-deploy-pipelines.md).
