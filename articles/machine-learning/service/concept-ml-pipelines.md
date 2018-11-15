---
title: Vytváření kanálů machine learning – služba Azure Machine Learning
description: V tomto článku najdete informace o machine learningu kanály, které můžete sestavit s využitím Azure Machine Learning SDK for Python a výhody použití kanály. Machine learning (ML) kanály využívají odborníci přes data sestavení, optimalizovat a spravovat jejich strojového učení pracovních postupů.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 11/07/2018
ms.openlocfilehash: 099b59cde4ee438f16b9d7e77bd81c004006cb71
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684854"
---
# <a name="pipelines-and-azure-machine-learning"></a>Kanály a Azure Machine Learning

V tomto článku najdete informace o machine learningu kanály, které můžete sestavit s využitím Azure Machine Learning SDK for Python a výhody použití kanály.

## <a name="what-are-machine-learning-pipelines"></a>Co jsou kanály machine learning?

Pomocí machine learning (ML) kanály, odborníci přes data, datovými architekty a odborníky v oblasti můžete spolupracovat na kroky:
+ Příprava dat, jako je například normalizations a transformace
+ Cvičení modelu
+ Vyhodnocení modelu
+ Nasazení 

Následující diagram znázorňuje kanál příkladu:

[ ![Služby machine learning kanály ve službě Azure Machine Learning](./media/concept-ml-pipelines/pipelines.png) ] (. / media/concept-ml-pipelines/machine-learning-pipelines-big.png#lightbox)

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Proč vytvářet kanály pomocí služby Azure Machine Learning?

[Azure Machine Learning SDK pro Python](#the-python-sdk-for-pipelines) lze použít k vytvoření kanálů ML a jde o odesílání a sledování spuštění jednotlivých kanálu.

S kanály můžete optimalizovat pracovní postup s jednoduchost, rychlost, přenositelnost a opakované použití. Při sestavování kanálů v Azure Machine learningu, můžete se soustředit na co znáte nejlepší &mdash; strojového učení &mdash; místo infrastruktury.

Použití samostatné kroky umožňuje znovu spustit pouze kroky, které je třeba upravit a testování pracovního postupu. Krok je výpočetní jednotka v kanálu. Jak je uvedeno ve výše uvedeném diagramu, úkolů přípravy dat může zahrnovat mnoho kroků, včetně, avšak nikoli výlučně normalizace, transformace, ověření a snadné. Zdroje dat a dočasných dat se využívají opakovaně v kanálu, který uloží výpočetní čas a prostředky. 

Jakmile kanálu je určen, je často Další doladění kolem smyčky školení kanálu. Když znovu spusťte kanál, spuštění přejde do kroků, které je potřeba znovu spustit, jako je například aktualizované cvičný skript a přeskočí, co se nezměnil. Stejné paradigma se vztahuje na beze změny skripty používané pro provedení kroku. 

Azure Machine Learning můžete použít různé sady nástrojů a architektur, jako je Microsoft Cognitive Toolkit nebo TensorFlow u každého kroku ve vašem kanálu. Souřadnice Azure mezi různými [cílových výpočetních prostředí](concept-azure-machine-learning-architecture.md) použijete tak, aby dočasných dat je s cíli podřízené výpočetní snadno sdílet. 

Je možné [sledujte metriky pro vaše experimenty kanálu](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) přímo na webu Azure portal. 

## <a name="key-advantages"></a>Klíčové výhody

Klíčové výhody pro vytváření kanálů pro strojového učení pracovních postupů je:

|Hlavní výhodou|Popis|
|:-------:|-----------|
|**Bezobslužné&nbsp;běží**|Naplánujte pár kroků ke spuštění paralelně nebo postupně spolehlivé a bezobslužné způsobem. Od přípravy dat a modelování můžete poslední dny nebo týdny, můžete nyní soustředit na další úkoly při spuštění kanálu. |
|**Smíšené a různých výpočetních**|Použití více kanálů, které jsou spolehlivě koordinovat napříč heterogenní a škálovatelné výpočetní prostředí a úložišť. Kanál jednotlivé kroky můžete spustit na různými výpočetními cíle, jako je HDInsight, virtuálních počítačů využívajících GPU datové vědy a Databricks, aby efektivní využívání možností výpočetního prostředí k dispozici.|
|**Opětovné použití**|Kanály můžou být převést na šablonu pro konkrétní scénáře, jako je například přetrénování a dávkové bodování.  Může být spuštěna z externích systémů prostřednictvím jednoduché volání REST.|
|**Sledování a správy verzí**|Místo ručně sledování data a výsledek cesty při opakovaném, pomocí kanálů SDK explicitně název a verze data zdrojů, vstupy a výstupy taky umožňuje spravovat skripty a data odděleně pro vyšší produktivitu.|

## <a name="the-python-sdk-for-pipelines"></a>Python SDK pro kanály

Použití Pythonu k vytvoření kanálů ML. Sada SDK Azure Machine Learning nabízí imperativní konstrukce pro pořadí úloh a paralelní provádění kroků ve vašich kanálů, když je k dispozici žádná data závislost. Můžete pracovat s ním v poznámkových blocích Jupyter nebo v jiné upřednostňované prostředí IDE. 

Použití závislostí deklarativní data, můžete optimalizovat vaše úkoly. Sada SDK zahrnuje rozhraní předem připravené moduly pro běžné úkoly jako přenos dat a modelu publikování. Rozhraní je možné rozšířit na model vlastní zásady odvíjející implementací vlastní kroky, které jsou opakovaně použitelné pro kanály. Cílových výpočetních prostředí a prostředků úložiště je také možné spravovat přímo ze sady SDK.

Kanály můžou být uložena jako šablony a mohou být nasazeny na koncový bod REST, můžete plánovat úlohy dávkové bodování nebo přeučení.

Podívejte se [Python SDK referenční dokumenty pro kanály](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) a Poznámkový blok v další části se dozvíte, jak vytvářet vlastní.

## <a name="example-notebooks"></a>Příklad poznámkové bloky
 
Následující Poznámkový blok ukazuje kanálů pomocí služby Azure Machine Learning: [kanál nebo kanál batch-scoring.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb).
 
Získáte tento poznámkový blok:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
