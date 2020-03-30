---
title: Co je distribuovaný trénink?
titleSuffix: Azure Machine Learning
description: Přečtěte si o distribuovaných školeních a o tom, jak ho Azure Machine Learning podporuje.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: a0d5bf795e4759a105b9a235770f37aa10bd6751
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385542"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Distribuovaná školení s Azure Machine Learning

V tomto článku se dozvíte o distribuované školení a jak Azure Machine Learning podporuje pro modely hlubokéučení. 

V distribuované školení pracovní vytížení trénovat model je rozdělena a sdílena mezi více mini procesory, nazývané pracovní uzly. Tyto pracovní uzly pracují paralelně, aby urychlily školení modelu. Distribuované školení lze použít pro tradiční modely ML, ale je vhodnější pro výpočetní a časově náročné úkoly, jako [je hluboké učení](concept-deep-learning-vs-machine-learning.md) pro školení hlubokých neuronových sítí.

## <a name="deep-learning-and-distributed-training"></a>Hloubkové učení a distribuovaná odborná příprava 

Existují dva hlavní typy distribuovaného [tréninku: paralelismus dat](#data-parallelism) a [modelový paralelismus](#model-parallelism). Pro distribuované školení o modelech hlubokého učení podporuje [Sada Azure Machine Learning SDK v Pythonu](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) integraci s oblíbenými architekturami, PyTorch a TensorFlow. Obě architektury využívají paralelismus dat pro distribuované školení a mohou využít [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) pro optimalizaci výpočetních rychlostí. 

* [Distribuované školení s PyTorch](how-to-train-pytorch.md#distributed-training)

* [Distribuované školení s TensorFlow](how-to-train-tensorflow.md#distributed-training)

Modely ML, které nevyžadují distribuované školení, najdete [v tématu trénování modelů s Azure Machine Learning](concept-train-machine-learning-model.md#python-sdk) pro různé způsoby, jak trénovat modely pomocí PythonU SDK.

## <a name="data-parallelism"></a>Paralelismus dat

Paralelismus dat je nejjednodušší implementovat dva přístupy distribuované školení a je dostačující pro většinu případů použití.

V tomto přístupu jsou data rozdělena do oddílů, kde se počet oddílů rovná celkovému počtu dostupných uzlů ve výpočetním clusteru. Model je zkopírován v každém z těchto uzlů pracovníka a každý pracovník pracuje na vlastní podmnožinu dat. Mějte na paměti, že každý uzel musí mít kapacitu pro podporu modelu, který je trénovaný, to je model musí zcela vejde na každý uzel.

Každý uzel nezávisle vypočítá chyby mezi jeho předpovědi pro jeho vzorky školení a popisované výstupy. Na druhé straně každý uzel aktualizuje svůj model na základě chyb a musí sdělit všechny své změny do ostatních uzlů aktualizovat své odpovídající modely. To znamená, že pracovní uzly potřebují synchronizovat parametry modelu nebo přechody na konci dávkového výpočtu, aby bylo zajištěno, že trénují konzistentní model. 

## <a name="model-parallelism"></a>Modelparalelismus

V paralelismu modelu, označovaném také jako síťový paralelismus, je model segmentován do různých částí, které mohou běžet souběžně v různých uzlech, a každý z nich bude spuštěn na stejných datech. Škálovatelnost této metody závisí na stupni paralelizace úkolu algoritmu a je složitější implementovat než paralelismus dat. 

V paralelismu modelu pracovní uzly stačí synchronizovat sdílené parametry, obvykle jednou pro každý krok šíření vpřed nebo vzad. Větší modely také nejsou problém, protože každý uzel pracuje na podčásti modelu na stejných trénovacích dat.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [nastavit školicí prostředí](how-to-set-up-training-targets.md) pomocí sady Python SDK.
* Technický příklad naleznete scénář [referenční architektury](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning).
* [Vlak ML modely s TensorFlow](how-to-train-tensorflow.md).
* [Vlak ML modely s PyTorch](how-to-train-pytorch.md). 