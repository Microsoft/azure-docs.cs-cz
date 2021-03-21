---
title: 'ONNX: vysoce výkonná platforma pro odvozování více platforem'
titleSuffix: Azure Machine Learning
description: Naučte se používat Open neuronové Network Exchange (ONNX), které vám pomůžou optimalizovat odvození modelu Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: prasantp
author: prasanthpul
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: 5a1ed6c43e4d3a96e0e19d7be50e1e16bcab780e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98071234"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX a Azure Machine Learning: vytváření a zrychlení modelů ML

Naučte se používat [Open neuronové Network Exchange](https://onnx.ai) (ONNX), které vám pomůžou optimalizovat odvození modelu Machine Learning. Odvození modelu nebo Bodové hodnocení je fáze, ve které se nasazený model používá pro předpověď, nejčastěji pro produkční data. 

Optimalizace modelů strojového učení pro odvození (nebo vyhodnocování modelů) je obtížné, protože je potřeba ladit model a odvozenou knihovnu a využít tak většinu hardwarových funkcí. Pokud chcete získat optimální výkon na různých druzích platforem (Cloud, Edge, CPU/GPU atd.), bude problém velmi tvrdý. vzhledem k tomu, že každý z nich má různé možnosti a charakteristiky Složitost se zvyšuje, pokud máte modely z nejrůznějších architektur, které potřebují běžet na různých platformách. Optimalizace všech různých kombinací rozhraní a hardwaru je velmi časově náročná. Řešení, které se má ve vašem preferovaném rozhraní naučit jednou a spustit kdekoli na cloudu nebo na hraničních zařízeních. Zde je místo, kde ONNX přichází.

Společnost Microsoft a komunitní partneři vytvořili ONNX jako open standard pro reprezentaci modelů strojového učení. Modely z [mnoha platforem](https://onnx.ai/supported-tools) , jako jsou TensorFlow, PyTorch, SciKit-učení, Keras, chainer, MXNET, MATLAB a SparkML, se dají exportovat nebo převést na standardní formát ONNX. Jakmile jsou modely ve formátu ONNX, dají se spouštět na různých platformách a zařízeních.

[ONNX runtime](https://onnxruntime.ai) je vysoce výkonný modul odvození pro nasazení modelů ONNX do produkčního prostředí. Je optimalizovaný pro Cloud i Edge a funguje v systémech Linux, Windows a Mac. Napsaný v jazyce C++ obsahuje také rozhraní API jazyka C, Python, C#, Java a JavaScriptu (Node.js) pro použití v různých prostředích. ONNX runtime podporuje modely DNN a tradiční ML a integruje se s akcelerátory na jiném hardwaru, jako je TensorRT v GPU GPU, OpenVINO na procesorech Intel, DirectML ve Windows a dalších. Pomocí modulu runtime ONNX můžete využívat rozsáhlé Optimalizace produkčních funkcí, testování a průběžná vylepšení.

ONNX runtime se používá v vysoce škálovatelných službách Microsoftu, jako je Bing, Office a Azure Cognitive Services. Zvýšení výkonu závisí na několika faktorech, ale tyto služby společnosti Microsoft viděli __průměrně dvojnásobný výkon na procesoru__. Kromě Azure Machine Learning služeb běží modul runtime ONNX také v dalších produktech, které podporují Machine Learning úlohy, včetně:
+ Windows: modul runtime je integrovaný do Windows jako součást [windows Machine Learning](/windows/ai/windows-ml/) a běží na stovkách milionů zařízení. 
+ Produktová řada Azure SQL: Spusťte nativní hodnocení dat ve [službě Azure SQL Edge](../azure-sql-edge/onnx-overview.md) a [spravované instanci Azure SQL](../azure-sql/managed-instance/machine-learning-services-overview.md).
+ ML.NET: [spouštějte modely ONNX v ml.NET](/dotnet/machine-learning/tutorials/object-detection-onnx).


[![ONNX Flow diagram znázorňující školení, převaděče a nasazení](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Získání modelů ONNX

Modely ONNX můžete získat několika způsoby:
+ Výuka nového modelu ONNX v Azure Machine Learning (podívejte se na příklady na konci tohoto článku) nebo pomocí [automatických funkcí Machine Learning](concept-automated-ml.md#automl--onnx)
+ Převést existující model z jiného formátu na ONNX (viz [kurzy](https://github.com/onnx/tutorials)) 
+ Získání předem připraveného modelu ONNX z modelu ONNX ve službě | [zoologického](https://github.com/onnx/models) navýšení
+ Generování přizpůsobeného modelu ONNX ze [služby Azure Custom Vision](../cognitive-services/custom-vision-service/index.yml) 

Mnoho modelů, včetně klasifikace obrázků, detekce objektů a zpracování textu, lze znázornit jako ONNX modely. Pokud narazíte na problém s modelem, který nelze úspěšně převést, uveďte problém na GitHubu příslušného převaděče, který jste použili. Existující model formátu můžete dál používat, dokud se problém nevyřeší.

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

Chcete-li odvozovat svůj model, použijte `run` a předejte seznam výstupů, které chcete vrátit (ponechte prázdné, pokud chcete všechny) a mapu vstupních hodnot. Výsledkem je seznam výstupů.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Kompletní referenční informace k rozhraní Python API najdete v [referenčních dokumentech ONNX za běhu](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Příklady
Příklady poznámkových bloků Pythonu, které vytvářejí a nasazují modely ONNX, najdete v tématu [How to-use-AzureML/Deployment/Onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) .

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

Ukázky pro použití v jiných jazycích najdete v [GitHubu ONNX runtime](https://github.com/microsoft/onnxruntime/tree/master/samples).

## <a name="more-info"></a>Další informace

Další informace o **ONNX** nebo přispívání do projektu:
+ [Web projektu ONNX](https://onnx.ai)
+ [ONNX kód na GitHubu](https://github.com/onnx/onnx)

Další informace o **modulu runtime ONNX** nebo o přispívání do projektu:
+ [Web projektu modulu runtime ONNX](https://onnxruntime.ai)
+ [Úložiště GitHub pro ONNX runtime](https://github.com/Microsoft/onnxruntime)