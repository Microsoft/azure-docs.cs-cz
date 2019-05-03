---
title: Vysoce výkonná a pro různé platformy odvození s ONNX
titleSuffix: Azure Machine Learning service
description: Další informace o ONNX a modul Runtime ONNX pro urychlení modelů
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: 6bda1af8095e7026c79b7d8ffe45b39f6cbb3508
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028692"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX a Azure Machine Learning: Vytvoření a zrychlit modelů ML

Zjistěte, jak pomocí [otevřít Neural Network Exchange](https://onnx.ai) (ONNX) může pomoct optimalizovat vašich modelů strojového učení.

Optimalizace modelů strojového učení pro odvození je obtížné, protože je potřeba optimalizovat knihovna odvození provádět většinu funkcí hardwaru a modelu. Tento problém bude velmi obtížné, pokud chcete dosáhnout optimálního výkonu na různých platformách (cloud nebo edge, CPU/GPU, atd.), od každého jeden má různé možnosti a vlastnosti. Složitost se ještě zvýší, pokud máte modely z nejrůznějších platforem, které je potřeba spustit na různých platformách. Je velmi časově optimalizovat všechny různé kombinace platforem a hardwaru. Je potřeba řešení, aby se jednou v upřednostňované framework a běží kdekoli v cloudu i hraničních. To přichází ONNX.

Microsoft a komunitou partnerů vytvořili ONNX jako otevřený standard představující modelů strojového učení. Modely z [mnoho architektur](https://onnx.ai/supported-tools) včetně TensorFlow, PyTorch, SciKit-poučení, Keras, Chainer, MXNet a MATLAB můžete exportovat nebo převést do formátu ONNX standard. Jakmile tyto modely jsou ve formátu ONNX, lze spustit na různých platformách a zařízeních.

[Modul Runtime ONNX](https://github.com/Microsoft/onnxruntime) je vysoce výkonné odvození modul pro nasazení do produkčního prostředí modely ONNX. Je optimalizovaný pro cloudem a hraničními zařízeními a funguje na Linuxu, Windows a Mac. Napsané v C++, má také C, Python, a C# rozhraní API. Modul Runtime ONNX poskytuje podporu pro všechny specifikace ONNX ML a také integruje s využitím akcelerátorů v jiném hardwaru, jako je například TensorRT na grafických procesorů NVidia.

Modul Runtime ONNX se používá ve velkém měřítku službám Microsoftu jako jsou Bing, Office a služby Cognitive Services. Zvýšení výkonu jsou závislé na několika faktorech, ale viděli těchto služeb Microsoftu __průměrná 2 x zvýšení výkonu CPU__. Modul Runtime ONNX slouží také jako součást Windows ML na stovky milionů zařízení. Můžete použít modul runtime služby Azure Machine Learning. Pomocí modulu Runtime ONNX můžete využívat výhod optimalizací rozsáhlé úrovni produkce, testování a průběžnou vylepšení.

[![ONNX vývojový diagram znázorňující nasazení, školení a převaděče](media/concept-onnx/onnx.png) ](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Získat modely ONNX

Můžete získat modely ONNX několika způsoby:
+ Trénování modelu ONNX nové služby Azure Machine Learning (viz příklady v dolní části tohoto článku)
+ Převést existující model z jiného formátu ONNX (viz [kurzy](https://github.com/onnx/tutorials)) 
+ Získejte předem vytrénovaných modelu ONNX ze [Zoo modelu ONNX](https://github.com/onnx/models) (viz příklady v dolní části tohoto článku)
+ Generovat přizpůsobené modelu ONNX ze [Azure Custom Vision service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Mnoho modelů, včetně klasifikace obrázků, zjišťování objektů a zpracování textu může být reprezentován jako modely ONNX. Některé modely ale nemusí být možné úspěšně převeden. Pokud narazíte na situace, požádejte prosím problém na Githubu příslušných převaděč, který jste použili. Můžete pokračovat v používání existujícího modelu formátu, dokud tento problém řeší.

## <a name="deploy-onnx-models-in-azure"></a>Nasadit modely ONNX v Azure

Pomocí služby Azure Machine Learning můžete nasadit, spravovat a monitorovat využijte modely ONNX. Pomocí standardní [pracovní postup nasazení](concept-model-management-and-deployment.md) a ONNX Runtime můžete vytvořit koncový bod REST hostované v cloudu. Viz příklad poznámkové bloky Jupyter na konci tohoto článku můžete vyzkoušet sami. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Nainstalovat a používat modul Runtime ONNX pomocí Pythonu

Balíčky Pythonu pro ONNX Runtime jsou k dispozici na [PyPi.org](https://pypi.org) ([procesoru](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Přečtěte si prosím [požadavky na systém](https://github.com/Microsoft/onnxruntime#system-requirements) před instalací. 

 Instalace modulu Runtime ONNX pro Python, použijte jednu z následujících příkazů: 
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
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})   
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
+ [Úložiště GitHub se vzorovými ONNX modulu Runtime](https://github.com/Microsoft/onnxruntime)


