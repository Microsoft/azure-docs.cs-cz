---
title: Ukázka skriptu rozhraní Service Fabric CLI – aktualizace aplikace v clusteru
description: Ukázka skriptu rozhraní Service Fabric CLI – aktualizace aplikace pomocí nové verze Tento příklad také upgraduje nasazenou aplikaci novými bity.
services: service-fabric
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: adegeo
ms.custom: ''
ms.openlocfilehash: e14e65e365389b33891794a3f12b86b3a4705533
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204375"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Přidání certifikátu aplikace do clusteru Service Fabric

Tento ukázkový skript odešle novou verzi existující aplikace a potom upgraduje nasazenou aplikaci novými bity.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Další kroky

Další informace najdete v [dokumentaci k rozhraní Service Fabric CLI](../service-fabric-cli.md).

Další ukázky rozhraní Service Fabric CLI pro Azure Service Fabric najdete v [ukázkách rozhraní Service Fabric CLI](../samples-cli.md).
