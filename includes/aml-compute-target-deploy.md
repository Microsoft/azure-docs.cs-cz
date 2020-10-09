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
ms.openlocfilehash: 944b96e7726f2b2becd5960a17a89c00d00c878a
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91841938"
---
Cílový výpočetní výkon, který používáte k hostování vašeho modelu, bude mít vliv na náklady a dostupnost nasazeného koncového bodu. Pomocí této tabulky můžete zvolit vhodný cíl služby Compute.

| Cílový výpočetní objekt | Použití | Podpora GPU | Podpora FPGA | Popis |
| ----- | ----- | ----- | ----- | ----- |
| [Místní &nbsp; Webová &nbsp; Služba](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testování a ladění | &nbsp; | &nbsp; | Používá se pro omezené testování a řešení potíží. Hardwarová akcelerace závisí na použití knihoven v místním systému.
| [&nbsp;Webová služba Azure Machine Learning COMPUTE instance &nbsp;](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testování a ladění | &nbsp; | &nbsp; | Používá se pro omezené testování a řešení potíží.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Odvození v reálném čase |  [Ano](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (nasazení webové služby) | [Ano](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Použijte pro vysoce škálovatelná produkční nasazení. Poskytuje rychlou odezvu a automatické škálování nasazené služby. Automatické škálování clusteru není podporováno sadou Azure Machine Learning SDK. Pokud chcete změnit uzly v clusteru AKS, použijte uživatelské rozhraní pro cluster AKS v Azure Portal. AKS je jediná možnost dostupná pro návrháře. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Testování a vývoj | &nbsp;  | &nbsp; | Používejte pro vysoce škálovatelné úlohy založené na procesoru, které vyžadují méně než 48 GB paměti RAM. |
| [Výpočetní clustery Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | &nbsp;Odvození dávky | [Ano](../articles/machine-learning/how-to-use-parallel-run-step.md) (kanál strojového učení) | &nbsp;  | Spusťte dávkové vyhodnocování pro výpočetní prostředky bez serveru. Podporuje virtuální počítače s normálním a nízkou prioritou. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | Tisk Odvození v reálném čase | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | Tisk &nbsp;Modul IoT |  &nbsp; | &nbsp; | Nasaďte a zajišťovat modely strojového učení na zařízeních IoT. |
| [Azure Data Box Edge](../articles/databox-online/azure-stack-edge-overview.md)   | Přes IoT Edge |  &nbsp; | Ano | Nasaďte a zajišťovat modely strojového učení na zařízeních IoT. |

> [!NOTE]
> I když výpočetní prostředky, jako jsou místní, Azure Machine Learning výpočetní prostředí a Azure Machine Learning výpočetní clustery, podporují GPU pro školení a experimentování, při použití GPU pro odvození _při nasazení jako webové služby_ se podporuje jenom AKS.
>
> Použití GPU pro odvození _při vyhodnocování s kanálem strojového učení_ je podporované jenom v Azure Machine Learning Compute.

> [!NOTE]
> * Instance kontejnerů jsou vhodné jenom pro malé modely, které mají velikost menší než 1 GB.
> * Používejte clustery AKS s jedním uzlem pro vývoj a testování větších modelů.