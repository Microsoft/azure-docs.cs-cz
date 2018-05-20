---
title: Proměnné prostředí Azure Service Fabric | Microsoft Docs
description: Referenční dokumentace pro proměnné prostředí Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: f7c36fec7ff58c225e41899e8264ca1dde95ce7c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="service-fabric-environment-variables"></a>Proměnné prostředí Service Fabric

Service Fabric má integrované prostředí proměnné nastavené pro každou instanci služby. Úplný seznam proměnných prostředí, je nižší než:

| Proměnné prostředí                         | Popis                                                            | Příklad:                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Identifikátor uri název prostředků infrastruktury aplikace                                 | Fabric: / Moje_aplikace                                                |
| Fabric_CodePackageName                       | Název balíčku kódu, do které patří proces              | Kód                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | Ip adresa nebo plně kvalifikovaný název domény koncového bodu                                 | 10.0.0.1                                                     |
| Fabric\_koncový bod\_*ServiceEndpointName*              | Číslo portu pro koncový bod                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Do složky protokolů                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\protokolu      |
| Fabric_Folder_App_Temp                       | Dočasnou složku                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Pracovní složky                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\práce     |
| Fabric_Folder_Application                    | Domovské složky aplikace                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Určení, zda je proces kontejner bool                   | false (nepravda)                                                                |
| Fabric_NodeId                                | ID uzlu uzlu spuštěním procesu                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | Soubor manifestu, IP adresa nebo plně kvalifikovaný název domény uzlu, jak je uvedeno v clusteru. | localhost nebo 10.0.0.1                                                |
| Fabric_NodeName                              | Název uzlu uzlu spuštěním procesu                          | _Node_0                                                              |
| Fabric_ServiceName                           | Název služby, pokud služba je hostována v ExclusiveProcess režimu. Tato proměnná je k dispozici, pokud vytvoříte službu s ServicePackageActivationMode ExclusiveProcess pouze.  | Moje_služba                                               |
| Fabric_ServicePackageActivationId            | ServicePackageActivationId                                         | IDENTIFIKÁTOR GUID                                                               |
| Fabric_ServicePackageName                    | Název balíčku pro service proces je součástí                     | Web1Pkg                                                              |

Interní systémové proměnné používané modulem Service Fabric Runtime:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName