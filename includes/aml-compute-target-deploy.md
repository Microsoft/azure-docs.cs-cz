---
title: zahrnout soubor
description: zahrnout soubor
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/30/2019
ms.openlocfilehash: 59e5adb5d1ee35b983c9552a2076a373561c0b44
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67168237"
---
| Cílové výpočetní prostředí | Využití | Podpora GPU | Podporu FPGA | Popis |
| ----- | ----- | ----- | ----- | ----- |
| [Místní&nbsp;webové&nbsp;služby](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Testování/ladění | Možná | &nbsp; | Vhodné pro testování a řešení potíží omezené. Hardwarová akcelerace, závisí na použití knihoven v místním systému.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Odvození v reálném čase |  [Ano](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Ano](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Vhodné pro nasazení v produkčním prostředí vysoce škálovatelné. Nabízí automatické škálování a krátké doby odezvy.  AKS je jedinou možností, které jsou k dispozici pro vizuální rozhraní. |
| [Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Testování a vývoj | &nbsp;  | &nbsp; | Dobrým nízkou škálovatelnost, založené na procesoru úlohy vyžadující < 48 GB paměti RAM |
| [Azure Machine Learning Compute](../articles/machine-learning/service/how-to-run-batch-predictions.md) | (Preview) Batch&nbsp;odvození | ano | &nbsp;  | Spusťte vyhodnocení na výpočetní prostředí služby batch. Podporuje virtuální počítače s normální a s nízkou prioritou. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | (Preview) IoT&nbsp;modulu |  &nbsp; | &nbsp; | Nasazení a poskytovat modelů ML na zařízeních IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | via IoT Edge |  &nbsp; | ano | Nasazení a poskytovat modelů ML na zařízeních IoT. |
