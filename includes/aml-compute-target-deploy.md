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
ms.date: 08/23/2019
ms.openlocfilehash: 1bf1202a635184ad7f0022cf5baa9d72e8547f14
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74549374"
---
| Cílový výpočetní objekt | Použití | Podpora GPU | Podpora FPGA | Popis |
| ----- | ----- | ----- | ----- | ----- |
| [Služba místní&nbsp;web&nbsp;](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Testování a ladění | &nbsp; | &nbsp; | Používá se pro omezené testování a řešení potíží. Hardwarová akcelerace závisí na použití knihoven v místním systému.
| [Služba Azure Machine Learning výpočetní instance&nbsp;službu Web&nbsp;](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Testování a ladění | &nbsp; | &nbsp; | Používá se pro omezené testování a řešení potíží.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Odvození v reálném čase |  [Ano](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md) (nasazení webové služby) | [Ano](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Použijte pro vysoce škálovatelná produkční nasazení. Poskytuje rychlou odezvu a automatické škálování nasazené služby. Automatické škálování clusteru není podporováno sadou Azure Machine Learning SDK. Pokud chcete změnit uzly v clusteru AKS, použijte uživatelské rozhraní pro cluster AKS v Azure Portal. AKS je jediná možnost dostupná pro návrháře. |
| [Azure Container Instances](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Testování a vývoj | &nbsp;  | &nbsp; | Používejte pro vysoce škálovatelné úlohy založené na procesoru, které vyžadují méně než 48 GB paměti RAM. |
| [Azure Machine Learning výpočetní clustery](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Tisk Odvození&nbsp;dávky | [Ano](../articles/machine-learning/service/how-to-run-batch-predictions.md) (kanál strojového učení) | &nbsp;  | Spusťte dávkové vyhodnocování pro výpočetní prostředky bez serveru. Podporuje virtuální počítače s normálním a nízkou prioritou. |
| [Azure Functions](../articles/machine-learning/service/how-to-deploy-functions.md) | Tisk Odvození v reálném čase | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Tisk Modul IoT&nbsp; |  &nbsp; | &nbsp; | Nasaďte a zajišťovat modely ML na zařízeních IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Prostřednictvím IoT Edge |  &nbsp; | Ano | Nasaďte a zajišťovat modely ML na zařízeních IoT. |

> [!NOTE]
> I když výpočetní prostředky, jako jsou místní, Azure Machine Learning výpočetní instance a Azure Machine Learning výpočetní clustery, podporují GPU pro školení a experimentování a __při nasazení jako webové služby__ na platformě Azure se používá GPU Služba Kubernetes
>
> Použití GPU pro odvození __při vyhodnocování s kanálem strojového učení__ je podporované jenom v Azure Machine Learning Compute.