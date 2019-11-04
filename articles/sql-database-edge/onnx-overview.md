---
title: Machine Learning a AI s ONNX v Azure SQL Database Edge Preview | Microsoft Docs
description: Machine Learning v Azure SQL Database Edge Preview podporuje modely ve formátu Open neuronové Network Exchange (ONNX). ONNX je otevřený formát, který můžete použít k výměně modelů mezi různými architekturami a nástroji pro strojové učení.
keywords: nasazení Edge databáze SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/04/2019
ms.openlocfilehash: c4c87f7f6f8735c9a50c61b0e083c77b915e0d98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514014"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Machine Learning a AI s ONNX v SQL Database Edge Preview

Machine Learning v Azure SQL Database Edge Preview podporuje modely ve formátu [Open neuronové Network Exchange (ONNX)](https://onnx.ai/) . ONNX je otevřený formát, který můžete použít k výměně modelů mezi různými [architekturami a nástroji pro strojové učení](https://onnx.ai/supported-tools).

## <a name="supported-tool-kits"></a>Podporované sady nástrojů

ONNXMLTools vám umožňuje převést modely z různých sad nástrojů Machine Learning na model ONNX. V současné době jsou pro číselné datové typy a jednotlivé vstupy v jednom sloupci podporovány následující sady nástrojů:

* [scikit-learn](https://github.com/onnx/sklearn-onnx)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)
* [Spark ML (experimentální)](https://github.com/onnx/onnxmltools/tree/master/onnxmltools/convert/sparkml)
* [LightGBM](https://github.com/onnx/onnxmltools)
* [libsvm](https://github.com/onnx/onnxmltools)
* [XGBoost](https://github.com/onnx/onnxmltools)

## <a name="get-onnx-models"></a>Získání modelů ONNX

Existuje několik způsobů, jak můžete získat model ve formátu ONNX:

- [Model ONNX](https://github.com/onnx/models): obsahuje několik předem vyškolených modelů ONNX pro různé typy úloh. Můžete stáhnout a použít verze, které podporuje Windows Machine Learning.

- [Nativní export z architektur pro školení ve službě Machine Learning](https://onnx.ai/supported-tools): několik architektur pro školení podporuje nativní funkce exportu do ONNX, což vám umožní uložit vyškolený model do konkrétní verze formátu ONNX. Například chainer, Caffee2 a PyTorch. Kromě toho služby jako [Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning-service/) a [Azure Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) také poskytují nativní export ONNX.

  - Další informace o tom, jak v cloudu pomocí Custom Vision naučit a exportovat model ONNX, najdete v tématu [kurz: použití modelu ONNX z Custom Vision s Windows ml (Preview)](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/custom-vision-onnx-windows-ml).

- [Převod stávajících modelů pomocí WinMLTools](https://docs.microsoft.com/windows/ai/windows-ml/convert-model-winmltools): Tento balíček Pythonu umožňuje modelům převod z několika formátů rozhraní pro školení na ONNX. Můžete určit, na kterou verzi ONNX chcete model převést, v závislosti na tom, která sestavení cílí na vaše aplikace. Pokud nejste obeznámeni s Pythonem, můžete k převodu vašich modelů použít [řídicí panel založený na uživatelském rozhraní Windows Machine Learning](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLDashboard) .

> [!IMPORTANT]
> V hraničních zařízeních Azure SQL Database nejsou podporovány všechny verze ONNX. V současné době je podporována pouze předpověď číselných datových typů prostřednictvím modelu ONNX.

Jakmile máte model ONNX, můžete model nasadit v Azure SQL Database Edge. Pak můžete použít [nativní hodnocení pomocí funkce prediktivní T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="limitations"></a>Omezení

V současné době je tato podpora omezená na modely s **číselnými datovými typy**:

- [int a bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [reálné číslo a float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Jiné číselné typy lze převést na podporované typy pomocí funkce CAST a převod [přetypování a převod](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Vstupy modelu by měly být strukturované, aby každý vstup do modelu odpovídal jednomu sloupci v tabulce. Například pokud používáte PANDAS dataframe pro výuku modelu, pak každý vstup by měl být samostatným sloupcem pro model.

## <a name="next-steps"></a>Další kroky

- [Nasazení SQL Database Edge přes Azure Portal](deploy-portal.md)
- [Nasazení modelu ONNX v Azure SQL Database Edge Preview](deploy-onnx.md)