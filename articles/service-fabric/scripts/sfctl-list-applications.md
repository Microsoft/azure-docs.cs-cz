---
title: Výpis aplikací v clusteru v sfctl
description: Ukázka skriptu rozhraní Service Fabric CLI – výpis aplikací v clusteru Service Fabric
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 04/13/2018
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: 41685e53eb0915f54bdc2d678191e5b767990dde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "75610263"
---
# <a name="list-applications-running-in-a-service-fabric-cluster"></a>Výpis aplikací spuštěných v clusteru Service Fabric

Tento ukázkový skript se připojí ke clusteru Service Fabric a potom zobrazí seznam všech zřízených aplikací.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-sh[main](../../../cli_scripts/service-fabric/list-application/list-application.sh "List provisioned applications from a cluster")]

## <a name="next-steps"></a>Další kroky

Další informace najdete v [dokumentaci k rozhraní Service Fabric CLI](../service-fabric-cli.md).

Další ukázky rozhraní Service Fabric CLI pro Azure Service Fabric najdete v [ukázkách rozhraní Service Fabric CLI](../samples-cli.md).
