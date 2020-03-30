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
ms.openlocfilehash: ba1e0eee5de65364d8e5e762195f7de98384c2a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76122575"
---
| Cílový výpočetní objekt | Použití | Podpora GPU | Podpora pro FPGA | Popis |
| ----- | ----- | ----- | ----- | ----- |
| [Místní&nbsp;&nbsp;webová služba](../articles/machine-learning/how-to-deploy-and-where.md#local) | Testování/ladění | &nbsp; | &nbsp; | Používá se pro omezené testování a řešení potíží. Hardwarová akcelerace závisí na použití knihoven v místním systému.
| [Webová&nbsp;služba&nbsp;výpočetní instance Azure Machine Learning](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | Testování/ladění | &nbsp; | &nbsp; | Používá se pro omezené testování a řešení potíží.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | Odvození v reálném čase |  [Ano](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (nasazení webové služby) | [Ano](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Používá se pro nasazení ve velkém měřítku. Poskytuje rychlou dobu odezvy a automatické škálování nasazené služby. Automatické škálování clusteru není podporované prostřednictvím sady Azure Machine Learning SDK. Chcete-li změnit uzly v clusteru AKS, použijte ui pro cluster AKS na webu Azure Portal. AKS je jedinou možností, která je k dispozici pro návrháře. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | Testování nebo vývoj | &nbsp;  | &nbsp; | Používá se pro úlohy založené na procesoru v nízkém měřítku, které vyžadují méně než 48 GB paměti RAM. |
| [Výpočetní clustery Azure Machine Learning](../articles/machine-learning/how-to-use-parallel-run-step.md) | (Náhled) Odvození dávky&nbsp; | [Ano](../articles/machine-learning/how-to-use-parallel-run-step.md) (kanál strojového učení) | &nbsp;  | Spusťte dávkové vyhodnocování na výpočetních výkonech bez serveru. Podporuje virtuální ch odpyka normálních a málo prioritních. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | (Náhled) Odvození v reálném čase | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | (Náhled) Modul IoT&nbsp; |  &nbsp; | &nbsp; | Nasazujte a obsluhujte modely ML na zařízeních IoT. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Přes IoT Edge |  &nbsp; | Ano | Nasazujte a obsluhujte modely ML na zařízeních IoT. |

> [!NOTE]
> I když výpočetní cíle, jako je místní, Azure Machine Learning výpočetní instance a Azure Machine Learning výpočetní clustery podporují GPU pro školení a experimentování, pomocí GPU pro odvození __při nasazení jako webová služba__ je podporována pouze ve službě Azure Kubernetes.
>
> Použití GPU pro odvození __při vyhodnocování pomocí kanálu strojového učení__ je podporované jenom na Azure Machine Learning Compute.