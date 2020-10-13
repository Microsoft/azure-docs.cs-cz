---
title: Diagnostika a řešení potíží s dostupností sad Azure Cosmos SDK v prostředí s více oblastmi
description: Seznamte se s chováním dostupnosti sady SDK Azure Cosmos při provozu ve více regionálních prostředích.
author: ealsur
ms.service: cosmos-db
ms.date: 10/05/2020
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 400795d20b6e7ad919f5cbbfa6078987bb65297e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743960"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>Diagnostika a řešení potíží s dostupností sad Azure Cosmos SDK v prostředí s více oblastmi

Tento článek popisuje chování nejnovější verze sad Azure Cosmos SDK, pokud se zobrazí problém s připojením k určité oblasti nebo když dojde k převzetí služeb při selhání oblasti.

Všechny sady SDK pro Azure Cosmos poskytují možnost přizpůsobit místní preference. V různých sadách SDK se používají následující vlastnosti:

* Vlastnost [ConnectionPolicy. PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) v sadě .NET v2 SDK.
* Vlastnosti [CosmosClientOptions. ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) nebo [CosmosClientOptions. ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) v sadě .NET V3 SDK.
* Metoda [CosmosClientBuilder. preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) v sadě Java v4 SDK.
* Parametr [CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) v sadě Python SDK.
* Parametr [CosmosClientOptions. ConnectionPolicy. preferredLocations](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations) v sadě js SDK.

Když nastavíte místní předvolbu, klient se připojí k oblasti, jak je uvedeno v následující tabulce:

|Typ účtu |Čtení |Zápisy |
|------------------------|--|--|
| Jedna oblast zápisu | Upřednostňovaná oblast | Primární oblast  |
| Více oblastí zápisu | Upřednostňovaná oblast | Upřednostňovaná oblast  |

Pokud nenastavíte upřednostňovanou oblast:

|Typ účtu |Čtení |Zápisy |
|------------------------|--|--|
| Jedna oblast zápisu | Primární oblast | Primární oblast |
| Více oblastí zápisu | Primární oblast  | Primární oblast  |

> [!NOTE]
> Primární oblast odkazuje na první oblast v [seznamu oblastí účtu Azure Cosmos](distribute-data-globally.md) .

Když nastane kterýkoli z následujících scénářů, klient nástroje, který používá sadu Azure Cosmos SDK, zveřejňuje protokoly a obsahuje informace o opakování v rámci **diagnostických informací o operaci**:

* Vlastnost *RequestDiagnosticsString* na odpovědích v sadě .NET v2 SDK.
* Vlastnost *Diagnostic* na odpovědích a výjimkách v sadě .NET V3 SDK.
* Metoda *Getdiagnostics ()* na odpovědích a výjimkách v sadě Java v4 SDK.

Při určování další oblasti v upřednostňovaném pořadí bude klient sady SDK používat seznam oblastí účtu a stanovit prioritu upřednostňovaných oblastí (pokud existují).

Podrobné informace o zárukách SLA v těchto událostech najdete v [SLA dostupnosti](high-availability.md#slas-for-availability).

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>Odebrání oblasti z účtu

Když odeberete oblast z účtu Azure Cosmos, vyhledá kterýkoli klient sady SDK, který aktivně používá účet, odebrání oblasti prostřednictvím kódu odpovědi back-endu. Klient pak označí místní koncový bod jako nedostupný. Klient opakuje aktuální operaci a všechny budoucí operace budou trvale směrovány do další oblasti v pořadí podle priority.

## <a name="adding-a-region-to-an-account"></a>Přidání oblasti k účtu

Každých 5 minut klient služby Azure Cosmos SDK přečte konfiguraci účtu a aktualizuje oblasti, o kterých je vědom.

Pokud odeberete oblast a později ji přidáte zpátky k účtu, pokud přidaná oblast má v konfiguraci sady SDK vyšší pořadí priorit, než je aktuální připojená oblast, sada SDK se vrátí k trvalému použití této oblasti. Po zjištění přidané oblasti se na ni budou směrovat všechny budoucí požadavky.

Pokud nakonfigurujete klienta nástroje tak, aby se k oblasti, kterou účet Azure Cosmos nemá, připojovat, bude preferovaná oblast ignorována. Pokud přidáte tuto oblast později, klient ji detekuje a bude trvale přepnuta do této oblasti.

## <a name="failover-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>Převzetí služeb při selhání v oblasti zápisu v jednom účtu s oblastí zápisu

Pokud zahájíte převzetí služeb při selhání aktuální oblasti pro zápis, další požadavek na zápis se nezdaří se známou odpovědí na back-end. Když je tato odpověď zjištěná, klient odešle dotaz na účet, aby se dozvěděl o nové oblasti pro zápis, a pokračuje v operaci opakování aktuální operace a trvale přesměruje všechny budoucí operace zápisu do nové oblasti.

## <a name="regional-outage"></a>Oblastní výpadek

Pokud je účet jediný zápis a v průběhu operace zápisu dojde k oblasti výpadku, chování je podobné [ručnímu převzetí služeb při selhání](#manual-failover-single-region). U žádostí o čtení nebo u více účtů oblastí zápisu je chování podobné jako při [odebrání oblasti](#remove-region).

## <a name="session-consistency-guarantees"></a>Záruky konzistence relace

Při použití [konzistence relací](consistency-levels.md#guarantees-associated-with-consistency-levels)musí klient zaručit, že může číst vlastní zápisy. V jednom z účtů oblastí pro zápis, kde se preference oblasti čtení liší od oblasti zápisu, můžou nastat případy, kdy uživatel vystaví zápis a když provádí čtení z místní oblasti, místní oblast ještě neobdržela replikaci dat (rychlost omezení světla). V takových případech sada SDK detekuje konkrétní selhání operace čtení a opakuje čtení v oblasti centra, aby se zajistila konzistence relace.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>Problémy s přechodným připojením v protokolu TCP

Ve scénářích, kdy je klient služby Azure Cosmos SDK nakonfigurovaný tak, aby používal protokol TCP, může pro daný požadavek dojít k situaci, kdy podmínky sítě dočasně ovlivňují komunikaci s konkrétním koncovým bodem. Tyto dočasné síťové podmínky se můžou nacházet jako časový limit TCP. Klient zopakuje požadavek lokálně na stejném koncovém bodu po určitou dobu.

Pokud uživatel nakonfiguroval seznam upřednostňovaných oblastí s více než jednou oblastí a účet Azure Cosmos je více oblastí zápisu nebo jedna oblast pro zápis a operace je požadavek na čtení, bude klient opakovat tuto jednu operaci v další oblasti ze seznamu předvoleb.

## <a name="next-steps"></a>Další kroky

* Zkontrolujte [SLA dostupnosti](high-availability.md#slas-for-availability).
* Použití nejnovější [sady .NET SDK](sql-api-sdk-dotnet-standard.md)
* Použít nejnovější [sadu Java SDK](sql-api-sdk-java-v4.md)
* Použití nejnovější [sady Python SDK](sql-api-sdk-python.md)
* Použití nejnovější [sady SDK pro Node](sql-api-sdk-node.md)
