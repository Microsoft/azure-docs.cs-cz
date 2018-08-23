---
title: Fondy agentů DC/OS pro službu Azure Container Service
description: Jak fungují fondy veřejných i privátních agentů s clusterem Azure Container Service DC/OS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 17029f51be9fed8fc36c5f919ece84acbf0461d9
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "42057165"
---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Fondy agentů DC/OS pro službu Azure Container Service
Clustery DC/OS v Azure Container Service obsahovat agentské uzly v dva fondy, fondu veřejného a privátního fondu. Aplikace je možné nasadit buď fondu by to ovlivnilo usnadnění mezi počítači služby container Service. Počítače můžete přístupný z Internetu (veřejných) nebo udržovat interní (privátní). Tento článek poskytuje stručný přehled důvod, proč k fondům veřejné a soukromé.


* **Privátní agenti**: privátní agentské uzly projít-směrovatelné síti. Tato síť je dostupná jenom v zóně správce nebo přes hraniční směrovač veřejnou zónu. Ve výchozím nastavení DC/OS se spustí aplikace na privátní agentské uzly. 

* **Veřejní agenti**: veřejné agentské uzly spuštění aplikace v DC/OS a služeb prostřednictvím veřejně přístupné síti. 

Další informace o zabezpečení sítě DC/OS, najdete v článku [dokumentaci DC/OS](https://dcos.io/docs/1.8/administration/securing-your-cluster/).

## <a name="deploy-agent-pools"></a>Fondy agentů nasazení

Fondy agentů DC/OS v Azure Container Service vytvoří následujícím způsobem:

* **Privátního fondu** obsahuje počet uzlů agentů, můžete určit, kdy jste [nasazení clusteru DC/OS](container-service-deployment.md). 

* **Veřejný fond** zpočátku obsahuje předem určený počet uzlů agentů. Tento fond se automaticky přidá při zřízení clusteru DC/OS.

Fond privátních a veřejných fondu jsou škálovací sady virtuálních počítačů Azure. Po nasazení můžete změnit velikost těchto fondů.

## <a name="use-agent-pools"></a>Používat fondy agentů
Ve výchozím nastavení **Marathon** nasadí jakékoli nové aplikace *privátní* agentské uzly. Je nutné explicitně nasazení aplikace *veřejné* uzlů během vytváření aplikace. Vyberte **volitelné** kartě a zadejte **ACCEPTED** pro **role prostředků** hodnotu. Tento proces je zdokumentován [tady](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) a [DC/OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) dokumentaci.

## <a name="next-steps"></a>Další postup
* Další informace o [Správa kontejnerů systému DC/OS](container-service-mesos-marathon-ui.md).

* Zjistěte, jak [otevření brány firewall](container-service-enable-public-access.md) poskytovaný platformou Azure umožní veřejný přístup k kontejnerů DC/OS.

