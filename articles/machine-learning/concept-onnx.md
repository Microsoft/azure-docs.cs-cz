---
title: 'ONNX: high-perf, cross platformy inference'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí Open Neural Network Exchange (ONNX) může pomoci optimalizovat odvození modelu strojového učení.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270170"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX a Azure Machine Learning: Vytváření a urychlení modelů ML

Zjistěte, jak pomocí [Open Neural Network Exchange](https://onnx.ai) (ONNX) může pomoci optimalizovat odvození modelu strojového učení. Odvození nebo vyhodnocování modelu je fáze, kde se nasazený model používá pro předpověď, nejčastěji na produkčních datech. 

Optimalizace modelů strojového učení pro odvození (nebo vyhodnocování modelu) je obtížné, protože potřebujete vyladit model a knihovnu odvození, abyste co nejlépe využít hardwarové možnosti. Problém se stává velmi těžké, pokud chcete získat optimální výkon na různých typech platforem (cloud / okraj, CPU / GPU, atd.), protože každý z nich má různé schopnosti a vlastnosti. Složitost se zvyšuje, pokud máte modely z různých architektur, které je třeba spustit na různých platformách. Je velmi časově náročné optimalizovat všechny různé kombinace rámců a hardwaru. Je potřeba řešení pro trénování jednou ve preferovaném rámci a běhat kdekoli na cloudu nebo hraně. To je místo, kde ONNX přichází.

Microsoft a komunita partnerů vytvořili ONNX jako otevřený standard pro reprezentaci modelů strojového učení. Modely z [mnoha rámců](https://onnx.ai/supported-tools) včetně TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet a MATLAB lze exportovat nebo převést do standardního formátu ONNX. Jakmile jsou modely ve formátu ONNX, mohou být spuštěny na různých platformách a zařízeních.

[ONNX Runtime](https://github.com/Microsoft/onnxruntime) je vysoce výkonný inference engine pro nasazení modelů ONNX do produkčního prostředí. Je optimalizovaný pro cloud i edge a funguje na Linuxu, Windows a Macu. Napsaný v Jazyce C++, má také C, Python a C# API. ONNX Runtime poskytuje podporu pro všechny specifikace ONNX-ML a také integruje s akcelerátory na různých hardwaru, jako je TensorRT na GPU NVidia.

Běhový čas ONNX se používá ve špičkových službách společnosti Microsoft, jako je Bing, Office a Cognitive Services. Zvýšení výkonu závisí na řadě faktorů, ale tyto služby společnosti Microsoft zaznamenaly __průměrný 2x nárůst výkonu procesoru__. ONNX Runtime se také používá jako součást Windows ML na stovkách milionů zařízení. Runtime můžete použít s Azure Machine Learning. Pomocí onnx runtime, můžete těžit z rozsáhlé optimalizace výroby třídy, testování a průběžná vylepšení.

[![Vývojový diagram ONNX znázorňující školení, převodníky a nasazení](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Získejte modely ONNX

Modely ONNX můžete získat několika způsoby:
+ Trénování nového modelu ONNX v Azure Machine Learning (viz příklady v dolní části tohoto článku)
+ Převést existující model z jiného formátu na ONNX (viz [výukové programy)](https://github.com/onnx/tutorials) 
+ Získejte předem vycvičený model ONNX ze [zoo modelu ONNX](https://github.com/onnx/models) (viz příklady v dolní části tohoto článku)
+ Generování přizpůsobeného modelu ONNX ze [služby Azure Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

Mnoho modelů včetně klasifikace obrázků, detekce objektů a zpracování textu může být reprezentováno jako modely ONNX. Některé modely však nemusí být možné úspěšně převést. Pokud narazíte na tuto situaci, prosím, soubor problém v GitHub příslušného převaděče, který jste použili. Můžete pokračovat v používání existujícího modelu formátu, dokud nebude problém vyřešen.

## <a name="deploy-onnx-models-in-azure"></a>Nasazení modelů ONNX v Azure

S Azure Machine Learning můžete nasadit, spravovat a monitorovat své modely ONNX. Pomocí standardního [pracovního postupu nasazení](concept-model-management-and-deployment.md) a onnx runtime, můžete vytvořit koncový bod REST hostované v cloudu. Podívejte se na příklad Jupyter poznámkové bloky na konci tohoto článku vyzkoušet sami. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Instalace a použití modulu RUNTime ONNX s Pythonem

Balíčky Pythonu pro ONNX Runtime jsou k dispozici na [PyPi.org](https://pypi.org) [(CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Před instalací si přečtěte [systémové požadavky.](https://github.com/Microsoft/onnxruntime#system-requirements) 

 Chcete-li nainstalovat modul RUNTIME ONNX pro Python, použijte jeden z následujících příkazů: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Chcete-li volat modul RUNTIME ONNX ve skriptu pythonu, použijte:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

Dokumentace doprovázející model obvykle informuje o vstupech a výstupech pro použití modelu. K zobrazení modelu můžete také použít vizualizační nástroj, například [Netron.](https://github.com/lutzroeder/Netron) Modul runtime ONNX také umožňuje dotazovat metadata, vstupy a výstupy modelu:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Chcete-li odvodit `run` váš model, použijte a předat v seznamu výstupů, které chcete vrátit (nechte prázdné, pokud chcete všechny z nich) a mapu vstupních hodnot. Výsledkem je seznam výstupů.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Úplný odkaz na rozhraní API pythonu najdete v [referenčních dokumentech onnx runtime](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Příklady

Podívejte se na příklad použití [azureml/deployment/onnx,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) například poznámkové bloky, které vytvářejí a nasazují modely ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Další informace

Přečtěte si více o ONNX nebo přispějte k projektu:
+ [Webové stránky projektu ONNX](https://onnx.ai)
+ [KÓD ONNX na GitHubu](https://github.com/onnx/onnx)

Přečtěte si další informace o onnx runtime nebo přispívat do projektu:
+ [Úložiště GitHub runtime ONNX](https://github.com/Microsoft/onnxruntime)


