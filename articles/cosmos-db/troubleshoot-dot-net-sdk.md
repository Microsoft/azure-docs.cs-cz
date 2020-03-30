---
title: Diagnostika a řešení potíží při používání sady .NET SDK služby Azure Cosmos DB
description: Pomocí funkcí, jako je protokolování na straně klienta a další nástroje třetích stran k identifikaci, diagnostiku a řešení problémů Azure Cosmos DB při použití .NET SDK.
author: j82w
ms.service: cosmos-db
ms.date: 03/11/2020
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5f92d98630c6fb875babeb907f92732b0c24bb52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137950"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnostika a řešení potíží při používání sady .NET SDK služby Azure Cosmos DB
Tento článek popisuje běžné problémy, řešení, diagnostické kroky a nástroje při použití [sady .NET SDK](sql-api-sdk-dotnet.md) s účty Azure Cosmos DB SQL API.
Sada .NET SDK poskytuje logickou reprezentaci na straně klienta pro přístup k rozhraní SQL API Azure Cosmos DB. Tento článek popisuje nástroje a přístupy, které vám pomůžou v případě jakýchkoli problémů.

## <a name="checklist-for-troubleshooting-issues"></a>Kontrolní seznam pro řešení problémů:
Před přesunutím aplikace do produkčního prostředí zvažte následující kontrolní seznam. Použití kontrolního seznamu zabrání několika běžným problémům, které se mohou zobrazit. Můžete také rychle diagnostikovat, když dojde k problému:

*    Použijte nejnovější [sadu SDK](sql-api-sdk-dotnet-standard.md). Náhled sady SDK by neměly být používány pro produkční prostředí. Tím zabráníte zasažení známých problémů, které jsou již opraveny.
*    Projděte si tipy pro [výkon](performance-tips.md)a postupujte podle doporučených postupů. To pomůže zabránit škálování, latence a další problémy s výkonem.
*    Povolte protokolování sady SDK, které vám pomůže vyřešit problém. Povolení protokolování může ovlivnit výkon, takže je nejlepší povolit pouze při řešení problémů. Můžete povolit následující protokoly:
    *    [Protokolovat metriky](monitor-accounts.md) pomocí portálu Azure. Portál metriky zobrazit Azure Cosmos DB telemetrie, což je užitečné k určení, pokud problém odpovídá Azure Cosmos DB nebo pokud je ze strany klienta.
    *    Protokolovat [řetězec diagnostiky](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) v v2 SDK nebo [diagnostiky](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) v V3 SDK z odezvy operace bodu.
    *    Protokolovat [metriky dotazu SQL](sql-api-query-metrics.md) ze všech odpovědí na dotaz 
    *    Postupujte podle nastavení [pro protokolování sady SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Podívejte se na [běžné problémy a řešení](#common-issues-workarounds) části v tomto článku.

Podívejte se na [část problémů GitHubu,](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) která je aktivně monitorovaná. Zkontrolujte, zda je podobný problém s tímto zástupným tématem již podán. Pokud jste nenašli řešení, pak soubor GitHub problém. Můžete otevřít podporu klíště pro naléhavé problémy.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Běžné problémy a alternativní řešení

### <a name="general-suggestions"></a>Obecné návrhy
* Spusťte aplikaci ve stejné oblasti Azure jako váš účet Azure Cosmos DB, kdykoli je to možné. 
* Můžete spustit problémy s připojením nebo dostupností z důvodu nedostatku prostředků v klientském počítači. Doporučujeme sledovat využití procesoru na uzlech s klientem Azure Cosmos DB a škálování, pokud běží při vysokém zatížení.

### <a name="check-the-portal-metrics"></a>Kontrola metrik portálu
Kontrola [metriky portálu](monitor-accounts.md) pomůže určit, pokud se jedná o problém na straně klienta nebo pokud je problém se službou. Například pokud metriky obsahují vysokou rychlost požadavků s omezenou rychlostí (stavový kód HTTP 429), což znamená, že požadavek je stále omezen, zkontrolujte příliš velkou část [Požadavek.] 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>Požadavky na časové opození
RequestTimeout se obvykle stane při použití Direct/TCP, ale může dojít v režimu brány. Tyto chyby jsou běžné známé příčiny a návrhy, jak problém vyřešit.

* Využití procesoru je vysoká, což způsobí latence nebo časový limit požadavku. Zákazník může navýšit kapacitu hostitelského počítače, aby mu poskytl více prostředků, nebo zatížení může být distribuováno mezi více počítačů.
* Dostupnost soketu / portu může být nízká. Když běží v Azure, klienti pomocí .NET SDK můžete zasáhnout Azure SNAT (PAT) vyčerpání portu. Chcete-li snížit pravděpodobnost výskytu tohoto problému, použijte nejnovější verzi 2.x nebo 3.x sady .NET SDK. Toto je příklad, proč se doporučuje vždy spustit nejnovější verzi sady SDK.
* Vytvoření více instancí DocumentClient může vést k konflikty připojení a problémy s časovým limitem. Postupujte podle [tipů pro výkon](performance-tips.md)a použijte jednu instanci DocumentClient v celém procesu.
* Uživatelé někdy zobrazit zvýšené latence nebo časové limity požadavku, protože jejich kolekce jsou zřízeny nedostatečně, back-end škrticí akcelerátory požadavky a klient opakuje interně. Podívejte se na [metriky portálu](monitor-accounts.md).
* Azure Cosmos DB distribuuje celkovou zřízenou propustnost rovnoměrně napříč fyzickými oddíly. Zkontrolujte metriky portálu a zjistěte, zda se při práci dochází k klávesové zkratky [.](partition-data.md) To způsobí, že agregátní spotřebované propustnost (RU/s) se zobrazí v rámci zřízených ru, ale jeden oddíl spotřebované propustnost (RU/s) překročí zřízenou propustnost. 
* Sada 2.0 SDK navíc přidává sémantiku kanálu do přímých připojení TCP. Jedno připojení TCP se používá pro více požadavků současně. To může vést ke dvěma problémům v konkrétních případech:
    * Vysoký stupeň souběžnosti může vést k tvrzení na kanálu.
    * Velké požadavky nebo odpovědi může vést k head-of-line blokování na kanálu a zhoršit sváry, a to i při relativně nízký stupeň souběžnosti.
    * Pokud případ spadá do některé z těchto dvou kategorií (nebo pokud existuje podezření na vysoké využití procesoru), jedná se o možné skutečnosti snižující závažnost rizika:
        * Pokuste se škálovat aplikace nahoru / ven.
        * Navíc protokoly sady SDK lze zachytit prostřednictvím [naslouchací proces trasování](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) získat další podrobnosti.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Vysoká latence sítě
Vysoká latence sítě lze identifikovat pomocí [řetězce diagnostiky](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) v v2 SDK nebo [diagnostiky](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) v V3 SDK.

Pokud nejsou k dispozici žádné [časové osy](#request-timeouts) a diagnostika zobrazit jednotlivé požadavky, kde je zřejmá vysoká latence na rozdíl mezi `ResponseTime` a `RequestStartTime`, jako tak (>300 milisekund v tomto příkladu):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Tato latence může mít více příčin:

* Vaše aplikace neběží ve stejné oblasti jako váš účet Azure Cosmos DB.
* Vaše [Upřednostňovací umístění](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) nebo [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) konfigurace je nesprávná a pokouší se připojit k jiné oblasti, kde je aktuálně spuštěna aplikace.
* V síťovém rozhraní může být kritické místo z důvodu vysokého provozu. Pokud je aplikace spuštěná na virtuálních počítačích Azure, existují možná řešení:
    * Zvažte použití [virtuálního počítače s povolenou zrychlenou sítí](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Povolte [akcelerované sítě na existujícím virtuálním počítači](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Zvažte použití [vyššího virtuálního počítače](../virtual-machines/windows/sizes.md).

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Vyčerpání portu Azure SNAT (PAT)

Pokud se vaše aplikace nasadí na [virtuálních počítačích Azure bez veřejné IP adresy](../load-balancer/load-balancer-outbound-connections.md#defaultsnat), ve výchozím nastavení navazují [porty Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) připojení k libovolnému koncovému bodu mimo váš virtuální počítač. Počet připojení povolených z virtuálního počítače do koncového bodu Azure Cosmos DB je omezen [konfigurací Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Tato situace může vést k omezení připojení, uzavření připojení nebo výše uvedené [požadavek časové limity](#request-timeouts).

 Porty Azure SNAT se používají jenom v případě, že váš virtuální počítač má privátní IP adresu se připojuje k veřejné IP adrese. Existují dvě řešení, jak se vyhnout omezení Azure SNAT (za předpokladu, že už používáte jednu instanci klienta v celé aplikaci):

* Přidejte koncový bod služby Azure Cosmos DB do podsítě virtuální sítě Virtuálních počítačů Azure. Další informace najdete v tématu [koncové body služby Azure Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Pokud je koncový bod služby povolen, požadavky se už neodesílají z veřejné IP adresy do Azure Cosmos DB. Místo toho jsou odesílány virtuální sítě a identity podsítě. Tato změna může způsobit, že brána firewall klesne, pokud jsou povoleny pouze veřejné IP adresy. Pokud používáte bránu firewall, při povolení koncového bodu služby přidejte podsíť do brány firewall pomocí [seznamů ACL virtuální sítě](../virtual-network/virtual-networks-acl.md).
* Přiřaďte [k virtuálnímu počítači Azure veřejnou IP adresu](../load-balancer/load-balancer-outbound-connections.md#assignilpip).

### <a name="http-proxy"></a>Proxy server HTTP
Pokud používáte proxy server HTTP, ujistěte se, že podporuje `ConnectionPolicy`počet připojení nakonfigurovaných v sadě SDK .
V opačném případě budete čelit problémům s připojením.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>Příliš velká míra požadavků
"Příliš velká míra požadavků" nebo kód chyby 429 označuje, že vaše požadavky jsou omezeny, protože spotřebovaná propustnost (RU/s) [překročila zřízenou propustnost](set-throughput.md). Sada SDK automaticky zopakují požadavky na základě zadané [zásady opakování](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Pokud se často dostanete k této chybě, zvažte zvýšení propustnost v kolekci. Podívejte se na [metriky portálu](use-metrics.md) a zjistěte, zda se vám zobrazuje 429 chyb. Zkontrolujte [klíč oddílu,](partitioning-overview.md#choose-partitionkey) abyste se ujistili, že výsledkem je rovnoměrná distribuce úložiště a objemu požadavků. 

### <a name="slow-query-performance"></a>Pomalý výkon dotazu
[Metriky dotazu](sql-api-query-metrics.md) vám pomohou určit, kde dotaz tráví většinu času. Z metriky dotazu můžete vidět, kolik z toho se vynakládá na back-end vs klienta.
* Pokud se dotaz back-end vrátí rychle a stráví velký čas na klienta zkontrolujte zatížení v počítači. Je pravděpodobné, že není dostatek prostředků a sada SDK čeká na prostředky, které mají být k dispozici pro zpracování odpovědi.
* Pokud je dotaz back-end pomalý, zkuste [dotaz optimalizovat](optimize-cost-queries.md) a podívat se na aktuální [zásady indexování](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Příliš velká míra požadavků]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list


