---
title: Vysoce výkonná platforma pro odvození více platforem pomocí ONNX
titleSuffix: Azure Machine Learning service
description: Přečtěte si o ONNX a modulu runtime ONNX pro zrychlení modelů.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 1d97e2d2698c482b75f037dbd8cde1027c472125
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534879"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX a Azure Machine Learning: Vytváření a zrychlení modelů ML

Naučte se používat [Open neuronové Network Exchange](https://onnx.ai) (ONNX), které vám pomůžou optimalizovat odvození modelu Machine Learning. Odvození modelu nebo Bodové hodnocení je fáze, ve které se nasazený model používá pro předpověď, nejčastěji pro produkční data. 

Optimalizace modelů strojového učení pro odvození (nebo vyhodnocování modelů) je obtížné, protože je potřeba ladit model a odvozenou knihovnu a využít tak většinu hardwarových funkcí. Pokud chcete získat optimální výkon na různých druzích platforem (Cloud, Edge, CPU/GPU atd.), bude problém velmi tvrdý. vzhledem k tomu, že každý z nich má různé možnosti a charakteristiky Složitost se zvyšuje, pokud máte modely z nejrůznějších architektur, které potřebují běžet na různých platformách. Optimalizace všech různých kombinací rozhraní a hardwaru je velmi časově náročná. Řešení, které se má ve vašem preferovaném rozhraní naučit jednou a spustit kdekoli na cloudu nebo na hraničních zařízeních. Zde je místo, kde ONNX přichází.

Společnost Microsoft a komunitní partneři vytvořili ONNX jako open standard pro reprezentaci modelů strojového učení. Modely z [mnoha platforem](https://onnx.ai/supported-tools) , jako jsou TensorFlow, PyTorch, SciKit-učení, Keras, chainer, MXNET a MATLAB, se dají exportovat nebo převést na standardní formát ONNX. Jakmile jsou modely ve formátu ONNX, dají se spouštět na různých platformách a zařízeních.

[ONNX runtime](https://github.com/Microsoft/onnxruntime) je vysoce výkonný modul odvození pro nasazení modelů ONNX do produkčního prostředí. Je optimalizovaný pro Cloud i Edge a funguje v systémech Linux, Windows a Mac. Napsaný C++v, má také jazyk C, Python a C# rozhraní API. ONNX runtime poskytuje podporu pro veškerou specifikaci ONNX-ML a integruje se s akcelerátory na jiném hardwaru, jako je například TensorRT na grafickém rozhraní NVidia.

Modul runtime ONNX se používá ve vysoce škálovatelných službách Microsoftu, jako je Bing, Office a Cognitive Services. Zvýšení výkonu závisí na několika faktorech, ale tyto služby společnosti Microsoft viděli průměrně __dvojnásobný výkon na procesoru__. Modul runtime ONNX se používá také jako součást Windows ML na stovkách milionů zařízení. Můžete použít modul runtime s Azure Machine Learning Services. Pomocí modulu runtime ONNX můžete využívat rozsáhlé Optimalizace produkčních funkcí, testování a průběžná vylepšení.

[![ONNX Flow diagram znázorňující školení, převaděče a nasazení](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Získat modely ONNX

Můžete získat modely ONNX několika způsoby:
+ Výuka nového modelu ONNX ve službě Azure Machine Learning (podívejte se na příklady na konci tohoto článku)
+ Převést existující model z jiného formátu na ONNX (viz [kurzy](https://github.com/onnx/tutorials)) 
+ Získejte předem vyškolený model ONNX z [modelu ONNX](https://github.com/onnx/models) (viz příklady v dolní části tohoto článku).
+ Generovat přizpůsobené modelu ONNX ze [Azure Custom Vision service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Mnoho modelů, včetně klasifikace obrázků, detekce objektů a zpracování textu, lze znázornit jako ONNX modely. Některé modely ale nemusí být možné úspěšně převést. Pokud narazíte na tuto situaci, uveďte problém na GitHubu příslušného převaděče, který jste použili. Existující model formátu můžete dál používat, dokud se problém nevyřeší.

## <a name="deploy-onnx-models-in-azure"></a>Nasadit modely ONNX v Azure

Pomocí služby Azure Machine Learning můžete nasadit, spravovat a monitorovat využijte modely ONNX. Pomocí standardní [pracovní postup nasazení](concept-model-management-and-deployment.md) a ONNX Runtime můžete vytvořit koncový bod REST hostované v cloudu. Další informace najdete v části Jupyter poznámkové bloky na konci tohoto článku. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Instalace a použití modulu runtime ONNX v Pythonu

Balíčky Pythonu pro modul runtime ONNX jsou k dispozici na [PyPI.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Před instalací si prosím přečtěte [požadavky na systém](https://github.com/Microsoft/onnxruntime#system-requirements) . 

 Pokud chcete nainstalovat modul runtime ONNX pro Python, použijte jeden z následujících příkazů: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Pokud chcete volat ONNX Runtime ve svém skriptu Pythonu, použijte:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

Dokumentaci modelu obvykle zjistíte, vstupy a výstupy pro použití modelu. Můžete také použít vizualizačního nástroje jako [Netron](https://github.com/lutzroeder/Netron) zobrazíte modelu. Modul Runtime ONNX také umožňuje dotazování metadat modelu, vstupy a výstupy:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

K odvození váš model, použijte `run` a předejte mu seznam výstupy požadujete vrátil (ponechte prázdné Pokud chcete všechny z nich) a mapu vstupních hodnot. Výsledkem je seznam výstupy.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Úplné referenční rozhraní Python API najdete v článku [ONNX Runtime referenční dokumenty](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Příklady

Zobrazit [postupy-k-použití azureml/nasazení/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) například poznámkových bloků, které vytvářet a nasazovat modely ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Další informace

Další informace o ONNX nebo přispět k projektu:
+ [ONNX projekt webu](https://onnx.ai)
+ [ONNX kódu na Githubu](https://github.com/onnx/onnx)

Další informace o modulu Runtime ONNX nebo přispět k projektu:
+ [Úložiště GitHub pro ONNX runtime](https://github.com/Microsoft/onnxruntime)


