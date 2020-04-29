---
title: 'ONNX: vysoce výkonná platforma pro odvozování více platforem'
titleSuffix: Azure Machine Learning
description: Naučte se používat Open neuronové Network Exchange (ONNX), které vám pomůžou optimalizovat odvození modelu Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 98aebb4733c2aa2a6d0b0217f1f437bcea1992e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79270170"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX a Azure Machine Learning: vytváření a zrychlení modelů ML

Naučte se používat [Open neuronové Network Exchange](https://onnx.ai) (ONNX), které vám pomůžou optimalizovat odvození modelu Machine Learning. Odvození modelu nebo Bodové hodnocení je fáze, ve které se nasazený model používá pro předpověď, nejčastěji pro produkční data. 

Optimalizace modelů strojového učení pro odvození (nebo vyhodnocování modelů) je obtížné, protože je potřeba ladit model a odvozenou knihovnu a využít tak většinu hardwarových funkcí. Pokud chcete získat optimální výkon na různých druzích platforem (Cloud, Edge, CPU/GPU atd.), bude problém velmi tvrdý. vzhledem k tomu, že každý z nich má různé možnosti a charakteristiky Složitost se zvyšuje, pokud máte modely z nejrůznějších architektur, které potřebují běžet na různých platformách. Optimalizace všech různých kombinací rozhraní a hardwaru je velmi časově náročná. Řešení, které se má ve vašem preferovaném rozhraní naučit jednou a spustit kdekoli na cloudu nebo na hraničních zařízeních. Zde je místo, kde ONNX přichází.

Společnost Microsoft a komunitní partneři vytvořili ONNX jako open standard pro reprezentaci modelů strojového učení. Modely z [mnoha platforem](https://onnx.ai/supported-tools) , jako jsou TensorFlow, PyTorch, SciKit-učení, Keras, chainer, MXNET a MATLAB, se dají exportovat nebo převést na standardní formát ONNX. Jakmile jsou modely ve formátu ONNX, dají se spouštět na různých platformách a zařízeních.

[ONNX runtime](https://github.com/Microsoft/onnxruntime) je vysoce výkonný modul odvození pro nasazení modelů ONNX do produkčního prostředí. Je optimalizovaný pro Cloud i Edge a funguje v systémech Linux, Windows a Mac. Napsaný v jazyce C++ obsahuje také rozhraní API jazyka C, Python a C#. ONNX runtime poskytuje podporu pro veškerou specifikaci ONNX-ML a integruje se s akcelerátory na jiném hardwaru, jako je například TensorRT na grafickém rozhraní NVidia.

Modul runtime ONNX se používá ve vysoce škálovatelných službách Microsoftu, jako je Bing, Office a Cognitive Services. Zvýšení výkonu závisí na několika faktorech, ale tyto služby společnosti Microsoft viděli __průměrně dvojnásobný výkon na procesoru__. Modul runtime ONNX se používá také jako součást Windows ML na stovkách milionů zařízení. Modul runtime lze použít s Azure Machine Learning. Pomocí modulu runtime ONNX můžete využívat rozsáhlé Optimalizace produkčních funkcí, testování a průběžná vylepšení.

[![ONNX Flow diagram znázorňující školení, převaděče a nasazení](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Získání modelů ONNX

Modely ONNX můžete získat několika způsoby:
+ Výuka nového modelu ONNX v Azure Machine Learning (podívejte se na příklady na konci tohoto článku)
+ Převést existující model z jiného formátu na ONNX (viz [kurzy](https://github.com/onnx/tutorials)) 
+ Získejte předem vyškolený model ONNX z [modelu ONNX](https://github.com/onnx/models) (viz příklady v dolní části tohoto článku).
+ Generování přizpůsobeného modelu ONNX ze [služby Azure Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Mnoho modelů, včetně klasifikace obrázků, detekce objektů a zpracování textu, lze znázornit jako ONNX modely. Některé modely ale nemusí být možné úspěšně převést. Pokud narazíte na tuto situaci, uveďte problém na GitHubu příslušného převaděče, který jste použili. Existující model formátu můžete dál používat, dokud se problém nevyřeší.

## <a name="deploy-onnx-models-in-azure"></a>Nasazení modelů ONNX v Azure

Pomocí Azure Machine Learning můžete nasazovat, spravovat a monitorovat modely ONNX. Pomocí standardního [pracovního postupu nasazení](concept-model-management-and-deployment.md) a modulu runtime ONNX můžete vytvořit koncový bod REST, který je hostovaný v cloudu. Další informace najdete v části Jupyter poznámkové bloky na konci tohoto článku. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Instalace a použití modulu runtime ONNX v Pythonu

Balíčky Pythonu pro modul runtime ONNX jsou k dispozici na [PyPI.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Před instalací si prosím přečtěte [požadavky na systém](https://github.com/Microsoft/onnxruntime#system-requirements) . 

 Pokud chcete nainstalovat modul runtime ONNX pro Python, použijte jeden z následujících příkazů: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Pokud chcete volat ONNX runtime ve skriptu Pythonu, použijte:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

Dokumentace doprovázející model obvykle oznamuje vstupy a výstupy pro používání modelu. K zobrazení modelu můžete použít také nástroj pro vizualizaci, jako je [Netron](https://github.com/lutzroeder/Netron) . ONNX runtime také umožňuje dotazovat se na metadata modelu, vstupy a výstupy:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Chcete-li odvozovat svůj model `run` , použijte a předejte seznam výstupů, které chcete vrátit (ponechte prázdné, pokud chcete všechny) a mapu vstupních hodnot. Výsledkem je seznam výstupů.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Kompletní referenční informace k rozhraní Python API najdete v [referenčních dokumentech ONNX za běhu](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Příklady

Příklady poznámkových bloků, které vytvářejí a nasazují modely ONNX, najdete v tématu [How to-use-AzureML/Deployment/Onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) .

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Další informace

Další informace o ONNX nebo přispívání do projektu:
+ [Web projektu ONNX](https://onnx.ai)
+ [ONNX kód na GitHubu](https://github.com/onnx/onnx)

Další informace o modulu runtime ONNX nebo o přispívání do projektu:
+ [Úložiště GitHub pro ONNX runtime](https://github.com/Microsoft/onnxruntime)


