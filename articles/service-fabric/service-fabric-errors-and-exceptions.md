---
title: Běžné FabricClient výjimky vydané | Microsoft Docs
description: Popisuje běžné výjimek a chyb, k nimž může být vyvolána rozhraní API FabricClient při provádění aplikace a operace správy clusterů.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: bb821313-b221-479f-b08e-36cf07e60a07
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/18/2017
ms.author: ryanwi
ms.openlocfilehash: 31defde289643b2adfd6e15bb8417b5498ba65d1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Běžné výjimek a chyb při práci s rozhraními API sady FabricClient
[FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) rozhraní API umožňují cluster a aplikace správcům umožňuje provádět úlohy správy na aplikace, služby nebo clusteru Service Fabric. Například nasazení aplikace, upgrade a odebrání, kontrola stavu clusteru nebo testování služby. Vývojáři aplikací a Správce clusteru můžete použít rozhraní API FabricClient vyvíjet nástroje pro správu clusteru Service Fabric a aplikace.

Existuje mnoho různých typů operací, které je možné provést pomocí FabricClient.  Každá metoda může vyvolat výjimky pro chyby z důvodu nesprávné vstup, chyby za běhu nebo problémů s přechodnou infrastrukturou.  V tématu referenční dokumentace rozhraní API k vyhledání výjimek, které jsou vyvolány pomocí konkrétní metody. Existují některé výjimky, ale to může být vyvolána mnoho různých [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) rozhraní API. Následující tabulka uvádí výjimky, které jsou společné pro rozhraní API FabricClient.

| Výjimka | Při vyvolání |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception#System_Fabric_FabricObjectClosedException) |[FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) objekt je v uzavřeném stavu. Odstranění [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) používáte a vytvořit novou instanci objektu [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient#System_Fabric_FabricClient) objektu. |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception#System_TimeoutException) |Vypršel časový limit operace. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) je vrácena, pokud operace trvá déle než jako MaxOperationTimeout k dokončení. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception#System_UnauthorizedAccessException) |Kontrola přístupu pro operace se nezdařila. E_ACCESSDENIED je vrácen. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException) |Při provádění operace došlo k chybě za běhu. Libovolná z metod FabricClient potenciálně throw [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException), [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException_ErrorCode) vlastnost určuje přesné příčině výjimky. Kódy chyb jsou definovány v [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) výčtu. |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception#System_Fabric_FabricTransientException) |Operace se nezdařila z důvodu přechodného chybový stav určitého druhu. Například operace může selhat, protože kvorum repliky není dočasně dostupná. Přechodný výjimky odpovídají neúspěšné operace, které můžete zkusit znovu. |

Některé běžné [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) chyb, které mohou být vráceny v [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException):

| Chyba | Podmínka |
| --- |:--- |
| CommunicationError |Komunikační chyba způsobila se operace nezdaří, zkuste operaci zopakovat. |
| InvalidCredentialType |Tento typ přihlašovacích údajů je neplatný. |
| InvalidX509FindType |X509FindType je neplatný. |
| InvalidX509StoreLocation |X509 umístění úložiště je neplatný. |
| InvalidX509StoreName |X509 úložiště název je neplatný. |
| InvalidX509Thumbprint |X509 řetězec kryptografický otisk certifikátu je neplatný. |
| InvalidProtectionLevel |Úroveň ochrany je neplatná. |
| InvalidX509Store |X509 nelze otevřít úložiště certifikátů. |
| InvalidSubjectName |Název předmětu je neplatný. |
| InvalidAllowedCommonNameList |Formát řetězce seznamu běžný název je neplatný. To by měl být čárkami oddělený seznam. |

