---
title: Co je distribuované školení?
titleSuffix: Azure Machine Learning
description: Přečtěte si o distribuovaném školení a o tom, jak ho Azure Machine Learning podporuje.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 62edee6a882191551ce2409646ea8b617576c059
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651158"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Distribuované školení pomocí Azure Machine Learning

V tomto článku se dozvíte o distribuovaném školení a o tom, jak ho Azure Machine Learning podporuje pro modely hloubkového učení. 

V distribuovaném školení úlohy pro vývoj modelu je rozdělené a sdílené mezi více mini procesory nazývaných pracovní uzly. Tyto pracovní uzly pracují paralelně a urychlují školení modelů. Distribuované školení se dá použít pro tradiční modely ML, ale je vhodnější pro úlohy náročné na výpočetní a časově náročné, jako je [obsáhlý Learning](concept-deep-learning-vs-machine-learning.md) pro školení neuronovéch sítí. 

## <a name="deep-learning-and-distributed-training"></a>Obsáhlý Learning a distribuované školení 

Existují dva hlavní typy distribuovaných školení: [datová paralelismus](#data-parallelism) a [model paralelismus](#model-parallelism). Pro distribuované školení v modelech hloubkového učení podporuje [Azure Machine Learning SDK v Pythonu](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) integraci s oblíbenými architekturami, PyTorch a TensorFlow. Obě architektury využívají datovou paralelismuu pro distribuované školení a můžou využít [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) k optimalizaci rychlostech výpočtů. 

* [Distribuované školení pomocí PyTorch](how-to-train-pytorch.md#distributed-training)

* [Distribuované školení pomocí TensorFlow](how-to-train-tensorflow.md#distributed-training)

V případě modelů ML, které nevyžadují distribuované školení, najdete informace v tématu [výuka modelů pomocí Azure Machine Learning](concept-train-machine-learning-model.md#python-sdk) různých způsobech výuky modelů pomocí sady Python SDK.

## <a name="data-parallelism"></a>Datový paralelismus

Data paralelismus je nejjednodušší implementace dvou distribuovaných školicích postupů a je pro většinu případů použití dostačující.

V tomto přístupu jsou data rozdělená na oddíly, kde počet oddílů se rovná celkovému počtu dostupných uzlů ve výpočetním clusteru. Model je zkopírován do každého z těchto pracovních uzlů a každý pracovní proces funguje na vlastní podmnožině dat. Mějte na paměti, že každý uzel musí mít kapacitu pro podporu modelu, který je právě vyškolený, což je model, který je zcela přizpůsoben na každý uzel. Následující diagram poskytuje vizuální ukázku tohoto přístupu.

![Data-paralelismus – koncept – diagram](./media/concept-distributed-training/distributed-training.svg)

Každý uzel nezávisle vypočítá chyby mezi jeho předpovědi a ukázkami, které jsou označeny jako výstupy. V takovém případě každý uzel aktualizuje svůj model na základě chyb a musí sdělit všechny jeho změny v ostatních uzlech za účelem aktualizace odpovídajících modelů. To znamená, že pracovní uzly musí synchronizovat parametry modelu nebo přechody na konci dávkového výpočtu, aby bylo zajištěno, že budou školení konzistentního modelu. 

## <a name="model-parallelism"></a>Paralelismuing modelu

V modelu paralelismus, označovaný také jako paralelismue sítě, je model rozdělen do různých částí, které mohou běžet souběžně v různých uzlech, přičemž každý z nich bude spuštěn se stejnými daty. Škálovatelnost této metody závisí na stupních paralelismu tohoto algoritmu a je složitější pro implementaci než datový paralelismus. 

V modelu paralelismus musí pracovní uzly synchronizovat pouze sdílené parametry, obvykle jednou pro každý krok dopředu nebo zpětná propagace. Větší modely navíc nezáleží na tom, že každý uzel funguje v dílčí části modelu na stejných školicích datech.

## <a name="next-steps"></a>Další kroky

* Naučte se [používat výpočetní cíle pro školení modelů](how-to-set-up-training-targets.md) se sadou Python SDK.
* Technický příklad naleznete v tématu [scénář referenční architektury](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning).
* [Modely vlakových ml pomocí TensorFlow](how-to-train-tensorflow.md).
* [Modely vlakových ml pomocí PyTorch](how-to-train-pytorch.md). 