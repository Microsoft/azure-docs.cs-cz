---
title: ZASTARALÉ Fondy agentů DC/OS pro Azure Container Service
description: Jak fungují veřejné a soukromé fondy agentů s Azure Container Service cluster DC/OS
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bb9b33bf537ebd5a563f8e8a8afd45cd2e5b292d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76278381"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>ZASTARALÉ Fondy agentů DC/OS pro Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Clustery DC/OS v Azure Container Service obsahují uzly agentů ve dvou fondech, veřejný fond a privátní fond. Aplikaci lze nasadit do fondu, což má vliv na přístupnost mezi počítači ve službě Container Service. Počítače můžou být vystavené na internetu (veřejné) nebo uchovávat interní (privátní). Tento článek poskytuje stručný přehled důvodů, proč existují veřejné a soukromé fondy.


* **Privátní agenti**: uzly privátního agenta se spouštějí prostřednictvím sítě, která není směrovatelný. Tato síť je dostupná jenom z oblasti pro správu nebo z hraničního směrovače veřejné zóny. Ve výchozím nastavení služba DC/OS spouští aplikace na uzlech privátních agentů. 

* **Veřejné agenti**: uzly veřejného agenta spouštějí aplikace a služby DC/OS prostřednictvím veřejně přístupné sítě. 

Další informace o zabezpečení sítě DC/OS najdete v dokumentaci k [DC/OS](https://docs.mesosphere.com/).

## <a name="deploy-agent-pools"></a>Nasazení fondů agentů

Fondy agentů DC/OS ve službě Azure Container Service se vytvoří takto:

* **Privátní fond** obsahuje počet uzlů agentů, které zadáte při [nasazení clusteru DC/OS](container-service-deployment.md). 

* **Veřejný fond** zpočátku obsahuje předem určený počet uzlů agentů. Tento fond se přidá automaticky, když se zřídí cluster DC/OS.

Privátní fond a veřejný fond jsou služby Azure Virtual Machine Scale Sets. Po nasazení můžete změnit velikost těchto fondů.

## <a name="use-agent-pools"></a>Použití fondů agentů
Ve výchozím nastavení **Marathon** nasadí každou novou aplikaci do uzlů *privátních* agentů. Je nutné explicitně nasadit aplikaci do *veřejných* uzlů během vytváření aplikace. Vyberte kartu **volitelné** a zadejte **Slave_public** pro hodnotu **akceptované role prostředků** . Tento proces je popsán [zde](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) a v dokumentaci [DC/OS](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/) .

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [správě kontejnerů DC/OS](container-service-mesos-marathon-ui.md).

* Naučte se, jak [otevřít bránu firewall](container-service-enable-public-access.md) poskytovanou službou Azure, která umožňuje veřejný přístup ke kontejnerům DC/OS.

