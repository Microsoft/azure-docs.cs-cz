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
ms.openlocfilehash: e402989c6bdbbc90ae10047fcf19f4f2d74328a6
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014749"
---
| Cílové výpočetní prostředí | Použití | Podpora GPU | Podpora FPGA | Popis |
| ----- | ----- | ----- | ----- | ----- |
| [Místní&nbsp;webová&nbsp;služba](../articles/machine-learning/service/how-to-deploy-and-where.md#local) | Testování/ladění | &nbsp; | &nbsp; | Vhodné pro omezené testování a řešení potíží. Hardwarová akcelerace závisí na používání knihoven v místním systému.
| [&nbsp;Webová&nbsp;služba pro virtuální počítač poznámkového bloku](../articles/machine-learning/service/how-to-deploy-and-where.md#notebookvm) | Testování/ladění | &nbsp; | &nbsp; | Vhodné pro omezené testování a řešení potíží. 
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/service/how-to-deploy-and-where.md#aks) | Odvození v reálném čase |  [Ano](../articles/machine-learning/service/how-to-deploy-inferencing-gpus.md)  | [Ano](../articles/machine-learning/service/how-to-deploy-fpga-web-service.md)   |Vhodné pro nasazení v produkčním prostředí vysoce škálovatelné. Poskytuje rychlou odezvu a automatické škálování nasazené služby. Automatické škálování clusteru není podporováno sadou Azure Machine Learning SDK. Pokud chcete změnit uzly v clusteru AKS, použijte uživatelské rozhraní pro cluster AKS v Azure Portal. AKS je jediná možnost dostupná pro vizuální rozhraní. |
| [Azure Container Instances (ACI)](../articles/machine-learning/service/how-to-deploy-and-where.md#aci) | Testování a vývoj | &nbsp;  | &nbsp; | Vhodné pro vysoce škálovatelné úlohy založené na procesoru, které vyžadují < 48 GB RAM |
| [Azure Machine Learning Compute](../articles/machine-learning/service/how-to-run-batch-predictions.md) | Tisk Odvození dávky&nbsp; | &nbsp; | &nbsp;  | Spusťte dávkové vyhodnocování pro výpočetní prostředky bez serveru. Podporuje virtuální počítače s normálním a nízkou prioritou. |
| [Azure IoT Edge](../articles/machine-learning/service/how-to-deploy-and-where.md#iotedge) | Tisk Modul&nbsp;IoT |  &nbsp; | &nbsp; | Nasaďte & obsluhující modely ML na zařízeních IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | prostřednictvím IoT Edge |  &nbsp; | ano | Nasaďte & obsluhující modely ML na zařízeních IoT. |

> [!NOTE]
> I když výpočetní prostředí, jako je místní, virtuální počítač poznámkového bloku a Azure Machine Learning výpočetní podpory, se GPU pro školení a experimentování používá pro odvození GPU jenom ve službě Azure Kubernetes.