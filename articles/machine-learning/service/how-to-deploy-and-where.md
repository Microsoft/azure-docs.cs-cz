---
title: Kde nasazujte modely pomocí služby Azure Machine Learning | Dokumentace Microsoftu
description: Další informace o různých způsobech, kterou můžete nasadit modely do produkčního prostředí pomocí služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/29/2018
ms.openlocfilehash: 606e8aed42bce69d5b3210b4e97f8cbfeaaf104c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961004"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Nasazujte modely pomocí služby Azure Machine Learning

Služby Azure Machine Learning poskytuje několik způsobů, jak můžete nasadit trénovaného modelu. V tomto dokumentu zjistěte, jak model nasadit jako webovou službu v cloudu Azure nebo na hraničních zařízeních IoT.

Můžete nasadit modely do následující cílových výpočetních prostředí:

- Azure Container Instances (ACI)
- Azure Kubernetes Service (AKS)
- Azure IoT Edge
- Pole programmable gate array (FPGA)

Zbývající část tohoto dokumentu se hovoří o každé z těchto možností podrobněji.

## <a name="azure-container-instances"></a>Azure Container Instances

Použití Azure Container Instances pro nasazení modelů jako koncový bod rozhraní REST API, pokud jeden nebo více z následujících podmínek je splněných:
- Hledáte rychlé nasazení, aby bylo možné stanovení skóre a ověření modelu. Obvykle se nasazení ACI dokončí za méně než 5 minut.
- Potřebujete k nasazení modelu ve vývojovém nebo testovacím prostředí. ACI umožňuje nasadit 20 skupiny kontejnerů na jedno předplatné. Další informace najdete v tématu [kvóty a dostupnost oblastí pro Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) dokumentu.

Další informace najdete v tématu [nasazení modelu do služby Azure Container Instances](how-to-deploy-to-aci.md) dokumentu.

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Pro zajištění vysoce škálovatelné produkční scénáře, můžete svůj model dokážete nasadit do Azure Kubernetes Service (AKS). Můžete použít existující cluster AKS, nebo vytvořte novou pomocí sady SDK Azure Machine Learning, rozhraní příkazového řádku nebo na webu Azure portal.

Vytváření AKS clusteru je čas procesu pro váš pracovní prostor. Po vytvoření můžete znovu použít tento cluster pro více nasazení. Při odstranění clusteru nebo skupinu prostředků, který jej obsahuje, pak musíte vytvořit nový cluster, které se budete muset nasadit.

Nasazení do AKS nabízí automatické škálování, protokolování, shromažďování dat modelu a krátké doby odezvy webové služby. 

Proces vytváření clusteru AKS trvá přibližně 20 minut.

Další informace najdete v tématu [nasazení modelu do služby Azure Kubernetes Service](how-to-deploy-to-aks.md) dokumentu.

## <a name="azure-iot-edge"></a>Azure IoT Edge

Se zařízeními IoT je rychlejší provést vyhodnocení na zařízení místo odesílání dat do cloudu a čekání na hostované v cloudu pro vrácení dat modelu. S Azure IoT Edge můžete hostovat váš model na hraničních zařízeních. Model nasazujete na hraničních zařízeních IoT, pokud budete potřebovat jeden nebo více z následujících funkcí:
- Zpracování úlohy s prioritou místně, i bez připojení cloudu
- Práce s generovaná data, která je příliš velký, aby rychle načítat cloudu
- Povolit zpracování v reálném čase prostřednictvím intelligence v nebo blízko ní místní zařízení
- Plnění požadavků na ochranu osobních údajů souvisejících dat 

Další informace najdete v tématu [nasadit do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) dokumentu.

Další informace o službě IoT Edge, najdete v článku [dokumentace k Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).


## <a name="field-programmable-gate-arrays-fpga"></a>Pole programmable gate Array (FPGA)

Hardware Accelerated modely využívající Project Brainwave umožňují dosáhnout mimořádně nízkou latenci pro požadavky v reálném čase odvozování. Project Brainwave zrychluje neuronových sítí (DNN) nasadit na pole programmable gate Array v cloudu Azure. Nejčastěji používané dopředné jsou k dispozici jako featurizers pro přenos učení nebo přizpůsobitelná váhy Trénink probíhal svoje vlastní data.

Další informace najdete v tématu [nasadit do FPGA](how-to-deploy-fpga-web-service.md) dokumentu.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak nasadit model na cíl konkrétní výpočetní prostředky, najdete v následujících dokumentech:

* [Nasazení modelu do služby Azure Container Instances](how-to-deploy-to-aci.md)
* [Nasazení modelu pro služby Azure Kubernetes](how-to-deploy-to-aks.md)
* [Nasazení modelu pro Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)
* [Nasazení modelu do FPGA](how-to-deploy-fpga-web-service.md)
