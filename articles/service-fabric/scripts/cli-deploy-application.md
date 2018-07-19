---
title: Ukázka nasazení skriptu Azure Service Fabric CLI (sfctl)
description: Nasazení aplikace do clusteru služby Azure Service Fabric pomocí rozhraní příkazového řádku Azure Service Fabric CLI
services: service-fabric
documentationcenter: ''
author: TylerMSFT
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 04/16/2018
ms.author: twhitney
ms.custom: mvc
ms.openlocfilehash: d3e619750c45ac2d8e0b942a304aadfa05301624
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069501"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Nasazení aplikace do clusteru Service Fabric

Tento ukázkový skript zkopíruje balíček aplikace do úložiště bitových kopií clusteru, zaregistruje typ aplikace v clusteru a vytvoří instanci aplikace z typu aplikace. V tuto chvíli se vytvoří také jakékoli výchozí služby.

V případě potřeby nainstalujte [Service Fabric CLI](../service-fabric-cli.md).

## <a name="sample-script"></a>Ukázkový skript

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Až budete hotovi, můžete aplikaci [odebrat](cli-remove-application.md) prostřednictvím odebrání skriptu. Odebráním skriptu dojde k odstranění instance aplikace, zrušení registrace typu aplikace a odstranění balíčku aplikace z úložiště bitových kopií.

## <a name="next-steps"></a>Další kroky

Další informace najdete v [dokumentaci k rozhraní Service Fabric CLI](../service-fabric-cli.md).

Další ukázky rozhraní Service Fabric CLI pro Azure Service Fabric najdete v [ukázkách rozhraní Service Fabric CLI](../samples-cli.md).
