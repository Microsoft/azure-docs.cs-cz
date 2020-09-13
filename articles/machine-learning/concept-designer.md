---
title: Sestavení modelů ML pomocí návrháře (Preview)
titleSuffix: Azure Machine Learning
description: Přečtěte si o pojmech, konceptech a pracovních postupech, které tvoří návrháře pro Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 06/28/2020
ms.custom: designer
ms.openlocfilehash: 788eebaf28a768acbdb5b1eb778f196fe05dc3e3
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651225"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Co je návrhář služby Azure Machine Learning (Preview)? 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Azure Machine Learning Designer vám umožňuje vizuálně propojit [datové sady](#datasets) a [moduly](#module) na interaktivním plátně a vytvářet modely strojového učení. Informace o tom, jak začít s návrhářem, najdete v tématu [kurz: předpověď ceny automobilu pomocí návrháře.](tutorial-designer-automobile-price-train-score.md)

![Příklad návrháře Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

Návrhář používá [pracovní prostor](concept-workspace.md) Azure Machine Learning k uspořádání sdílených prostředků, jako jsou:

+ [Pipelines](#pipeline)
+ [Datové sady](#datasets)
+ [Výpočetní prostředky](#compute)
+ [Registrované modely](concept-azure-machine-learning-architecture.md#models)
+ [Publikované kanály](#publish)
+ [Koncové body v reálném čase](#deploy)

## <a name="model-training-and-deployment"></a>Školení a nasazení modelu

Návrhář nabízí vizuální plátno pro sestavování, testování a nasazení modelů strojového učení. Pomocí návrháře můžete:

+ [Datové sady](#datasets) a [moduly](#module) přetáhněte na plátno.
+ Připojte moduly a vytvořte [koncept kanálu](#pipeline-draft).
+ Odešlete [běh kanálu](#pipeline-run) pomocí výpočetních prostředků ve vašem pracovním prostoru Azure Machine Learning.
+ Převeďte **školicí kanály** na **odvozené kanály**.
+ [Publikování](#publish) kanálů do **koncového bodu kanálu** REST pro odeslání nového kanálu, který běží s různými parametry a datovými sadami.
    + Publikování **výukového kanálu** pro opakované použití jednoho kanálu pro výuku více modelů při změně parametrů a datových sad.
    + Publikování **kanálu odvození dávky** , aby se předpovědi na nová data pomocí dříve poučeného modelu.
+ [Deploy](#deploy) Nasaďte **kanál pro odvození v reálném čase** do koncového bodu v reálném čase, abyste mohli předpovědi na nových datech v reálném čase.

![Diagram pracovního postupu pro školení, odvozování dávek a odvození v reálném čase v Návrháři](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Kanál

[Kanál](concept-azure-machine-learning-architecture.md#ml-pipelines) se skládá z datových sad a analytických modulů, které se připojují. Kanály mají spoustu použití: můžete vytvořit kanál, který bude nadávat jeden model, nebo ten, který bude vlakem více modelů. Můžete vytvořit kanál, který bude předpovědi v reálném čase nebo v dávce, nebo vytvořit kanál, který pouze čistí data. Kanály umožňují opakovaně používat práci a organizovat vaše projekty.

### <a name="pipeline-draft"></a>Koncept kanálu

Při úpravách kanálu v návrháři je průběh uložen jako **koncept kanálu**. Koncept kanálu můžete kdykoli upravit přidáním nebo odebráním modulů, konfigurací výpočetních cílů, vytvářením parametrů atd.

Platný kanál má tyto vlastnosti:

* Datové sady se mohou připojit pouze k modulům.
* Moduly se můžou připojit jenom k datovým sadám nebo jiným modulům.
* Všechny vstupní porty pro moduly musí mít připojení k toku dat.
* Musí být nastavené všechny požadované parametry pro každý modul.

Až budete připraveni spustit koncept kanálu, odešlete spuštění kanálu.

### <a name="pipeline-run"></a>Spuštění kanálu

Při každém spuštění kanálu se konfigurace kanálu a jeho výsledků ukládají do vašeho pracovního prostoru jako **spuštění kanálu**. Můžete se vrátit k libovolnému spuštění kanálu a zkontrolovat ho pro účely řešení potíží nebo auditování. **Naklonujte** spuštění kanálu, abyste mohli vytvořit novou koncept kanálu, který můžete upravit.

Spuštění kanálu jsou seskupena do [experimentů](concept-azure-machine-learning-architecture.md#experiments) , aby bylo možné uspořádat historii spuštění. Můžete nastavit experiment pro každé spuštění kanálu. 

## <a name="datasets"></a>Datové sady

Datová sada Machine Learning usnadňuje přístup k datům a práci s nimi. V návrháři je zahrnutých několik ukázkových datových sad, které můžete experimentovat s nástrojem. Můžete [zaregistrovat](how-to-create-register-datasets.md) více datových sad, jak je potřebujete.

## <a name="module"></a>Modul

Modul je algoritmus, který je možné provést na datech. Návrhář má několik modulů od vstupních funkcí dat až po procesy školení, bodování a ověřování.

Modul může obsahovat sadu parametrů, pomocí kterých je možné konfigurovat jeho vnitřní algoritmy. Když na plátnu vyberete modul, parametry modulu se zobrazí v podokně Vlastnosti napravo od plátna. Úpravou parametrů v tomto podokně můžete model optimalizovat. Můžete nastavit výpočetní prostředky pro jednotlivé moduly v návrháři. 

:::image type="content" source="./media/concept-designer/properties.png"alt-text="Vlastnosti modulu":::


Další nápovědu k dispozici v knihovně strojového učení, najdete v tématu [Přehled modulu & algoritmu](algorithm-module-reference/module-reference.md). Nápovědu k výběru algoritmu najdete v [tahákm listu s algoritmem Azure Machine Learning](algorithm-cheat-sheet.md).

## <a name="compute-resources"></a><a name="compute"></a> Výpočetní prostředky

Pomocí výpočetních prostředků z pracovního prostoru můžete spustit kanál a hostovat nasazené modely jako koncové body v reálném čase nebo koncové body kanálu (pro odvození dávky). Podporované cíle výpočtů:

| Cílový výpočetní objekt | Školení | Nasazení |
| ---- |:----:|:----:|
| Azure Machine Learning COMPUTE | ✓ | |
| Výpočetní instance Azure Machine Learningu | ✓ | |
| Azure Kubernetes Service | | ✓ |

Cíle výpočetní služby jsou připojeny k vašemu [pracovnímu prostoru Azure Machine Learning](concept-workspace.md). Výpočetní cíle můžete spravovat ve vašem pracovním prostoru v [Azure Machine Learning Studiu](https://ml.azure.com).

## <a name="deploy"></a>Nasazení

Chcete-li provést Inferencing v reálném čase, je nutné nasadit kanál jako **koncový bod v reálném**čase. Koncový bod v reálném čase vytvoří rozhraní mezi externí aplikací a modelem bodování. Volání koncového bodu v reálném čase vrátí výsledky předpovědi do aplikace v reálném čase. Pro volání koncového bodu v reálném čase předáte klíč rozhraní API, který byl vytvořen při nasazení koncového bodu. Koncový bod je založený na REST, oblíbené architektuře, která je vhodná pro projekty webového programování.

Koncové body v reálném čase musí být nasazeny do clusteru služby Azure Kubernetes.

Informace o tom, jak model nasadit, najdete v tématu [kurz: nasazení modelu strojového učení pomocí návrháře](tutorial-designer-automobile-price-deploy.md).

## <a name="publish"></a>Publikování

Kanál můžete také publikovat na **koncový bod kanálu**. Podobně jako koncový bod v reálném čase umožňuje koncový bod kanálu odeslat z externích aplikací spuštění nového kanálu pomocí volání REST. Pomocí koncového bodu kanálu ale nemůžete data odesílat nebo přijímat v reálném čase.

Publikované kanály jsou flexibilní, dají se využít ke školení a reučení modelů, [provádění dávkových Inferencing](how-to-run-batch-predictions-designer.md), zpracování nových dat a mnohem víc. Můžete publikovat více kanálů do jednoho koncového bodu kanálu a určit, která verze kanálu se má spustit.

Publikovaný kanál běží na výpočetních prostředcích, které definujete v konceptu kanálu pro každý modul.

Návrhář vytvoří stejný objekt [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py&preserve-view=true) jako sadu SDK.

## <a name="next-steps"></a>Další kroky

* Seznamte se se základy prediktivní analýzy a strojového učení s využitím [kurzu: předpověď ceny automobilu pomocí návrháře](tutorial-designer-automobile-price-train-score.md)
* Naučte se, jak upravit existující [ukázky Návrháře](samples-designer.md) a přizpůsobit je vašim potřebám.
