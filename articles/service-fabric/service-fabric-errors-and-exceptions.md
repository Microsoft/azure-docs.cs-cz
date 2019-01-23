---
title: Běžné výjimky FabricClient | Dokumentace Microsoftu
description: Popisuje běžné výjimky a chyby, které mohou být vyvolány pomocí rozhraní API FabricClient při provádění aplikace a operace správy clusterů.
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
ms.date: 06/20/2018
ms.author: ryanwi
ms.openlocfilehash: 9e29f05c71f9dfe0bcd79135deb30d713fb3abb0
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54477089"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Běžné výjimky a chyby při práci s rozhraními API FabricClient
[FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) rozhraní API umožňují správcům aplikací a clusteru k provádění úloh správy v aplikaci Service Fabric, služby nebo clusteru. Například nasazení aplikace, upgrade a odebrání, kontrola stavu clusteru nebo testování služby. Vývojáři aplikací a Správce clusterů můžete použít rozhraní API FabricClient pro vývoj aplikací pro správu clusteru Service Fabric a aplikací.

Existuje mnoho různých typů operací, které je možné provádět pomocí FabricClient.  Každá metoda může vyvolat výjimky, chyby kvůli nesprávný vstup, chyby za běhu nebo problémů s infrastrukturou přechodné.  Zobrazit referenční dokumentaci rozhraní API k vyhledání výjimek, které jsou vyvolány pomocí konkrétní metody. Existují některé výjimky, ale které mohou být vyvolány mnoho různých [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) rozhraní API. V následující tabulce jsou uvedeny výjimky, které jsou společné pro rozhraní API FabricClient.

| Výjimka | Vyvolána, když |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception#System_Fabric_FabricObjectClosedException) |[FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objektu je v uzavřeném stavu. Vyřazení [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) používáte a vytvoření instance nového objektu [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objektu. |
| [System.TimeoutException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception#System_TimeoutException) |Vypršel časový limit operace. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) je vrácena, když operace trvá déle než MaxOperationTimeout dokončete. |
| [System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception#System_UnauthorizedAccessException) |Kontrola přístupu pro operace se nezdařila. E_ACCESSDENIED je vrácena. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException) |Při provádění operace došlo k chybě za běhu. Některé z metod FabricClient může potenciálně vyvolat [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException), [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException_ErrorCode) vlastnost určuje přesné příčině výjimky. Kódy chyb jsou definovány v [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) výčtu. |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception#System_Fabric_FabricTransientException) |Operace se nezdařila z důvodu přechodných chybový stav určitého druhu. Operace může například selhat, protože kvorum replik je dočasně nedostupná. Přechodným výjimkám odpovídají neúspěšné operace, které umožňují opakovaný pokus. |

Některé běžné [FabricErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode#System_Fabric_FabricErrorCode) chyby, které mohou být vráceny v [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception#System_Fabric_FabricException):

| Chyba | Podmínka |
| --- |:--- |
| CommunicationError |Komunikační chyba způsobila operace, která se nezdaří, zkuste operaci zopakovat. |
| InvalidCredentialType |Typ přihlašovacích údajů je neplatný. |
| InvalidX509FindType |X509FindType je neplatný. |
| InvalidX509StoreLocation |X509 umístění úložiště je neplatný. |
| InvalidX509StoreName |X509 název úložiště je neplatný. |
| InvalidX509Thumbprint |X509 řetězec kryptografického otisku certifikátu je neplatný. |
| InvalidProtectionLevel |Úroveň ochrany je neplatný. |
| InvalidX509Store |X509 nelze otevřít úložiště certifikátů. |
| InvalidSubjectName |Název předmětu není platný. |
| InvalidAllowedCommonNameList |Formát řetězce seznam běžných název je neplatný. Měla by být seznam oddělený čárkami. |

