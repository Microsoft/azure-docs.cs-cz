---
title: 'Mezi kanály: optimalizovat ML pro rychlost, přenositelnost a opakovaně používat'
titleSuffix: Azure Machine Learning service
description: V tomto článku najdete informace o machine learningu kanály, které můžete sestavit s využitím Azure Machine Learning SDK for Python a výhody použití kanály. Kanály Machine Learning (ML) používají datoví vědci k vytváření, optimalizaci a správě svých pracovních postupů strojového učení.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 11/07/2018
ms.custom: seodec18
ms.openlocfilehash: e2874c4a40a2347fd47e93c99fdec7ff5e81aec1
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53308195"
---
# <a name="build-machine-learning-pipelines-with-the-azure-machine-learning-service"></a>Sestavit machine learning kanály ve službě Azure Machine Learning

V tomto článku najdete informace o machine learningu kanály, které můžete vytvářet pomocí sady Azure Machine Learning SDK pro Python a výhody použití kanály.

## <a name="what-are-machine-learning-pipelines"></a>Co jsou kanály machine learning?

Pomocí machine learning (ML) kanály, odborníci přes data, datovými architekty a odborníky v oblasti můžete spolupracovat na kroky:
+ Příprava dat, jako je například normalizations a transformace
+ Cvičení modelu
+ Vyhodnocení modelu
+ Nasazení 

Následující diagram znázorňuje kanál příkladu:

![Strojové učení kanály ve službě Azure Machine Learning](./media/concept-ml-pipelines/pipelines.png)

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Proč vytvářet kanály pomocí služby Azure Machine Learning?

Můžete použít [Azure Machine Learning SDK pro Python](#the-python-sdk-for-pipelines) k vytvoření kanálů ML, a jde o odesílání a sledování spuštění jednotlivých kanálu.

S kanály můžete optimalizovat pracovní postup s jednoduchost, rychlost, přenositelnost a opakované použití. Při sestavování kanálů v Azure Machine learningu, můžete se soustředit na svou odbornost, strojové učení, nikoli na infrastrukturu.

Použití samostatné kroky umožňuje znovu spustit pouze kroky, které potřebujete, jak upravit a testování pracovního postupu. Krok je výpočetní jednotka v kanálu. Jak je znázorněno na předchozím obrázku, úkolů přípravy dat může zahrnovat mnoho kroků. Ty zahrnují, ale nejsou omezené na normalizace, transformace, ověření a snadné. Zdroje dat a dočasných dat se využívají opakovaně v kanálu, který uloží výpočetní čas a prostředky. 

Po kanálu je určená, je často Další doladění kolem smyčky školení kanálu. Když znovu spusťte kanál, spuštění přejde do kroků, které je potřeba znovu spustit, jako je například aktualizované cvičný skript a přeskočí, co se nezměnil. Stejné paradigma se vztahuje na beze změny skripty používané pro provedení kroku. 

Pomocí služby Azure Machine Learning můžete použít různé sady nástrojů a architektur, jako je například Microsoft Cognitive Toolkit nebo TensorFlow, pro každý krok v kanálu. Souřadnice Azure mezi různými [cílových výpočetních prostředí](concept-azure-machine-learning-architecture.md) použijete, tak, aby dočasných dat je s cíli podřízené výpočetní snadno sdílet. 

Je možné [sledujte metriky pro vaše experimenty kanálu](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) přímo na webu Azure portal. 

## <a name="key-advantages"></a>Klíčové výhody

Klíčové výhody pro vytváření kanálů pro strojového učení pracovní postupy jsou:

|Hlavní výhodou|Popis|
|:-------:|-----------|
|**Bezobslužné&nbsp;běží**|Naplánujte, aby několik kroků běželo buď paralelně, nebo v daném pořadí, spolehlivým způsobem bez nutnosti obsluhy. Protože přípravy dat a modelování můžete poslední dny nebo týdny, můžete nyní soustředit na další úkoly při spuštění kanálu. |
|**Smíšené a různých výpočetních**|Použití více kanálů, které jsou spolehlivě koordinovat napříč heterogenní a škálovatelné výpočetní prostředí a úložišť. Kanál jednotlivé kroky můžete spustit s různými výpočetními cíli, jako je HDInsight, virtuálních počítačů využívajících GPU datové vědy a Databricks. To efektivně využije dostupné výpočetní možnosti.|
|**Opětovné použití**|Vytvoření šablon z kanálů pro konkrétní scénáře, jako je například přetrénování a dávkové bodování. Aktivujte je z externích systémů prostřednictvím jednoduché volání REST.|
|**Sledování a správy verzí**|Místo ručně sledování data a výsledek cesty při opakovaném, pomocí kanálů SDK explicitně název a verze data zdrojů, vstupy a výstupy. Můžete také spravovat skripty a data odděleně pro vyšší produktivitu.|

## <a name="the-python-sdk-for-pipelines"></a>Python SDK pro kanály

Použití Pythonu k vytvoření kanálů ML. Sada SDK Azure Machine Learning nabízí imperativní konstrukce pro pořadí úloh a paralelní provádění kroků ve vašich kanálů, když je k dispozici žádná data závislost. Můžete pracovat s ním v poznámkových bloků Jupyter, nebo v jiné upřednostňované integrovaného vývojového prostředí. 

Použití závislostí deklarativní data, můžete optimalizovat vaše úkoly. Sada SDK zahrnuje rozhraní předem připravené moduly pro běžné úkoly, jako jsou přenosy dat a modelu publikování. Můžete rozšířit rozhraní modelu vlastní zásady odvíjející, díky implementaci vlastní kroky, které jsou opakovaně použitelné pro kanály. Můžete také spravovat cílových výpočetních prostředí a prostředky úložiště přímo ze sady SDK.

Můžete uložit kanály jako šablon a nasazovat je do koncového bodu REST, můžete plánovat úlohy dávkové bodování nebo přeučení.

Chcete zjistit, jak vytvářet vlastní, viz [Python SDK referenční dokumenty pro kanály](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) a poznámkového bloku v další části.

## <a name="example-notebooks"></a>Příklad poznámkové bloky
 
Tyto poznámkové bloky ukazují kanálů pomocí služby Azure Machine Learning: [how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines).
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup

Zjistěte, jak [vytvořit svůj první kanál](how-to-create-your-first-pipeline.md).
