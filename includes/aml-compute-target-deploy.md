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
ms.openlocfilehash: 29da4ab949cf38419bc7a4d0a3b5da6669441887
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70847669"
---
| Cílové výpočetní prostředí | Použití | Podpora GPU | Podpora FPGA | Popis |
| ----- | ----- | ----- | ----- | ----- |
| [Místní&nbsp;webová&nbsp;služba](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Testování a ladění | &nbsp; | &nbsp; | Používá se pro omezené testování a řešení potíží. Hardwarová akcelerace závisí na použití knihoven v místním systému.
| [&nbsp;Webová&nbsp;služba pro virtuální počítač poznámkového bloku](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Testování a ladění | &nbsp; | &nbsp; | Používá se pro omezené testování a řešení potíží.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Odvození v reálném čase |  [Ano](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md) (nasazení webové služby) | [Ano](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Použijte pro vysoce škálovatelná produkční nasazení. Poskytuje rychlou odezvu a automatické škálování nasazené služby. Automatické škálování clusteru není podporováno sadou Azure Machine Learning SDK. Pokud chcete změnit uzly v clusteru AKS, použijte uživatelské rozhraní pro cluster AKS v Azure Portal. AKS je jediná možnost dostupná pro vizuální rozhraní. |
| [Azure Container Instances](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Testování a vývoj | &nbsp;  | &nbsp; | Používejte pro vysoce škálovatelné úlohy založené na procesoru, které vyžadují méně než 48 GB paměti RAM. |
| [Azure Machine Learning Compute](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Tisk Odvození dávky&nbsp; | [Ano](../articles/machine-learning/service/how-to-run-batch-predictions.md) (kanál strojového učení) | &nbsp;  | Spusťte dávkové vyhodnocování pro výpočetní prostředky bez serveru. Podporuje virtuální počítače s normálním a nízkou prioritou. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Tisk Modul&nbsp;IoT |  &nbsp; | &nbsp; | Nasaďte a zajišťovat modely ML na zařízeních IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Prostřednictvím IoT Edge |  &nbsp; | Ano | Nasaďte a zajišťovat modely ML na zařízeních IoT. |

> [!NOTE]
> I když výpočetní prostředky, jako je místní, virtuální počítač poznámkového bloku a Azure Machine Learning výpočetní podpory, podporují GPU a experimenty pomocí GPU pro odvození, pokud je tato __služba nasazená jako webová služba__ , je podporovaná jenom ve službě Azure Kubernetes.
>
> Použití GPU pro odvození __při vyhodnocování s kanálem strojového učení__ je podporované jenom v Azure Machine Learning Compute.