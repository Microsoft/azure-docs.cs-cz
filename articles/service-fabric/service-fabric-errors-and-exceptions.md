---
title: Byly vyvolány běžné výjimky FabricClient
description: Popisuje běžné výjimky a chyby, které mohou být vyvolány rozhraními API FabricClient při provádění operací správy aplikací a clusterů.
author: georgewallace
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: gwallace
ms.openlocfilehash: 7f3c3e072a3a2e4f7723f84b2c70ba0d0ddb9d03
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86258835"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Běžné výjimky a chyby při práci s rozhraním API FabricClient
Rozhraní [FabricClient](/dotnet/api/system.fabric.fabricclient) API umožňují správcům clusterů a aplikací provádět úlohy správy Service Fabric aplikace, služby nebo clusteru. Například nasazení aplikace, upgrade a odebrání, kontrola stavu clusteru nebo testování služby. Vývojáři aplikací a Správci clusterů můžou používat rozhraní FabricClient API k vývoji nástrojů pro správu Service Fabric clusteru a aplikací.

Existuje mnoho různých typů operací, které lze provést pomocí FabricClient.  Každá metoda může vyvolat výjimky z důvodu nesprávného vstupu, chyb za běhu nebo přechodných problémů infrastruktury.  V dokumentaci Reference k rozhraní API najdete informace o tom, které výjimky jsou vyvolány konkrétní metodou. Existují však některé výjimky, které mohou být vyvolány mnoha různými [FabricClient](/dotnet/api/system.fabric.fabricclient) rozhraními API. V následující tabulce jsou uvedeny výjimky, které jsou společné napříč FabricClient rozhraními API.

| Výjimka | Vyvolána, když |
| --- |:--- |
| [System. Fabric. FabricObjectClosedException](/dotnet/api/system.fabric.fabricobjectclosedexception) |Objekt [FabricClient](/dotnet/api/system.fabric.fabricclient) je v zavřeném stavu. Vyřazení objektu [FabricClient](/dotnet/api/system.fabric.fabricclient) , který používáte, a vytvoření instance nového objektu [FabricClient](/dotnet/api/system.fabric.fabricclient) . |
| [System. TimeoutException](/dotnet/core/api/system.timeoutexception) |Vypršel časový limit operace. [OperationTimedOut](/dotnet/api/system.fabric.fabricerrorcode) se vrátí, když operace trvá déle než MaxOperationTimeout. |
| [System. UnauthorizedAccessException](/dotnet/core/api/system.unauthorizedaccessexception) |Ověření přístupu k operaci se nezdařilo. Je vrácena E_ACCESSDENIED. |
| [System. Fabric. FabricException](/dotnet/api/system.fabric.fabricexception) |Při provádění operace došlo k chybě modulu runtime. Kterákoli z metod FabricClient může potenciálně vyvolat [FabricException](/dotnet/api/system.fabric.fabricexception), vlastnost [ErrorCode](/dotnet/api/system.fabric.fabricexception.errorcode) indikuje přesný důvod výjimky. Kódy chyb jsou definovány ve výčtu [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) . |
| [System. Fabric. FabricTransientException](/dotnet/api/system.fabric.fabrictransientexception) |Operace se nezdařila z důvodu přechodné chybové podmínky nějakého druhu. Operace může například selhat, protože kvorum replik je dočasně nedosažitelné. Přechodné výjimky odpovídají neúspěšným operacím, které lze opakovat. |

Některé běžné chyby [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) , které mohou být vráceny v [FabricException](/dotnet/api/system.fabric.fabricexception):

| Chyba | Podmínka |
| --- |:--- |
| CommunicationError |Chyba komunikace způsobila selhání operace, zkuste operaci zopakovat. |
| InvalidCredentialType |Typ přihlašovacích údajů je neplatný. |
| InvalidX509FindType |X509FindType je neplatný. |
| InvalidX509StoreLocation |Umístění úložiště x509 je neplatné. |
| InvalidX509StoreName |Název úložiště x509 je neplatný. |
| InvalidX509Thumbprint |Řetězec kryptografického otisku certifikátu x509 je neplatný. |
| InvalidProtectionLevel |Úroveň ochrany je neplatná. |
| InvalidX509Store |Úložiště certifikátů x509 nelze otevřít. |
| InvalidSubjectName |Název subjektu je neplatný. |
| InvalidAllowedCommonNameList |Formát řetězce seznamu běžných názvů je neplatný. Mělo by se jednat o čárkami oddělený seznam. |
