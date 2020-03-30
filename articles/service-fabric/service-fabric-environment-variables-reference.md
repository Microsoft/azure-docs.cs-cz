---
title: Proměnné prostředí Azure Service Fabric
description: Další informace o proměnných prostředí v Azure Service Fabric. Obsahuje odkaz na úplný seznam proměnných a jejich použití.
author: mikkelhegn
ms.topic: reference
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: b13522b1d9f2acd2aa3f7923c1b623fab696056d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645680"
---
# <a name="service-fabric-environment-variables"></a>Proměnné prostředí Service Fabric

Service Fabric má integrované proměnné prostředí nastavené pro každou instanci služby. Úplný seznam proměnných prostředí je následující:

| Proměnná prostředí                         | Popis                                                            | Příklad                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Název uri prostředků prostředků na tkanině aplikace                                 | fabric:/MyApplication                                                |
| Fabric_CodePackageName                       | Název balíčku kódu, do kterého proces patří              | kód                                                                 |
| Fabric_Endpoint\_\_*název_koncového bodu služby IPOrFQDN*     | Adresa IP nebo víceméněnkový program koncového bodu                                 | 10.0.0.1                                                     |
| Název\_\_*koncového bodu koncového bodu* prostředků infrastruktury              | Číslo portu pro koncový bod                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Složka protokolu                                                             | C:\\\\\\\\Protokol\\\\\\\\MyApplicationType_App12\\_Node_0\\_Node_0 datových _App      |
| Fabric_Folder_App_Temp                       | Dočasná složka                                                            | C:\\\\\\\\MyApplicationType_App12\\\\\\\\\\MyApplicationType_App12\\_Node_0 MyApplicationType_App12 _App dat     |
| Fabric_Folder_App_Work                       | Pracovní složka                                                            | C:\\\\\\\\_Node_0\\\\\\\\_App\\\\_Node_0 dat MyApplicationType_App12 práce     |
| Fabric_Folder_Application                    | Domovská složka aplikací                                           | C:\\\\\\\\_Node_0\\\\\\\\MyApplicationType_App12 _Node_0 _Node_0 datového _App             |
| Fabric_IsContainerHost                       | Bool určující, zda je proces kontejner                   | false (nepravda)                                                                |
| Fabric_NodeId                                | ID uzlu s chodu procesu                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | Adresa IP nebo FQDN uzlu, jak je určena v souboru manifestu clusteru. | localhost nebo 10.0.0.1                                                |
| Fabric_NodeName                              | Název uzlu uzlu spuštěného procesem                          | _Node_0                                                              |
| Fabric_ServiceName                           | Název uri prostředků prostředků na tkanině, pokud je služba hostována v režimu ExclusiveProcess. Tato hodnota proměnné je k dispozici pouze v případě, že vytvoříte službu s ServicePackageActivationMode ExclusiveProcess.  | fabric:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | Id servicepackageactivationid                                         | Identifikátor GUID                                                               |
| Fabric_ServicePackageName                    | Název balíčku služeb, jehož součástí je proces                     | Web1Pkg                                                              |

Proměnné interního prostředí používané runtime service fabric:

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
- FabricActivatorAdresa
- FabricPackageFileName
- HostedServiceName
