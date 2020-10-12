---
title: Proměnné prostředí Azure Service Fabric
description: Přečtěte si o proměnných prostředí v Azure Service Fabric. Obsahuje odkaz na úplný seznam proměnných a jejich použití.
author: mikkelhegn
ms.topic: reference
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: b13522b1d9f2acd2aa3f7923c1b623fab696056d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75645680"
---
# <a name="service-fabric-environment-variables"></a>Service Fabric proměnných prostředí

Service Fabric má pro každou instanci služby nastavenou předdefinované proměnné prostředí. Úplný seznam proměnných prostředí je následující:

| Proměnná prostředí                         | Popis                                                            | Příklad                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Název identifikátoru URI prostředků infrastruktury aplikace                                 | Fabric:/MyApplication                                                |
| Fabric_CodePackageName                       | Název balíčku kódu, ke kterému patří proces              | Kód                                                                 |
| Fabric_Endpoint \_ IPOrFQDN. \_ *ServiceEndpoint*     | IP adresa nebo plně kvalifikovaný název domény koncového bodu                                 | 10.0.0.1                                                     |
| Fabric – \_ koncový bod koncového bodu \_ *ServiceEndpointName*              | Číslo portu pro koncový bod                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Složka protokolu                                                             | C: \\ \\ datový \\ \\ _App \\ \\ _Node_0 \\ \\ MyApplicationType_App12 \\ \\ protokolu      |
| Fabric_Folder_App_Temp                       | Dočasná složka                                                            | C: \\ \\ Data \\ \\ _App \\ \\ _Node_0 \\ \\ MyApplicationType_App12 \\ \\ TEMP     |
| Fabric_Folder_App_Work                       | Pracovní složka                                                            | C: \\ \\ Data \\ \\ _App \\ \\ _Node_0 \\ \\ MyApplicationType_App12 \\ \\ práci     |
| Fabric_Folder_Application                    | Domovská složka aplikací                                           | C: \\ \\ Data \\ \\ _App \\ \\ _Node_0 \\ \\ MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Logická hodnota určující, zda je proces kontejner                   | false (nepravda)                                                                |
| Fabric_NodeId                                | ID uzlu, na kterém je spuštěný proces                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | IP adresa nebo plně kvalifikovaný název domény uzlu, jak je uvedeno v souboru manifestu clusteru. | localhost nebo 10.0.0.1                                                |
| Fabric_NodeName                              | Název uzlu, na kterém je spuštěn proces                          | _Node_0                                                              |
| Fabric_ServiceName                           | Název identifikátoru URI prostředků infrastruktury služby, pokud je služba hostovaná v režimu ExclusiveProcess. Tato hodnota proměnné je k dispozici pouze v případě, že vytvoříte službu pomocí ServicePackageActivationMode ExclusiveProcess.  | Fabric:/MyApplication/Mojesluzba                                               |
| Fabric_ServicePackageActivationId            | ServicePackageActivationId                                         | IDENTIFIKÁTOR GUID                                                               |
| Fabric_ServicePackageName                    | Název balíčku služby, jehož součástí je proces                     | Web1Pkg                                                              |

Interní proměnné prostředí používané modulem runtime Service Fabric:

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
