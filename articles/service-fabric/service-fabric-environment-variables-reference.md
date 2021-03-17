---
title: Proměnné prostředí Azure Service Fabric
description: Přečtěte si o proměnných prostředí v Azure Service Fabric. Obsahuje odkaz na úplný seznam proměnných a jejich použití.
ms.topic: reference
ms.date: 12/07/2017
ms.openlocfilehash: b70249daa439b5a631b5a84b10c47f082ce75985
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574577"
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
