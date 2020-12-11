---
title: Azure Cosmos DB režimy připojení sady SQL SDK
description: Přečtěte si o různých režimech připojení, které jsou k dispozici na Azure Cosmos DB sadách SQL SDK.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: maquaran
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: c30e97a4bff8fa845f6eb3c3092a00ee541e59f4
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032792"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>Azure Cosmos DB režimy připojení sady SQL SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Způsob připojení klienta k Azure Cosmos DB má důležité dopady na výkon, zejména u pozorované latence na straně klienta. Azure Cosmos DB nabízí jednoduchý a otevřený programovací model RESTful přes protokol HTTPS nazvaný režim brány. Kromě toho nabízí efektivní protokol TCP, který se taky RESTful ve svém komunikačním modelu a používá protokol TLS pro počáteční ověřování a šifrování provozu, který se nazývá přímý režim.

## <a name="available-connectivity-modes"></a>Dostupné režimy připojení

K dispozici jsou dva režimy připojení:

  * Režim brány
      
    Režim brány je podporovaný na všech platformách SDK. Pokud vaše aplikace běží v podnikové síti s přísnými omezeními brány firewall, je nejlepší volbou režim brány, protože používá standardní port HTTPS a jeden koncový bod DNS. Kompromisy týkající se výkonu však jsou v tom, že režim brány zahrnuje dodatečné směrování sítě pokaždé, když se data čtou nebo se zapisují do Azure Cosmos DB. Režim připojení brány doporučujeme také v případě, že spouštíte aplikace v prostředích, které mají omezený počet připojení soketu.

    Při použití sady SDK v Azure Functions, zejména v [plánu spotřeby](../azure-functions/functions-scale.md#consumption-plan), si pamatujte na aktuální [omezení připojení](../azure-functions/manage-connections.md).

  * Přímý režim

    Přímý režim podporuje připojení prostřednictvím protokolu TCP a nabízí lepší výkon, protože je k dispozici méně segmentů směrování sítě. Aplikace se připojuje přímo k replikám back-endu. Přímý režim se momentálně podporuje jenom na platformách .NET a Java SDK.
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Režimy připojení Azure Cosmos DB" border="false":::

Tyto režimy připojení v podstatě vybírají trasu, kterou požadavky na data rovinují – čtení a zápis dokumentů – z klientského počítače na oddíly v Azure Cosmos DB back-endu. Přímým režimem je upřednostňovaná možnost pro nejlepší výkon – umožňuje klientovi otevřít připojení TCP přímo k oddílům v Azure Cosmos DB back-endu a *odesílat požadavky přímo* na zpracování bez jakýchkoli dodavatelů. V režimu brány naopak požadavky vytvořené vaším klientem jsou směrovány na server "brána" v Azure Cosmos DB front-endu, který zase odvolá vaše požadavky na příslušné oddíly v back-endu Azure Cosmos DB.

## <a name="service-port-ranges"></a>Rozsahy portů služby

Použijete-li přímý režim kromě portů brány, je nutné zajistit, aby byl rozsah portů mezi 10000 a 20000 otevřený, protože Azure Cosmos DB používá dynamické porty TCP. Při použití přímého režimu u [privátních koncových bodů](./how-to-configure-private-endpoints.md)by se mělo otevřít celý rozsah portů TCP od 0 do 65535. Pokud tyto porty nejsou otevřené a pokusíte se použít protokol TCP, může se zobrazit chyba nedostupná služba 503.

V následující tabulce najdete přehled režimů připojení dostupných pro různá rozhraní API a porty služeb používané pro každé rozhraní API:

|Režim připojení  |Podporovaný protokol  |Podporované sady SDK  |Port API/Service  |
|---------|---------|---------|---------|
|brána  |   HTTPS    |  Všechny sady SDK    |   SQL (443), MongoDB (10250, 10255, 10256), Table (443), Cassandra (10350), Graph (443) <br> Port 10250 se mapuje na výchozí rozhraní Azure Cosmos DB API pro instanci MongoDB bez geografické replikace. V případě, že jsou porty 10255 a 10256 mapovány na instanci, která má geografickou replikaci.   |
|Direct    |     TCP    |  Sada .NET SDK Java SDK    | Při použití koncových bodů veřejné/služby: porty v rozsahu 10000 až 20000<br>Při použití privátních koncových bodů: porty v rozsahu 0 až 65535 |

## <a name="next-steps"></a>Další kroky

Pro konkrétní optimalizace výkonu platformy SDK:

* [Tipy pro výkon sady .NET v2 SDK](performance-tips.md)

* [Tipy pro výkon sady .NET V3 SDK](performance-tips-dotnet-sdk-v3-sql.md)
 
* [Tipy pro výkon sady Java v4 SDK](performance-tips-java-sdk-v4-sql.md)