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
ms.openlocfilehash: 9eedc0c3044717360494b222c88d73b3c8999e94
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82587853"
---
| Cílový výpočetní objekt | Použití | Podpora GPU | Podpora FPGA | Popis |
| ----- | ----- | ----- | ----- | ----- |
| [Místní&nbsp;webová&nbsp;služba](../articles/machine-learning/how-to-deploy-and-where.md#local) | Testování a ladění | &nbsp; | &nbsp; | Používá se pro omezené testování a řešení potíží. Hardwarová akcelerace závisí na použití knihoven v místním systému.
| [Webová&nbsp;služba Azure Machine Learning COMPUTE&nbsp;instance](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Testování a ladění | &nbsp; | &nbsp; | Používá se pro omezené testování a řešení potíží.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Odvození v reálném čase |  [Ano](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (nasazení webové služby) | [Ano](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Použijte pro vysoce škálovatelná produkční nasazení. Poskytuje rychlou odezvu a automatické škálování nasazené služby. Automatické škálování clusteru není podporováno sadou Azure Machine Learning SDK. Pokud chcete změnit uzly v clusteru AKS, použijte uživatelské rozhraní pro cluster AKS v Azure Portal. AKS je jediná možnost dostupná pro návrháře. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Testování a vývoj | &nbsp;  | &nbsp; | Používejte pro vysoce škálovatelné úlohy založené na procesoru, které vyžadují méně než 48 GB paměti RAM. |
| [Výpočetní clustery Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | Tisk Odvození dávky&nbsp; | [Ano](../articles/machine-learning/how-to-use-parallel-run-step.md) (kanál strojového učení) | &nbsp;  | Spusťte dávkové vyhodnocování pro výpočetní prostředky bez serveru. Podporuje virtuální počítače s normálním a nízkou prioritou. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | Tisk Odvození v reálném čase | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | Tisk Modul&nbsp;IoT |  &nbsp; | &nbsp; | Nasaďte a zajišťovat modely ML na zařízeních IoT. |
| [Azure Data Box Edge](../articles/databox-online/azure-stack-edge-overview.md)   | Prostřednictvím IoT Edge |  &nbsp; | Ano | Nasaďte a zajišťovat modely ML na zařízeních IoT. |

> [!NOTE]
> I když výpočetní cíle, jako je místní, Azure Machine Learning výpočetní instance a Azure Machine Learning výpočetní clustery, podporují GPU pro školení a experimentování a __při nasazení jako webové služby v případě__ , že je tato služba dostupná jenom ve službě Azure Kubernetes, se používá GPU pro odvození.
>
> Použití GPU pro odvození __při vyhodnocování s kanálem strojového učení__ je podporované jenom v Azure Machine Learning Compute.