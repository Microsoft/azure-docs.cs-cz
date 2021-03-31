---
title: Ukázka nasazení skriptu Azure Service Fabric CLI (sfctl)
description: Nasazení aplikace do clusteru služby Azure Service Fabric pomocí rozhraní příkazového řádku Azure Service Fabric CLI
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 04/16/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: ff40dc62b4dcd622156a78518bbdcb6b9b430644
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75526596"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster-using-the-service-fabric-cli"></a>Nasazení aplikace do clusteru Service Fabric pomocí rozhraní příkazového řádku Service Fabric

Tento ukázkový skript zkopíruje balíček aplikace do úložiště bitových kopií clusteru, zaregistruje typ aplikace v clusteru a vytvoří instanci aplikace z typu aplikace. V tuto chvíli se vytvoří také jakékoli výchozí služby.

V případě potřeby nainstalujte [Service Fabric CLI](../service-fabric-cli.md).

## <a name="sample-script"></a>Ukázkový skript

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Až budete hotovi, můžete aplikaci [odebrat](cli-remove-application.md) prostřednictvím odebrání skriptu. Odebráním skriptu dojde k odstranění instance aplikace, zrušení registrace typu aplikace a odstranění balíčku aplikace z úložiště bitových kopií.

## <a name="next-steps"></a>Další kroky

Další informace najdete v [dokumentaci k rozhraní Service Fabric CLI](../service-fabric-cli.md).

Další ukázky rozhraní Service Fabric CLI pro Azure Service Fabric najdete v [ukázkách rozhraní Service Fabric CLI](../samples-cli.md).
