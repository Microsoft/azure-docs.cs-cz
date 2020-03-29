---
title: (ZASTARALÉ) Fondy agentů DC/OS pro službu Azure Container Service
description: Jak fondy veřejných a soukromých agentů fungují s clusterem řadičů domény/operačního systému Azure Container Service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bb9b33bf537ebd5a563f8e8a8afd45cd2e5b292d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278381"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>(ZASTARALÉ) Fondy agentů DC/OS pro službu Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Clustery dc/os ve službě Azure Container Service obsahují uzly agentů ve dvou fondech, ve veřejném fondu a v soukromém fondu. Aplikace lze nasadit do obou fondu, což má vliv na usnadnění přístupu mezi počítači ve vaší službě kontejnerů. Stroje mohou být vystaveny internetu (veřejné) nebo mohou být uchovávány interní (soukromé). Tento článek poskytuje stručný přehled o tom, proč existují veřejné a soukromé fondy.


* **Soukromí agenti**: Uzly soukromého agenta procházejí nesměrovatelnou sítí. Tato síť je přístupná pouze ze zóny správy nebo prostřednictvím směrovače veřejné zóny edge. Ve výchozím nastavení spouští dc/os aplikace na uzlech soukromých agentů. 

* **Veřejní agenti**: Uzly veřejných agentů spouštějí aplikace a služby DC/OS prostřednictvím veřejně přístupné sítě. 

Další informace o zabezpečení sítě DC/OS naleznete v [dokumentaci k řadiči domény/os .](https://docs.mesosphere.com/)

## <a name="deploy-agent-pools"></a>Nasazení fondů agentů

Fondy agentů dc/os ve službě Azure Container Service se vytvářejí takto:

* **Privátní fond** obsahuje počet uzlů agenta, které zadáte při [nasazení clusteru DC/OS](container-service-deployment.md). 

* **Veřejný fond** zpočátku obsahuje předem určený počet uzlů agenta. Tento fond se přidává automaticky při zřízení clusteru dc/os.

Soukromý fond a veřejný fond jsou škálovací sady virtuálních strojů Azure. Velikost těchto fondů můžete změnit po nasazení.

## <a name="use-agent-pools"></a>Použít fondy agentů
Ve výchozím nastavení **Marathon** nasazuje všechny nové aplikace do uzlů *soukromého* agenta. Musíte explicitně nasadit aplikaci do *veřejných* uzlů během vytváření aplikace. Vyberte kartu **Volitelné** a zadejte **slave_public** pro hodnotu **Přijaté role zdrojů.** Tento proces je dokumentován [zde](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) a v dokumentaci [DC/OS.](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/)

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [správě kontejnerů DC/OS](container-service-mesos-marathon-ui.md).

* Zjistěte, jak [otevřít bránu firewall](container-service-enable-public-access.md) poskytovanou Azure, abyste povolili veřejný přístup k vašim kontejnerům DC/OS.

