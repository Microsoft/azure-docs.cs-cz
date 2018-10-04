---
title: Proměnné prostředí Azure Service Fabric | Dokumentace Microsoftu
description: Referenční dokumentace pro Service Fabric proměnné prostředí
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
ms.openlocfilehash: 1c8400898dba59f312ba9d994ee711a5e241973a
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268000"
---
# <a name="service-fabric-environment-variables"></a>Proměnné prostředí Service Fabric

Service Fabric je integrované prostředí proměnné nastavené pro každou repliku služby. Úplný seznam proměnných prostředí je nižší než:

| Proměnná prostředí                         | Popis                                                            | Příklad:                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Název identifikátoru uri prostředků infrastruktury aplikace                                 | Fabric: / MyApplication                                                |
| Fabric_CodePackageName                       | Název balíčku kódu, ke kterému patří procesu              | Kód                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | Ip adresa nebo plně kvalifikovaného názvu domény koncového bodu                                 | 10.0.0.1                                                     |
| Fabric\_koncový bod\_*ServiceEndpointName*              | Číslo portu pro koncový bod                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Uloží do složky protokolů                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\protokolu      |
| Fabric_Folder_App_Temp                       | Dočasná složka                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Pracovní složky                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\práce     |
| Fabric_Folder_Application                    | Domovskou složku aplikace                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Hodnotu určující, jestli proces je kontejner                   | false (nepravda)                                                                |
| Fabric_NodeId                                | ID uzlu uzlu spuštění procesu                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | Soubor manifestu IP adresu nebo plně kvalifikovaný název domény uzlu, jak je uvedeno v clusteru. | localhost nebo 10.0.0.1                                                |
| Fabric_NodeName                              | Název uzlu uzlu spuštění procesu                          | _Node_0                                                              |
| Fabric_ServiceName                           | Název prostředků infrastruktury identifikátor uri služby, pokud je v režimu ExclusiveProcess hostované služby. Tato proměnná je k dispozici, pokud vytvoříte službu s ServicePackageActivationMode ExclusiveProcess pouze.  | Fabric: / MyApplication/Moje_služba                                               |
| Fabric_ServicePackageActivationId            | ServicePackageActivationId                                         | IDENTIFIKÁTOR GUID                                                               |
| Fabric_ServicePackageName                    | Název balíčku služby procesu je součástí                     | Web1Pkg                                                              |

Interní systémové proměnné používané modul runtime Service Fabric:

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
