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
ms.openlocfilehash: 744ac9f8526b7d65709d3627a5f90b31d234b2cd
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74009081"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Machine Learning a AI s ONNX v SQL Database Edge Preview

Machine Learning v Azure SQL Database Edge Preview podporuje modely ve formátu [Open neuronové Network Exchange (ONNX)](https://onnx.ai/) . ONNX je otevřený formát, který můžete použít k výměně modelů mezi různými [architekturami a nástroji pro strojové učení](https://onnx.ai/supported-tools).

## <a name="overview"></a>Přehled

Chcete-li odvodit modely strojového učení v Azure SQL Database Edge, budete nejprve muset získat model. Může se jednat o předem vyškolený model nebo o vlastní model vyškolený s vámi zvoleným rozhraním. Azure SQL Database Edge podporuje formát ONNX a budete muset model převést do tohoto formátu. Nemusíte mít žádný vliv na přesnost modelu a až budete mít model ONNX, můžete model nasadit v Azure SQL Database Edge a použít [nativní hodnocení pomocí funkce prediktivní T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Získat modely ONNX

Postup získání modelu ve formátu ONNX:

- **Služby pro vytváření modelů**: služby, jako je například [funkce automatizovaného Machine Learning v Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) a [Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) podporují přímý export výukového modelu ve formátu ONNX.

- [**Převod a export stávajících modelů**](https://github.com/onnx/tutorials#converting-to-onnx-format): několik platforem školení (např. [PyTorch](https://pytorch.org/docs/stable/onnx.html), chainer a Caffe2) podporují NATIVNÍ funkce exportu na ONNX, což vám umožní uložit vyškolený model do konkrétní verze formátu ONNX. Pro architektury, které nepodporují nativní export, jsou k dispozici samostatné balíčky ONNX Converter, které umožňují převod modelů vyškolených z různých architektur strojového učení do formátu ONNX.

     **Podporované architektury**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    Úplný seznam podporovaných rozhraní a příkladů najdete v tématu [Převod do formátu ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Omezení

V současné době nejsou všechny modely ONNX podporovány Azure SQL Database Edge. Podpora je omezená na modely s **číselnými datovými typy**:

- [int a bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [reálné číslo a float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Jiné číselné typy lze převést na podporované typy pomocí funkce [cast a Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Vstupy modelu by měly být strukturované, aby každý vstup do modelu odpovídal jednomu sloupci v tabulce. Například pokud používáte PANDAS dataframe pro výuku modelu, pak každý vstup by měl být samostatným sloupcem pro model.

## <a name="next-steps"></a>Další kroky

- [Nasazení SQL Database Edge přes Azure Portal](deploy-portal.md)
- [Nasazení modelu ONNX v Azure SQL Database Edge Preview](deploy-onnx.md)
