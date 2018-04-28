---
title: Fondy agenta DC/OS pro Azure Container Service
description: Jak fungují veřejné a privátní agenta fondy pomocí clusteru služby Azure Container Service DC/OS
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 81059fd75f0e61324221614c4bb8eccd94203478
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Fondy agenta DC/OS pro Azure Container Service
Clustery DC/OS v Azure Container Service obsahovat agenta uzly ve dvou fondů, veřejné fondu a privátní fondu. Aplikace lze nasadit do buď fondu, které mají vliv na usnadnění přístupu mezi počítači v kontejneru služby. Na počítačích, můžete přístup k Internetu (veřejných) nebo zachovány interní (soukromý). Tento článek poskytuje stručný přehled proto nejsou veřejné a privátní fondy.


* **Privátní agenti**: uzly privátní agenta spustit přes síť s směrovat. Tato síť je přístupné pouze ze zóny správce nebo prostřednictvím hraniční směrovač veřejné zóny. Ve výchozím nastavení spustí DC/OS aplikace na uzlech privátní agenta. 

* **Veřejné agenty**: uzly veřejného agenta spustit DC/OS aplikace a služby přes síť s veřejně přístupná. 

Další informace o zabezpečení sítě DC/OS, najdete v článku [DC/OS dokumentaci](https://dcos.io/docs/1.7/administration/securing-your-cluster/).

## <a name="deploy-agent-pools"></a>Nasazení agenta fondy

Fondy agenta DC/OS v Azure Container Service vytvoří následujícím způsobem:

* **Privátní fondu** obsahuje počet uzlů agenta, můžete určit, kdy jste [nasadit cluster DC/OS](container-service-deployment.md). 

* **Veřejné fondu** původně obsahuje předem určený počet uzlů agenta. Tento fond je automaticky přidáno při zřízení clusteru DC/OS.

Fondu privátní a veřejné fondu jsou sady škálování virtuálního počítače Azure. Po nasazení můžete nastavit velikost těchto fondů.

## <a name="use-agent-pools"></a>Používat fondy agenta
Ve výchozím nastavení **Marathon** nasadí jakékoli nové aplikace *privátní* agenta uzly. Je třeba explicitně nasadit aplikaci *veřejné* uzly při vytváření aplikace. Vyberte **volitelné** kartě a zadejte **ACCEPTED** pro **role prostředků** hodnotu. Tento proces je popsána [sem](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) a v [DC/OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) dokumentaci.

## <a name="next-steps"></a>Další postup
* Další informace o [Správa kontejnerů Váš DC/OS](container-service-mesos-marathon-ui.md).

* Zjistěte, jak [otevření brány firewall](container-service-enable-public-access.md) poskytovaný platformou Azure umožňující veřejný přístup k kontejnery Váš DC/OS.

