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
ms.date: 12/11/2020
ms.openlocfilehash: 09dd6e9a9d69797c2c33270d1620e861a052efe2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97505116"
---
Cílový výpočetní výkon, který používáte k hostování vašeho modelu, bude mít vliv na náklady a dostupnost nasazeného koncového bodu. Pomocí této tabulky můžete zvolit vhodný cíl služby Compute.

| Cílový výpočetní objekt | Použití | Podpora GPU | Podpora FPGA | Description |
| ----- | ----- | ----- | ----- | ----- |
| [Místní &nbsp; Webová &nbsp; Služba](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testování a ladění | &nbsp; | &nbsp; | Používá se pro omezené testování a řešení potíží. Hardwarová akcelerace závisí na použití knihoven v místním systému.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Odvození v reálném čase |  [Ano](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (nasazení webové služby) | [Ano](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Použijte pro vysoce škálovatelná produkční nasazení. Poskytuje rychlou odezvu a automatické škálování nasazené služby. Automatické škálování clusteru není podporováno sadou Azure Machine Learning SDK. Pokud chcete změnit uzly v clusteru AKS, použijte uživatelské rozhraní pro cluster AKS v Azure Portal. <br/><br/> Podporováno v návrháři. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Testování a vývoj | &nbsp;  | &nbsp; | Používejte pro vysoce škálovatelné úlohy založené na procesoru, které vyžadují méně než 48 GB paměti RAM. <br/><br/> Podporováno v návrháři. |
| [Výpočetní clustery Azure Machine Learning](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) | &nbsp;Odvození dávky | [Ano](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) (kanál strojového učení) | &nbsp;  | Spusťte dávkové vyhodnocování pro výpočetní prostředky bez serveru. Podporuje virtuální počítače s normálním a nízkou prioritou. Žádná podpora pro odvozování v reálném čase.|

> [!NOTE]
> I když výpočetní prostředky, jako jsou místní, Azure Machine Learning výpočetní prostředí a Azure Machine Learning výpočetní clustery, podporují GPU pro školení a experimentování, při použití GPU pro odvození _při nasazení jako webové služby_ se podporuje jenom AKS.
>
> Použití GPU pro odvození _při vyhodnocování s kanálem strojového učení_ je podporované jenom v Azure Machine Learning Compute.
> 
> Při volbě SKU clusteru si naplánujte horizontální navýšení kapacity a potom horizontální navýšení kapacity. Začněte s počítačem, který má 150% paměti RAM, který váš model vyžaduje, profilujte výsledek a vyhledejte počítač, který má potřebný výkon. Jakmile se naučíte, můžete zvýšit počet počítačů tak, aby odpovídaly vašemu potřebám souběžného odvození.

> [!NOTE]
> * Instance kontejnerů jsou vhodné jenom pro malé modely, které mají velikost menší než 1 GB.
> * Používejte clustery AKS s jedním uzlem pro vývoj a testování větších modelů.