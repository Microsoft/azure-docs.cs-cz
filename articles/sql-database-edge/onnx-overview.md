---
title: Strojové učení a umělá ai s ONNX v Azure SQL Database Edge Preview | Dokumenty společnosti Microsoft
description: Strojové učení v Azure SQL Database Edge Preview podporuje modely ve formátu OPEN Neural Network Exchange (ONNX). ONNX je otevřený formát, který můžete použít k výměně modelů mezi různými architekturami strojového učení a nástroji.
keywords: nasazení okraje databáze SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: 7813a08b6b18e517b81e8c4bfac660d198eba7f7
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366278"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Strojové učení a umělá ai s ONNX v SQL Database Edge Preview

Strojové učení v Azure SQL Database Edge Preview podporuje modely ve formátu [OPEN Neural Network Exchange (ONNX).](https://onnx.ai/) ONNX je otevřený formát, který můžete použít k výměně modelů mezi různými [architekturami strojového učení a nástroji](https://onnx.ai/supported-tools).

## <a name="overview"></a>Přehled

Chcete-li odvodit modely strojového učení v Azure SQL Database Edge, budete muset nejprve získat model. To může být předem trénovaný model nebo vlastní model trénovaný s rámcem volby. Azure SQL Database Edge podporuje formát ONNX a budete muset převést model do tohoto formátu. Přesnost modelu by neměla mít žádný vliv a jakmile budete mít model ONNX, můžete nasadit model v Azure SQL Database Edge a použít [nativní vyhodnocování pomocí funkce PREDICT T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Získejte modely ONNX

Chcete-li získat model ve formátu ONNX:

- **Model Stavební služby:** Služby, jako je [například funkce automatizovaného strojového učení v Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) a azure vlastní vize [služby](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) podpory přímo exportu je trénovaný model ve formátu ONNX.

- [**Převod a/nebo export existujících modelů**](https://github.com/onnx/tutorials#converting-to-onnx-format): Několik [PyTorch](https://pytorch.org/docs/stable/onnx.html)trénovacích rámců (např. Pro architektury, které nepodporují nativní export, existují samostatné instalační balíčky ONNX Converter, které umožňují převést modely trénované z různých architektur strojového učení do formátu ONNX.

     **Podporované architektury**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tenzorový průtok](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-učit](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    Úplný seznam podporovaných architektur a příklady naleznete v [tématu Převod na formát ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Omezení

V současné době nejsou všechny modely ONNX podporované Azure SQL Database Edge. Podpora je omezena na modely s **číselnými datovými typy**:

- [int a bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [skutečné a plovák](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Jiné číselné typy lze převést na podporované typy pomocí [cast a convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Vstupy modelu by měly být strukturovány tak, aby každý vstup do modelu odpovídal jednomu sloupci v tabulce. Například pokud používáte pandas datový rámec trénování modelu, pak každý vstup by měl být samostatný sloupec modelu.

## <a name="next-steps"></a>Další kroky

- [Nasazení okraje databáze SQL prostřednictvím portálu Azure](deploy-portal.md)
- [Nasazení modelu ONNX v azure SQL database edge preview](deploy-onnx.md)
