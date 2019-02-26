---
title: Ukázka odebrání skriptu Azure Service Fabric CLI (sfctl)
description: Odebrání aplikace z clusteru služby Azure Service Fabric pomocí rozhraní příkazového řádku Azure Service Fabric CLI
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 47f0636437fb903d43c0aaa439bb41309b56dcc9
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804412"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Odebrání aplikace z clusteru Service Fabric

Tento ukázkový skript odstraní běžící instance aplikace Service Fabric a potom zruší registraci typu a verze aplikace z clusteru.  Odstraněním instance aplikace dojde také k odstranění všech spuštěných instancí služby přidružených dané aplikaci. Soubory aplikace se pak odstraní z úložiště bitových kopií. 

V případě potřeby nainstalujte [Service Fabric CLI](../service-fabric-cli.md).

## <a name="sample-script"></a>Ukázkový skript

[!code-sh[main](../../../cli_scripts/service-fabric/remove-application/remove-application.sh "Remove an application from a cluster")]

## <a name="next-steps"></a>Další postup

Další informace najdete v [dokumentaci k rozhraní Service Fabric CLI](../service-fabric-cli.md).

Další ukázky rozhraní Service Fabric CLI pro Azure Service Fabric najdete v [ukázkách rozhraní Service Fabric CLI](../samples-cli.md).
