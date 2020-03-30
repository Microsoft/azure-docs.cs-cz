---
title: Běžné výjimky fabricclient vyvolány
description: Popisuje běžné výjimky a chyby, které mohou být vyvolány fabricclient API při provádění operací správy aplikací a clusteru.
author: oanapl
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: oanapl
ms.openlocfilehash: 9ad3097a490d4728e05ea90652c17c24b79cac2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457931"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>Běžné výjimky a chyby při práci s rozhraním API FabricClient
Rozhraní [API infrastruktury klienta](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) umožňují správcům clusteru a aplikací provádět úlohy správy v aplikaci, službě nebo clusteru Service Fabric. Například nasazení, upgrade a odebrání aplikací, kontrola stavu clusteru nebo testování služby. Vývojáři aplikací a správci clusteru mohou pomocí rozhraní API infrastruktury klienta vyvíjet nástroje pro správu clusteru a aplikací Service Fabric.

Existuje mnoho různých typů operací, které lze provést pomocí FabricClient.  Každá metoda může vyvolat výjimky pro chyby z důvodu nesprávného vstupu, chyby za běhu nebo přechodné infrastruktury problémy.  Naleznete v referenční dokumentaci rozhraní API zjistit, které výjimky jsou vyvolány konkrétní metodou. Existují však některé výjimky, které mohou být vyvolány mnoha [různými](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) infrastrukturními klientskými api. V následující tabulce jsou uvedeny výjimky, které jsou běžné v rámci infrastruktury klienta API.

| Výjimka | Hozený, když |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricobjectclosedexception) |Objekt [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) je v uzavřeném stavu. Dispose [fabricclient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) objekt, který používáte a vytvořit instanci nový [objekt FabricClient.](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) |
| [Výjimka časového časového období system.outException](https://docs.microsoft.com/dotnet/core/api/system.timeoutexception) |Časový režim operace byl vypován. [OperationTimedOut](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) je vrácena, když operace trvá více než MaxOperationTimeout k dokončení. |
| [Výjimka System.UnauthorizedAccessException](https://docs.microsoft.com/dotnet/core/api/system.unauthorizedaccessexception) |Kontrola přístupu pro operaci se nezdařila. E_ACCESSDENIED je vrácena. |
| [System.Fabric.FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception) |Při provádění operace došlo k chybě za běhu. Všechny metody FabricClient může potenciálně vyvolat [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception), [ErrorCode](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception.ErrorCode) vlastnost označuje přesnou příčinu výjimky. Kódy chyb jsou definovány ve výčtu [FabricErrorCode.](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) |
| [System.Fabric.FabricTransientException](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception) |Operace se nezdařila z důvodu přechodné chyby stavu nějaký druh. Operace může například selhat, protože kvorum replik není dočasně dostupné. Přechodné výjimky odpovídají neúspěšným operacím, které lze opakovat. |

Některé běžné chyby [FabricErrorCode,](https://docs.microsoft.com/dotnet/api/system.fabric.fabricerrorcode) které mohou být vráceny v [FabricException](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception):

| Chyba | Podmínka |
| --- |:--- |
| Chyba komunikace |Chyba komunikace způsobila selhání operace, opakujte operaci. |
| Neplatný typ pověření |Typ pověření je neplatný. |
| InvalidX509FindType |X509FindType je neplatný. |
| InvalidX509Umístění úložiště |Umístění úložiště X509 je neplatné. |
| NeplatnéX509Název_obchodu |Název obchodu X509 je neplatný. |
| InvalidX509Otisk palce |Řetězec kryptografického náhledu certifikátu X509 je neplatný. |
| InvalidProtectionLevel |Úroveň ochrany je neplatná. |
| InvalidX509Store |Úložiště certifikátů X509 nelze otevřít. |
| Neplatný název_předmětu |Název subjektu je neplatný. |
| Seznam neplatných_povolených_společných_názvů |Formát běžného řetězce seznamu názvů je neplatný. Měl by to být seznam oddělený čárkami. |

