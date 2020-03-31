---
title: Vytváření modelů ML s návrhářem
titleSuffix: Azure Machine Learning
description: Seznamte se s podmínkami, koncepty a pracovními postupy, které tvoří návrháře pro Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.openlocfilehash: 78a6e7fa8d030185f537136a3a2124d8bc59d808
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037628"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Co je návrhář služby Azure Machine Learning (Preview)? 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Azure Machine Learning Designer umožňuje vizuálně připojit [datové sady](#datasets) a [moduly](#module) na interaktivní plátno k vytvoření modelů strojového učení. Chcete-li se dozvědět, jak začít s návrhářem, [najdete v tématu Výuka: Předvídání ceny automobilů s návrhářem](tutorial-designer-automobile-price-train-score.md)

![Příklad návrháře Azure Machine Learning](./media/concept-designer/designer-drag-and-drop.gif)

Návrhář používá váš [pracovní prostor](concept-workspace.md) Azure Machine Learning k uspořádání sdílených prostředků, jako jsou:

+ [Kanály](#pipeline)
+ [Soubory](#datasets)
+ [Výpočetní prostředky](#compute)
+ [Registrované modely](concept-azure-machine-learning-architecture.md#models)
+ [Publikované kanály](#publish)
+ [Koncové body v reálném čase](#deploy)

## <a name="model-training-and-deployment"></a>Školení a nasazení modelu

Návrhář poskytuje vizuální plátno pro vytváření, testování a nasazování modelů strojového učení. S designérem můžete:

+ Přetažení [mažete datové sady](#datasets) a [moduly](#module) na plátno.
+ Připojte moduly dohromady a vytvořte [koncept kanálu](#pipeline-draft).
+ Odešlete [spuštění kanálu](#pipeline-run) pomocí výpočetních prostředků v pracovním prostoru Azure Machine Learning.
+ Převeďte **své školicí kanály** **na odvozené kanály**.
+ [Publikujte](#publish) kanály do **koncového bodu kanálu** REST a odesílejte nové spuštění kanálu s různými parametry a datovými sadami.
    + Publikovat **trénovací kanál** pro opětovné použití jednoho kanálu trénovat více modelů při změně parametrů a datových sad.
    + Publikujte **kanál dávkových odvození,** abyste vytvořili předpovědi na nová data pomocí dříve trénovaného modelu.
+ [Nasaďte](#deploy) **kanál odvození v reálném čase** do koncového bodu v reálném čase a vytvořte předpovědi na nová data v reálném čase.

![Diagram pracovního postupu pro školení, odvození dávky a odvození v reálném čase v návrháři](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Kanál

[Kanál](concept-azure-machine-learning-architecture.md#ml-pipelines) se skládá z datových sad a analytických modulů, které spojujete dohromady. Potrubí mají mnoho použití: můžete vytvořit potrubí, které trénuje jeden model, nebo ten, který trénuje více modelů. Můžete vytvořit kanál, který umožňuje předpovědi v reálném čase nebo v dávce, nebo vytvořit kanál, který pouze čistí data. Kanály umožňují znovu použít práci a uspořádat projekty.

### <a name="pipeline-draft"></a>Pracovní verze kanálu

Při úpravách kanálu v návrháři se průběh uloží jako **koncept kanálu**. Pracovní verzi kanálu můžete kdykoli upravit přidáním nebo odebráním modulů, konfigurací výpočetních cílů, vytvořením parametrů a tak dále.

Platný kanál má tyto vlastnosti:

* Datové sady se mohou připojit pouze k modulům.
* Moduly se mohou připojit pouze k datovým souborům nebo jiným modulům.
* Všechny vstupní porty pro moduly musí mít nějaké připojení k toku dat.
* Pro každý modul musí být nastaveny všechny požadované parametry.

Až budete připraveni spustit koncept kanálu, odešlete spuštění kanálu.

### <a name="pipeline-run"></a>Spuštění kanálu

Při každém spuštění kanálu, konfigurace kanálu a jeho výsledky jsou uloženy ve vašem pracovním prostoru jako **spuštění kanálu**. Můžete se vrátit k libovolnému spuštění kanálu a zkontrolovat jej pro účely řešení potíží nebo auditování. **Klonujte** spuštění kanálu a vytvořte novou pracovní verzi kanálu, kterou můžete upravit.

Spuštění kanálu jsou seskupeny do [experimentů](concept-azure-machine-learning-architecture.md#experiments) pro uspořádání historie spuštění. Experiment můžete nastavit pro každé spuštění kanálu. 

## <a name="datasets"></a>Datové sady

Datová sada strojového učení usnadňuje přístup k vašim datům a práci s ním. V návrháři je zahrnuto několik ukázkových datových sad, se kterými můžete experimentovat. Můžete [zaregistrovat](how-to-create-register-datasets.md) více datových sad, jak je potřebujete.

## <a name="module"></a>Modul

Modul je algoritmus, který je možné provést na datech. Návrhář má řadu modulů od funkcí příchozího přenosu dat až po procesy školení, vyhodnocování a ověřování.

Modul může obsahovat sadu parametrů, pomocí kterých je možné konfigurovat jeho vnitřní algoritmy. Když na plátnu vyberete modul, parametry modulu se zobrazí v podokně Vlastnosti napravo od plátna. Úpravou parametrů v tomto podokně můžete model optimalizovat. Výpočetní prostředky pro jednotlivé moduly v návrháři můžete nastavit. 

![Vlastnosti modulu](./media/concept-designer/properties.png)

Nápovědu k navigaci v knihovně algoritmů strojového učení, které jsou k dispozici, najdete v článku [Přehled odkazů na modul algoritmus &](algorithm-module-reference/module-reference.md)

## <a name="compute-resources"></a><a name="compute"></a>Výpočetní prostředky

Pomocí výpočetních prostředků z vašeho pracovního prostoru můžete spouštět kanál a hostovat nasazené modely jako koncové body v reálném čase nebo koncové body kanálu (pro dávkové odvození). Podporované výpočetní cíle jsou:

| Cílový výpočetní objekt | Školení | Nasazení |
| ---- |:----:|:----:|
| Výpočetní prostředky Azure Machine Learning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Výpočetní cíle se připojují k [pracovnímu prostoru Azure Machine Learning](concept-workspace.md). Výpočetní cíle můžete spravovat ve svém pracovním prostoru ve [službě Azure Machine Learning Studio (klasicky).](https://ml.azure.com)

## <a name="deploy"></a>Nasazení

Chcete-li provádět odvození v reálném čase, je nutné nasadit kanál jako **koncový bod v reálném čase**. Koncový bod v reálném čase vytvoří rozhraní mezi externí aplikací a modelem vyhodnocování. Volání koncového bodu v reálném čase vrátí výsledky předpovědi do aplikace v reálném čase. Chcete-li volat koncový bod v reálném čase, předáte klíč rozhraní API, který byl vytvořen při nasazení koncového bodu. Koncový bod je založen na REST, oblíbené architektuře pro projekty webového programování.

Koncové body v reálném čase musí být nasazeny do clusteru služby Azure Kubernetes.

Informace o nasazení modelu najdete [v tématu Výuka: Nasazení modelu strojového učení s návrhářem](tutorial-designer-automobile-price-deploy.md).

## <a name="publish"></a>Publikování

Můžete také publikovat kanál do **koncového bodu kanálu**. Podobně jako koncový bod v reálném čase koncový bod kanálu umožňuje odeslat nové spuštění kanálu z externích aplikací pomocí volání REST. Však nelze odesílat nebo přijímat data v reálném čase pomocí koncového bodu kanálu.

Publikované kanály jsou flexibilní, lze je použít k trénování nebo rekvalifikaci modelů, [provádění dávkových odvození](how-to-run-batch-predictions-designer.md), zpracování nových dat a mnoho dalšího. Můžete publikovat více kanálů do jednoho koncového bodu kanálu a určit, kterou verzi kanálu spustit.

Publikovaný kanál běží na výpočetníprostředky, které definujete v návrhu kanálu pro každý modul.

Návrhář vytvoří stejný [Objekt PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) jako sada SDK.


## <a name="moving-from-the-visual-interface-to-the-designer"></a>Přechod z vizuálního rozhraní na návrháře

Vizuální rozhraní (náhled) byla aktualizována a je nyní Azure Machine Learning designer (preview). Návrhář byl přepracován tak, aby používal back-end založený na kanálu, který se plně integruje s dalšími funkcemi Azure Machine Learning. 

V důsledku těchto aktualizací byly některé koncepty a termíny pro vizuální rozhraní změněny nebo přejmenovány. Nejdůležitější koncepční změny naleznete v následující tabulce. 

| Koncept v návrháři | Dříve ve vizuálním rozhraní |
| ---- |:----:|
| Pracovní verze kanálu | Experiment |
| Koncový bod v reálném čase | Webová služba |

### <a name="migrating-to-the-designer"></a>Migrace na návrháře

Existující experimenty s vizuálními rozhraními a webové služby můžete převést na kanály a koncové body v reálném čase v návrháři. K migraci prostředků vizuálního rozhraní použijte následující kroky:

[!INCLUDE [migrate from the visual interface](../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Další kroky

* Naučte se základy prediktivní analýzy a strojového učení pomocí [kurzu: Předvídejte cenu automobilů s designérem](tutorial-designer-automobile-price-train-score.md)
* Přečtěte si, jak upravit existující [ukázky návrháře](samples-designer.md) tak, aby byly přizpůsobit vašim potřebám.

