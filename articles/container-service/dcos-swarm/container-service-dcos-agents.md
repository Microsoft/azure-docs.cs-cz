---
title: (NEPOUŽÍVANÉ) Fondy agentů DC/OS pro službu Azure Container Service
description: Jak fungují fondy veřejných i privátních agentů s clusterem Azure Container Service DC/OS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 03cacda1aa405cb2d0ded579c8ddb5f6011ce3bb
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113783"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>(NEPOUŽÍVANÉ) Fondy agentů DC/OS pro službu Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Clustery DC/OS v Azure Container Service obsahovat agentské uzly v dva fondy, fondu veřejného a privátního fondu. Aplikace je možné nasadit buď fondu by to ovlivnilo usnadnění mezi počítači služby container Service. Počítače můžete přístupný z Internetu (veřejných) nebo udržovat interní (privátní). Tento článek poskytuje stručný přehled důvod, proč k fondům veřejné a soukromé.


* **Privátní agenti**: Privátní agentské uzly projít-směrovatelné síti. Tato síť je dostupná jenom v zóně správce nebo přes hraniční směrovač veřejnou zónu. Ve výchozím nastavení DC/OS se spustí aplikace na privátní agentské uzly. 

* **Veřejní agenti**: Spuštění aplikace v DC/OS a služeb prostřednictvím veřejně přístupné síti veřejné agentské uzly. 

Další informace o zabezpečení sítě DC/OS, najdete v článku [dokumentaci DC/OS](https://docs.mesosphere.com/).

## <a name="deploy-agent-pools"></a>Fondy agentů nasazení

Fondy agentů DC/OS v Azure Container Service vytvoří následujícím způsobem:

* **Privátního fondu** obsahuje počet uzlů agentů, můžete určit, kdy jste [nasazení clusteru DC/OS](container-service-deployment.md). 

* **Veřejný fond** zpočátku obsahuje předem určený počet uzlů agentů. Tento fond se automaticky přidá při zřízení clusteru DC/OS.

Fond privátních a veřejných fondu jsou škálovací sady virtuálních počítačů Azure. Po nasazení můžete změnit velikost těchto fondů.

## <a name="use-agent-pools"></a>Používat fondy agentů
Ve výchozím nastavení **Marathon** nasadí jakékoli nové aplikace *privátní* agentské uzly. Je nutné explicitně nasazení aplikace *veřejné* uzlů během vytváření aplikace. Vyberte **volitelné** kartě a zadejte **ACCEPTED** pro **role prostředků** hodnotu. Tento proces je zdokumentován [tady](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) a [DC/OS](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/) dokumentaci.

## <a name="next-steps"></a>Další postup
* Další informace o [Správa kontejnerů systému DC/OS](container-service-mesos-marathon-ui.md).

* Zjistěte, jak [otevření brány firewall](container-service-enable-public-access.md) poskytovaný platformou Azure umožní veřejný přístup k kontejnerů DC/OS.

