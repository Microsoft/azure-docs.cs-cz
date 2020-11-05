---
title: Machine Learning a AI s ONNX ve službě Azure SQL Edge
description: Machine Learning v Azure SQL Edge podporuje modely ve formátu Open neuronové Network Exchange (ONNX). ONNX je otevřený formát, který můžete použít k výměně modelů mezi různými architekturami a nástroji pro strojové učení.
keywords: nasazení Edge SQL
services: sql-edge
ms.service: sql-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: 5dc3d44ac4396897fd43831d51ee628bb06048cb
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392057"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-edge"></a>Machine Learning a AI s ONNX v SQL Edge

Machine Learning v Azure SQL Edge podporuje modely ve formátu [Open neuronové Network Exchange (ONNX)](https://onnx.ai/) . ONNX je otevřený formát, který můžete použít k výměně modelů mezi různými [architekturami a nástroji pro strojové učení](https://onnx.ai/supported-tools).

## <a name="overview"></a>Přehled

Pokud chcete odvodit modely strojového učení ve službě Azure SQL Edge, budete nejprve muset získat model. Může se jednat o předem vyškolený model nebo o vlastní model vyškolený s vámi zvoleným rozhraním. Azure SQL Edge podporuje formát ONNX a vy budete muset model převést do tohoto formátu. Nemusíte mít žádný vliv na přesnost modelu a až budete mít model ONNX, můžete model nasadit v Azure SQL Edge a použít [nativní hodnocení pomocí funkce prediktivní T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Získání modelů ONNX

Postup získání modelu ve formátu ONNX:

- **Služby pro vytváření modelů** : služby, jako je například [funkce automatizovaného Machine Learning v Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) a [Azure Custom Vision Service](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md) podporují přímý export výukového modelu ve formátu ONNX.

- [**Převod a export stávajících modelů**](https://github.com/onnx/tutorials#converting-to-onnx-format): několik platforem školení (např. [PyTorch](https://pytorch.org/docs/stable/onnx.html), chainer a Caffe2) podporují NATIVNÍ funkce exportu na ONNX, což vám umožní uložit vyškolený model do konkrétní verze formátu ONNX. Pro architektury, které nepodporují nativní export, jsou k dispozici samostatné balíčky ONNX Converter, které umožňují převod modelů vyškolených z různých architektur strojového učení do formátu ONNX.

     **Podporované architektury**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit – informace](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    Úplný seznam podporovaných rozhraní a příkladů najdete v tématu [Převod do formátu ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Omezení

V současné době nejsou všechny modely ONNX podporovány serverem Azure SQL Edge. Podpora je omezená na modely s **číselnými datovými typy** :

- [int a bigint](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [reálné číslo a float](/sql/t-sql/data-types/float-and-real-transact-sql).
  
Jiné číselné typy lze převést na podporované typy pomocí funkce [cast a Convert](/sql/t-sql/functions/cast-and-convert-transact-sql).

Vstupy modelu by měly být strukturované, aby každý vstup do modelu odpovídal jednomu sloupci v tabulce. Například pokud používáte PANDAS dataframe pro výuku modelu, pak každý vstup by měl být samostatným sloupcem pro model.

## <a name="next-steps"></a>Další kroky

- [Nasazení okraje SQL pomocí Azure Portal](deploy-portal.md)
- [Nasazení modelu ONNX na hraničním serveru Azure SQL ](deploy-onnx.md)