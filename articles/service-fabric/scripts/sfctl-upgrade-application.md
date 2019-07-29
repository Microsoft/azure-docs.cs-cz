---
title: Ukázka skriptu rozhraní Service Fabric CLI – aktualizace aplikace v clusteru
description: Ukázka skriptu rozhraní Service Fabric CLI – aktualizace aplikace pomocí nové verze Tento příklad také upgraduje nasazenou aplikaci novými bity.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 12/06/2017
ms.author: atsenthi
ms.custom: ''
ms.openlocfilehash: 72195a3a127e33ffa6118f77c4fa58ba5f60ee17
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600087"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Přidání certifikátu aplikace do clusteru Service Fabric

Tento ukázkový skript odešle novou verzi existující aplikace a potom upgraduje nasazenou aplikaci novými bity.

[!INCLUDE [links to azure cli and service fabric cli](../../../includes/service-fabric-sfctl.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-sh[main](../../../cli_scripts/service-fabric/upgrade-application/upgrade-application.sh "Upload and update an application on a Service Fabric cluster")]

## <a name="next-steps"></a>Další postup

Další informace najdete v [dokumentaci k rozhraní Service Fabric CLI](../service-fabric-cli.md).

Další ukázky rozhraní Service Fabric CLI pro Azure Service Fabric najdete v [ukázkách rozhraní Service Fabric CLI](../samples-cli.md).
