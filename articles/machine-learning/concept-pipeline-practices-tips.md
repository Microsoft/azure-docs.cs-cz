---
title: Iterace a vývoj kanálů strojového učení
titleSuffix: Azure Machine Learning
description: Vzory, postupy a tipy pro rychlý vývoj
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 05/01/2020
ms.openlocfilehash: 2ea353469ed111eebb591aa6ba86c652683cc2f0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858191"
---
# <a name="iterating-and-evolving-machine-learning-pipelines"></a>Iterace a vývoj kanálů strojového učení

Azure Machine Learning kanály poskytují účinný způsob, jak naplánovat modularizaci váš kód, znovu použít výsledky a optimalizovat výpočetní prostředky. Tady je několik praktických tipů a postupů pro práci s kanály.

## <a name="how-do-you-get-started-with-pipelines"></a>Jak začít s kanály?

Existuje několik možností, jak začít, pokud jsou kanály novinkou:

* Pokud se vám nejlépe naučíte, jak číst a znovu vytvořit proces konstrukce, je vhodné [vytvořit a spustit kanály strojového učení se sadou Azure Machine Learning SDK](how-to-create-your-first-pipeline.md) . 
* Pokud se chcete naučit interaktivně v Jupyter poznámkovém bloku, kanál vyvinutý v [kanálu Azure Machine Learning: Začínáme](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-getting-started.ipynb) Poznámkový blok pro vás může být nejvhodnější
* Pokud upřednostňujete situaci s prvním kódem, klonování úložiště [Demo Azure Machine Learningch kanálů](https://github.com/microsoft/aml-pipelines-demo) nabízí dobrý výchozí bod.

## <a name="how-do-you-modularize-pipeline-code"></a>Jak naplánovat modularizaci kód kanálu? 

Moduly a `ModuleStep` třídy poskytují skvělou příležitost pro naplánovat modularizacií kódu ml. Je ale potřeba si pamatovat na vědomí, že přesun mezi kroky kanálu je mnohem dražší než volání funkce. Otázka, kterou je třeba položit, není tak velká, protože tyto funkce a data jsou koncepčně jiné než v této jiné části? " ale "Přeji si, aby se tyto funkce a data vyvinuly samostatně?" nebo je tato výpočetní náročná a můžu použít svůj výstup? " Další informace najdete v tématu thisn'tebook [jak vytvořit modul, ModuleVersion a použít ho v kanálu s ModuleStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb).

Jak už bylo popsáno dříve, oddělení přípravy dat ze školení je často takovou příležitostí. V některých případech je příprava dat složitá a časově náročná, což znamená, že je možné proces rozdělit do samostatných kroků kanálu. Mezi další příležitosti patří testování a analýza po školení. 

## <a name="how-do-you-speed-pipeline-iteration"></a>Jak Urychlete iteraci kanálu? 

Mezi běžné postupy pro rychlé iterace kanálů patří: 

- Rychlá klonování kanálu (vytvoření kopie) a rychlé spuštění kanálu
- Udržování spuštěné výpočetní instance, aby nedocházelo k času spuštění
- Konfigurace dat a kroků pro povolení opětovného použití umožní kanálu přeskočit přepočítání nezměněných dat.

Pokud chcete rychle iterovat, můžete vytvořit profil kanálu, vytvořit kanál a znovu spustit kanál. Další užitečnou technikou je, že pokud udržujete výpočetní výkon, nebudete se vám účtovat náklady na nové výpočetní prostředky. Pokud nastavíte krok, který umožní opakované použití výsledku spuštění, pak opakované spuštění znovu použije výsledky (Pokud se v krocích nemění).

## <a name="how-do-you-collaborate-using-ml-pipelines"></a>Jak spolupracujete s kanály ML? 

Samostatné kanály jsou přirozené čáry, na kterých je možné rozdělit úsilí. Více vývojářů nebo dokonce několik týmů může pracovat na různých krocích, pokud jsou data a argumenty toku mezi jednotlivými kroky dohodnuty. 

Během aktivního vývoje můžete načíst `PipelineRun` a `StepRun` spustit výsledky z pracovního prostoru, použít tyto objekty ke stažení finálního a mezilehlého výstupu a použít tyto artefakty pro vlastní modulární práci.

## <a name="use-pipelines-to-test-techniques-in-isolation"></a>Použití kanálů k testování technik v izolaci

Real-World ML řešení obecně zahrnují výrazné přizpůsobení každého kroku. Neupravená data se často musí připravovat nějakým způsobem: filtrováno, transformovat a rozšiřovat. Školicí procesy mohou mít několik potenciálních architektur a pro obsáhlý Learning mnoho možných variant pro velikosti vrstev a funkce aktivace. I v případě konzistentní architektury může vyhledávání pomocí parametrů vytvořit významnou službu WINS.

Kromě nástrojů, jako je [AutoML](concept-automated-ml.md) a [automatizované hledání parametrů](how-to-tune-hyperparameters.md), můžou být kanály důležitým nástrojem pro testovací řešení a/B. Pokud máte několik variant svých kroků kanálu, je snadné vygenerovat samostatné běhy, které se pokoušejí jejich variace: 

```python
data_preparation_variants = [data1, data2, data3]
data_augmentation_variants = [aug1, aug2]
architecture_variants = [train1, train2, train3]

# Cartesian product
all_variants = np.array(np.meshgrid(data_preparation_variants,data_augmentation_variants,architecture_variants)).T.reshape(-1,3)

pipelines = [Pipeline(workspace=ws, steps=variant.tolist(), description=str(variant)) for variant in all_variants]

```

