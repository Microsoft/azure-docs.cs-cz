---
title: Diagnostika a řešení potíží při používání sady .NET SDK služby Azure Cosmos DB
description: K identifikaci, diagnostice a řešení potíží s Azure Cosmos DB při použití sady .NET SDK použijte funkce, jako je protokolování na straně klienta a další nástroje třetích stran.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 09/12/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 8ae1e2ade5d8a942779fe31e324cd23756ee39ae
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079206"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnostika a řešení potíží při používání sady .NET SDK služby Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Sada Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Sada Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

Tento článek popisuje běžné problémy, alternativní řešení, diagnostické kroky a nástroje, pokud používáte [sadu .NET SDK](sql-api-sdk-dotnet.md) s Azure Cosmos DB účty rozhraní SQL API.
Sada .NET SDK poskytuje logickou reprezentaci na straně klienta pro přístup k Azure Cosmos DB rozhraní SQL API. Tento článek popisuje nástroje a přístupy, které vám pomůžou v případě jakýchkoli problémů.

## <a name="checklist-for-troubleshooting-issues"></a>Kontrolní seznam pro řešení problémů

Před přesunutím aplikace do produkčního prostředí Vezměte v úvahu následující kontrolní seznam. Při použití kontrolního seznamu se zabrání několik běžných problémů, které byste mohli vidět. Můžete také rychle diagnostikovat situaci, kdy dojde k problému:

*    Použijte nejnovější [sadu SDK](sql-api-sdk-dotnet-standard.md). Sady SDK verze Preview by se neměly používat pro produkční prostředí. Zabráníte tak známým problémům, které již byly opraveny.
*    Projděte si [tipy ke zvýšení výkonu](performance-tips.md)a řiďte se doporučenými postupy. To vám pomůže zabránit škálování, latenci a dalším problémům s výkonem.
*    Povolení protokolování sady SDK, které vám může pomoct vyřešit problém. Povolení protokolování může mít vliv na výkon, takže je nejvhodnější ho povolit jenom při řešení problémů. Můžete povolit následující protokoly:
*    [Metriky protokolu](./monitor-cosmos-db.md) pomocí Azure Portal. Metriky portálu ukazují Azure Cosmos DB telemetrii, což je užitečné, pokud chcete zjistit, jestli tento problém odpovídá Azure Cosmos DB nebo pokud je ze strany klienta.
*    Protokoluje [řetězec diagnostiky](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) v sadě v2 SDK nebo v [Diagnostics](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) v sadě V3 SDK z odpovědí na operace bodu.
*    Protokoluje [metriky dotazů SQL](sql-api-query-metrics.md) ze všech odpovědí na dotazy. 
*    Postupujte podle pokynů pro nastavení [protokolování sady SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md) .

Prohlédněte si část [běžné problémy a alternativní řešení](#common-issues-workarounds) v tomto článku.

Podívejte se na [část problémy GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) , která je aktivně monitorována. Zkontrolujte, zda je již archivován případný podobný problém s alternativním řešením. Pokud jste nenalezli řešení, zapište problém GitHubu. Můžete otevřít značku podpory pro naléhavé problémy.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Běžné problémy a alternativní řešení

### <a name="general-suggestions"></a>Obecné návrhy
* Pokud je to možné, spouštějte svou aplikaci ve stejné oblasti Azure jako účet Azure Cosmos DB. 
* V důsledku nedostatku prostředků na klientském počítači se můžete setkat s problémy s připojením nebo dostupností. Doporučujeme monitorovat využití CPU na uzlech, na kterých běží klient Azure Cosmos DB, a při vysokém zatížení se škálováním na více instancí.

### <a name="check-the-portal-metrics"></a>Kontrolovat metriky portálu
Kontrola [metrik portálu](./monitor-cosmos-db.md) vám pomůže určit, jestli se jedná o problém na straně klienta, nebo jestli došlo k potížím se službou. Pokud například metriky obsahují vysokou míru omezeného počtu požadavků (kód stavu HTTP 429), což znamená, že požadavek je omezený, zkontrolujte, že je [Počet požadavků příliš velký](troubleshoot-request-rate-too-large.md) . 

## <a name="common-error-status-codes"></a>Běžné chybové kódy stavu <a id="error-codes"></a>

| Stavový kód | Description | 
|----------|-------------|
| 400 | Chybný požadavek (závisí na chybové zprávě)| 
| 401 | [Neautorizováno](troubleshoot-unauthorized.md) | 
| 404 | [Prostředek se nenašel.](troubleshoot-not-found.md) |
| 408 | [Vypršel časový limit žádosti.](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | Selhání konfliktu nastane, pokud je ID poskytnuté pro prostředek operace zápisu pořízeno existujícím prostředkem. K vyřešení tohoto problému použijte jiné ID prostředku, protože ID musí být jedinečné ve všech dokumentech se stejnou hodnotou klíče oddílu. |
| 410 | Zmizely výjimky (přechodná chyba, která by neměla porušovat smlouvu SLA) |
| 412 | Podmínkou chyby je, že operace určila eTag, která se liší od verze, která je k dispozici na serveru. Je to optimistická chyba souběžnosti. Po načtení nejnovější verze prostředku a aktualizaci značky ETag v požadavku zkuste požadavek zopakovat.
| 413 | [Entita požadavku je příliš velká.](concepts-limits.md#per-item-limits) |
| 429 | [Příliš mnoho žádostí](troubleshoot-request-rate-too-large.md) |
| 449 | Přechodná chyba, ke které dochází pouze při operacích zápisu a je bezpečné ji opakovat |
| 500 | Operace se nezdařila z důvodu neočekávané chyby služby. Obraťte se na podporu. Viz část podání [problému podpory Azure](https://aka.ms/azure-support). |
| 503 | [Nedostupná služba](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Vyčerpání portů Azure SNAT (PAT)

Pokud je vaše aplikace nasazená v [Azure Virtual Machines bez veřejné IP adresy](../load-balancer/load-balancer-outbound-connections.md), ve výchozím nastavení [porty Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) naváže připojení ke koncovému bodu mimo váš virtuální počítač. Počet připojení povolených z virtuálního počítače do koncového bodu Azure Cosmos DB je omezen [konfigurací Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Tato situace může vést k omezení připojení, ukončení připojení nebo výše uvedeným [časovým limitům požadavků](troubleshoot-dot-net-sdk-request-timeout.md).

 Porty Azure SNAT se používají jenom v případě, že váš virtuální počítač má privátní IP adresu, která se připojuje k veřejné IP adrese. Omezení Azure SNAT (za předpokladu, že už v celé aplikaci používáte jednu instanci klienta) se vyhnete dvěma řešením:

* Přidejte koncový bod služby Azure Cosmos DB do podsítě vaší virtuální sítě Azure Virtual Machines. Další informace najdete v tématu [koncové body služby Azure Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Pokud je povolen koncový bod služby, žádosti již nejsou odesílány z veřejné IP adresy do Azure Cosmos DB. Místo toho se pošle identita virtuální sítě a podsítě. Tato změna může vést k poklesu brány firewall, pokud jsou povolené jenom veřejné IP adresy. Pokud používáte bránu firewall a povolíte koncový bod služby, přidejte do brány firewall podsíť pomocí [Virtual Network ACL](/previous-versions/azure/virtual-network/virtual-networks-acl).
* Přiřaďte [k virtuálnímu počítači Azure veřejnou IP adresu](../load-balancer/troubleshoot-outbound-connection.md#assignilpip).

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Vysoká latence sítě
Vysoká latence sítě se dá identifikovat pomocí [diagnostického řetězce](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?preserve-view=true&view=azure-dotnet) v sadě v2 SDK nebo v [Diagnostics](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?preserve-view=true&view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) v sadě V3 SDK.

Pokud nejsou k dispozici žádné [časové limity](troubleshoot-dot-net-sdk-request-timeout.md) a diagnostika zobrazí jednotlivé požadavky, u kterých je vysoká latence zřejmá na rozdíl mezi `ResponseTime` a `RequestStartTime` , například (>300 milisekund v tomto příkladu):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Tato latence může mít několik příčin:

* Vaše aplikace neběží ve stejné oblasti jako váš Azure Cosmos DB účet.
* Vaše konfigurace [PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) nebo [ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) není správná a pokouší se připojit k jiné oblasti, kde je aplikace momentálně spuštěná.
* Kvůli vysokému provozu může být síťové rozhraní v kritickém případě. Pokud aplikace běží na Azure Virtual Machines, existují možná alternativní řešení:
    * Zvažte použití [virtuálního počítače s povolenými akcelerovanými síťovými](../virtual-network/create-vm-accelerated-networking-powershell.md)službami.
    * Povolí [akcelerované síťové služby na stávajícím virtuálním počítači](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Zvažte použití [vyššího koncového virtuálního počítače](../virtual-machines/sizes.md).

### <a name="common-query-issues"></a>Běžné problémy s dotazy

[Metrika dotazu](sql-api-query-metrics.md) vám pomůže určit, kde je dotaz ve většině případů útrat. Z metriky dotazu vidíte, kolik z nich je stráveno na back-endu a klientovi. Přečtěte si další informace o [řešení potíží s výkonem dotazů](troubleshoot-query-performance.md).

* Pokud se back-end dotaz rychle vrátí, a při kontrole zatížení počítače stráví na klientovi velký čas. Pravděpodobně není dostatek prostředků a sada SDK čeká, až budou prostředky k dispozici pro zpracování odpovědi.
* Pokud je back-end dotaz pomalý, zkuste [optimalizovat dotaz](troubleshoot-query-performance.md) a podívat se na aktuální [zásady indexování](index-overview.md) .

    > [!NOTE]
    > Pro lepší výkon doporučujeme zpracování bitového hostitelského systému Windows 64. Sada SQL SDK obsahuje nativní ServiceInterop.dll k analýze a optimalizaci dotazů v místním prostředí. ServiceInterop.dll se podporuje jenom na platformě Windows x64. Pro Linux a jiné nepodporované platformy, kde ServiceInterop.dll není k dispozici, se k získání optimalizovaného dotazu provede další síťové volání brány.

Pokud narazíte na následující chybu: `Unable to load DLL 'Microsoft.Azure.Cosmos.ServiceInterop.dll' or one of its dependencies:` a používáte Windows, měli byste upgradovat na nejnovější verzi Windows.

## <a name="next-steps"></a>Další kroky

* Informace o zásadách výkonu pro [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) a [.NET v2](performance-tips.md)
* Další informace o sadách [Java SDK založených na reaktorech](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list