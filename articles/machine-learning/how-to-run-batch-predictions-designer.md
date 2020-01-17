---
title: Spuštění dávkového předpovědiu pomocí návrháře Azure Machine Learning (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se naučit model a nastavit kanál předpovědi dávky pomocí návrháře. Nasaďte kanál jako parametrizovanou webovou službu, která se dá aktivovat z libovolné knihovny HTTP.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 11/19/2019
ms.custom: Ignite2019
ms.openlocfilehash: 1e346d2542193ec1880ad0a56bd6afa1b0a46890
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122623"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Spuštění dávkového předpovědi pomocí návrháře Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto postupu se naučíte, jak používat návrháře k učení modelu a nastavení kanálu předpovědi a webové služby Batch. Předpovědi dávek umožňuje průběžné a vyhodnocování vyškolených modelů ve velkých sadách dat, volitelně nakonfigurovaných jako webová služba, která se dá aktivovat z libovolné knihovny HTTP. 

Informace o nastavení služby Batch vyhodnocování pomocí sady SDK najdete v doprovodném [postupu](how-to-use-parallel-run-step.md).

V tomto postupu se naučíte následující úlohy:

> [!div class="checklist"]
> * Vytvoření experimentu na základním ML v kanálu
> * Vytvoření parametrizovaného kanálu pro odvození dávky
> * Ruční správa a spouštění kanálů nebo použití koncového bodu REST

## <a name="prerequisites"></a>Požadavky

1. Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

1. Vytvořte [pracovní prostor](tutorial-1st-experiment-sdk-setup.md).

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/).

Tento postup předpokládá základní znalosti o vytvoření jednoduchého kanálu v návrháři. Úvodní Úvod k Návrháři dokončíte pomocí tohoto [kurzu](tutorial-designer-automobile-price-train-score.md). 

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Pokud chcete vytvořit kanál pro odvození dávky, budete nejdřív potřebovat experiment strojového učení. Pokud ho chcete vytvořit, přejděte na kartu **Návrhář** v pracovním prostoru a vytvořte nový kanál výběrem možnosti **snadno používané předem připravené moduly** .

![Domovská stránka Návrháře](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-1.png)

Následuje jednoduchý model strojového učení pro demonstrační účely. Tato data jsou registrovanou datovou sadou vytvořenou z dat diabetes otevřených pomocí Azure Open DataSet. V [části postupy](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) pro registraci datových sad z otevřených datových sad Azure. Data jsou rozdělená na školicí a ověřovací sady a vyškolený rozhodovací strom se vystaví a vyhodnotí skóre. Aby bylo možné vytvořit kanál Inferencing, musí se kanál aspoň jednou spustit. Kliknutím na tlačítko **Spustit** kanál spustíte.

![Vytvoření jednoduchého experimentu](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-2.png)

## <a name="create-a-batch-inference-pipeline"></a>Vytvoření kanálu odvození dávky

Teď, když je kanál spuštěný, je k dispozici nová možnost pro **spuštění** a **publikování** s názvem **vytvořit odvození kanálu**. Klikněte na rozevírací seznam a vyberte **kanál odvození dávky**.

![Vytvořit kanál odvození dávky](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-5.png)

Výsledkem je výchozí kanál odvození dávky. Patří sem uzel pro instalaci experimentu s vaším původním kanálem, uzel pro nezpracované data pro bodování a uzel, který bude vyhodnocovat nezpracovaná data oproti původnímu kanálu.

![Výchozí odvozený kanál dávky](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-6.png)

Chcete-li změnit chování procesu dávkového Inferencing, můžete přidat další uzly. V tomto příkladu přidáte uzel pro náhodné vzorkování ze vstupních dat před vyhodnocením. Vytvořte **oddíl a ukázkový** uzel a umístěte ho mezi nezpracovaná data a uzly bodování. Potom kliknutím na **oddíl oddíl a ukázkový** uzel získáte přístup k nastavení a parametrům.

![Nový uzel](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-7.png)

Parametr *frekvence vzorkování* určuje, v jakém procentu původní sady dat se vybral náhodný vzorek. Toto je parametr, který bude vhodný k častému úpravám, takže ho aktivujete jako parametr kanálu. Parametry kanálu lze změnit za běhu a lze je zadat v objektu datové části při spuštění kanálu z koncového bodu REST. 

Pokud chcete toto pole Povolit jako parametr kanálu, klikněte na tři tečky nad polem a pak klikněte na **Přidat do parametru kanálu**. 

![Ukázková nastavení](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-8.png)

Dále zadejte název a výchozí hodnotu parametru. Název bude použit k identifikaci parametru a jeho určení ve volání REST.

![Parametr kanálu](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-9.png)

## <a name="deploy-batch-inferencing-pipeline"></a>Nasazení kanálu Batch Inferencing

Nyní jste připraveni k nasazení kanálu. Klikněte na tlačítko **nasadit** , které otevře rozhraní pro nastavení koncového bodu. Klikněte na rozevírací seznam a vyberte **Nový PipelineEndpoint**.

![Nasazení kanálu](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-10.png)

Zadejte název a volitelný popis koncového bodu. V dolní části uvidíte parametr `sample-rate`, u kterého jste nakonfigurovali výchozí hodnotu 0,8. Až budete připraveni, klikněte na **nasadit**.

![Koncový bod instalace](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-11.png)

## <a name="manage-endpoints"></a>Správa koncových bodů 

Po dokončení nasazení přejděte na kartu **koncové body** a klikněte na název koncového bodu, který jste právě vytvořili.

![Odkaz na koncový bod](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-12.png)

Tato obrazovka zobrazuje všechny publikované kanály v rámci konkrétního koncového bodu. Klikněte na svůj kanál Inferencing.

![Odvození kanálu](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-13.png)

Na stránce Podrobnosti o kanálu najdete detailní historii spuštění a informace o připojovacím řetězci pro váš kanál. Kliknutím na tlačítko **Spustit** vytvoříte ruční spuštění kanálu.

![Podrobnosti kanálu](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-14.png)

V instalačním programu můžete zadat popis běhu a změnit hodnotu parametrů kanálu. Tentokrát znovu spusťte kanál Inferencing s vzorkovací frekvencí 0,9. Kliknutím na **Spustit** kanál spustíte.

![Spuštění kanálu](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-15.png)

Karta **Spotřeba** obsahuje koncový bod REST pro spuštění vašeho kanálu. Chcete-li provést volání REST, budete potřebovat hlavičku ověřování typu Bearer (OAuth 2,0). Další informace o nastavení ověřování pro váš pracovní prostor a o tom, jak se provádí parametrizované volání REST, najdete v následující [části kurzu](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint) .

## <a name="next-steps"></a>Další kroky

Postupujte podle [kurzu](tutorial-designer-automobile-price-train-score.md) návrháře a Projděte a nasaďte regresní model.
