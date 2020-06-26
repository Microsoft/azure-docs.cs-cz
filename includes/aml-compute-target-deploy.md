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
ms.date: 06/25/2020
ms.openlocfilehash: 82234719320f1ac707147c7c8393c0464956dd99
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378437"
---
| Cílový výpočetní objekt | Použití | Podpora GPU | Podpora FPGA | Description |
| ----- | ----- | ----- | ----- | ----- |
| [Místní &nbsp; Webová &nbsp; Služba](../articles/machine-learning/how-to-deploy-and-where.md#local) | Testování a ladění | &nbsp; | &nbsp; | Používá se pro omezené testování a řešení potíží. Hardwarová akcelerace závisí na použití knihoven v místním systému.
| [&nbsp;Webová služba Azure Machine Learning COMPUTE instance &nbsp;](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Testování a ladění | &nbsp; | &nbsp; | Používá se pro omezené testování a řešení potíží.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Odvození v reálném čase |  [Ano](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (nasazení webové služby) | [Ano](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Použijte pro vysoce škálovatelná produkční nasazení. Poskytuje rychlou odezvu a automatické škálování nasazené služby. Automatické škálování clusteru není podporováno sadou Azure Machine Learning SDK. Pokud chcete změnit uzly v clusteru AKS, použijte uživatelské rozhraní pro cluster AKS v Azure Portal. AKS je jediná možnost dostupná pro návrháře. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Testování a vývoj | &nbsp;  | &nbsp; | Používejte pro vysoce škálovatelné úlohy založené na procesoru, které vyžadují méně než 48 GB paměti RAM. |
| [Výpočetní clustery Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | &nbsp;Odvození dávky | [Ano](../articles/machine-learning/how-to-use-parallel-run-step.md) (kanál strojového učení) | &nbsp;  | Spusťte dávkové vyhodnocování pro výpočetní prostředky bez serveru. Podporuje virtuální počítače s normálním a nízkou prioritou. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | Tisk Odvození v reálném čase | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | Tisk &nbsp;Modul IoT |  &nbsp; | &nbsp; | Nasaďte a zajišťovat modely ML na zařízeních IoT. |
| [Azure Data Box Edge](../articles/databox-online/azure-stack-edge-overview.md)   | Prostřednictvím IoT Edge |  &nbsp; | Yes | Nasaďte a zajišťovat modely ML na zařízeních IoT. |

> [!NOTE]
> I když výpočetní cíle, jako je místní, Azure Machine Learning výpočetní instance a Azure Machine Learning výpočetní clustery, podporují GPU pro školení a experimentování a __při nasazení jako webové služby v případě__ , že je tato služba dostupná jenom ve službě Azure Kubernetes, se používá GPU pro odvození.
>
> Použití GPU pro odvození __při vyhodnocování s kanálem strojového učení__ je podporované jenom v Azure Machine Learning Compute.