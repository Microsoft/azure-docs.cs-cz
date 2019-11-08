---
title: Machine Learning a AI s ONNX v Azure SQL Database Edge Preview | Microsoft Docs
description: Machine Learning v Azure SQL Database Edge Preview podporuje modely ve formátu Open neuronové Network Exchange (ONNX). ONNX je otevřený formát, který můžete použít k výměně modelů mezi různými architekturami a nástroji pro strojové učení.
keywords: nasazení Edge databáze SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/07/2019
ms.openlocfilehash: 976c849f9cb48e1c197f70d10e911216a6a7425c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822852"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Machine Learning a AI s ONNX v SQL Database Edge Preview

Machine Learning v Azure SQL Database Edge Preview podporuje modely ve formátu [Open neuronové Network Exchange (ONNX)](https://onnx.ai/) . ONNX je otevřený formát, který můžete použít k výměně modelů mezi různými [architekturami a nástroji pro strojové učení](https://onnx.ai/supported-tools).

## <a name="overview"></a>Přehled

Chcete-li odvodit modely strojového učení v Azure SQL Database Edge, budete nejprve muset získat model. Může se jednat o předem vyškolený model nebo o vlastní model vyškolený s vámi zvoleným rozhraním. Azure SQL Database Edge podporuje formát ONNX a budete muset model převést do tohoto formátu. Nemusíte mít žádný vliv na přesnost modelu a až budete mít model ONNX, můžete model nasadit v Azure SQL Database Edge a použít [nativní hodnocení pomocí funkce prediktivní T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Získání modelů ONNX

Existuje několik způsobů, jak můžete získat model ve formátu ONNX:

- [Model ONNX](https://github.com/onnx/models): obsahuje mnoho předem vyškolených modelů ONNX pro různé typy úloh, které je možné stáhnout a které jsou připravené k použití.

- [Nativní export z ml rozhraní pro školení](https://onnx.ai/supported-tools): několik architektur pro školení podporuje nativní funkce exportu do ONNX, což vám umožní uložit vyškolený model do konkrétní verze formátu ONNX, včetně [PyTorch](https://pytorch.org/docs/stable/onnx.html), chainer a Caffe2. Kromě toho, vytváření modelů, jako je [funkce automatizované Machine Learning v Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) a [Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) , poskytují export ONNX.

- [Převod stávajících modelů](https://github.com/onnx/tutorials#converting-to-onnx-format): pro architektury, které nepodporují nativní export, jsou k převodu modelů na formát ONNX samostatné balíčky. Příklady a kurzy najdete v tématu [převod na formát ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format). 

### <a name="supported-frameworks"></a>Podporované architektury

Převaděče ONNX umožňují převod modelů vyškolených z různých architektur strojového učení do formátu ONNX. Mezi oblíbené převaděče patří: 

* [PyTorch](http://pytorch.org/docs/master/onnx.html)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [Scikit-learn](https://github.com/onnx/sklearn-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)

Úplný seznam podporovaných rozhraní najdete v tématu [Převod do formátu ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Omezení

V současné době nejsou všechny modely ONNX podporovány Azure SQL Database Edge. Podpora je omezená na modely s **číselnými datovými typy**:

- [int a bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [reálné číslo a float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Jiné číselné typy lze převést na podporované typy pomocí funkce [cast a Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Vstupy modelu by měly být strukturované, aby každý vstup do modelu odpovídal jednomu sloupci v tabulce. Například pokud používáte PANDAS dataframe pro výuku modelu, pak každý vstup by měl být samostatným sloupcem pro model.

## <a name="next-steps"></a>Další kroky

- [Nasazení SQL Database Edge přes Azure Portal](deploy-portal.md)
- [Nasazení modelu ONNX v Azure SQL Database Edge Preview](deploy-onnx.md)
