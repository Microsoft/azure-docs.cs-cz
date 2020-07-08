---
title: Diagnostika a řešení potíží při používání sady .NET SDK služby Azure Cosmos DB
description: K identifikaci, diagnostice a řešení potíží s Azure Cosmos DB při použití sady .NET SDK použijte funkce, jako je protokolování na straně klienta a další nástroje třetích stran.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/16/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0eb5d9cd86be05e5ad69bc9543231987e3c1dd2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85799261"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnostika a řešení potíží při používání sady .NET SDK služby Azure Cosmos DB

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
*    [Metriky protokolu](monitor-accounts.md) pomocí Azure Portal. Metriky portálu ukazují Azure Cosmos DB telemetrii, což je užitečné, pokud chcete zjistit, jestli tento problém odpovídá Azure Cosmos DB nebo pokud je ze strany klienta.
*    Protokoluje [řetězec diagnostiky](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) v sadě v2 SDK nebo v [Diagnostics](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) v sadě V3 SDK z odpovědí na operace bodu.
*    Protokoluje [metriky dotazů SQL](sql-api-query-metrics.md) ze všech odpovědí na dotazy. 
*    Postupujte podle pokynů pro nastavení [protokolování sady SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md) .

Prohlédněte si část [běžné problémy a alternativní řešení](#common-issues-workarounds) v tomto článku.

Podívejte se na [část problémy GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) , která je aktivně monitorována. Zkontrolujte, zda je již archivován případný podobný problém s alternativním řešením. Pokud jste nenalezli řešení, zapište problém GitHubu. Můžete otevřít značku podpory pro naléhavé problémy.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Běžné problémy a alternativní řešení

### <a name="general-suggestions"></a>Obecné návrhy
* Pokud je to možné, spouštějte svou aplikaci ve stejné oblasti Azure jako účet Azure Cosmos DB. 
* V důsledku nedostatku prostředků na klientském počítači se můžete setkat s problémy s připojením nebo dostupností. Doporučujeme monitorovat využití CPU na uzlech, na kterých běží klient Azure Cosmos DB, a při vysokém zatížení se škálováním na více instancí.

### <a name="check-the-portal-metrics"></a>Kontrolovat metriky portálu
Kontrola [metrik portálu](monitor-accounts.md) vám pomůže určit, jestli se jedná o problém na straně klienta, nebo jestli došlo k potížím se službou. Pokud například metriky obsahují vysokou míru omezeného počtu požadavků (kód stavu HTTP 429), což znamená, že požadavek je omezený, zkontrolujte, že je [Počet požadavků příliš velký] . 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>Vypršení časových limitů požadavků
K RequestTimeout obvykle dochází při použití Direct/TCP, ale může se stát v režimu brány. Tyto chyby jsou běžné známé příčiny a návrhy, jak problém vyřešit.

* Využití procesoru je vysoké, což způsobí latenci nebo časový limit požadavku. Zákazník může škálovat hostitelský počítač, aby získal více prostředků, nebo může být zatížení distribuováno do více počítačů.
* Dostupnost soketu/portu může být nízká. Při spuštění v Azure můžou klienti, kteří používají .NET SDK, dosáhnout vyčerpání portů Azure SNAT (PAT). Chcete-li snížit riziko, že se na tento problém zasáhne, použijte nejnovější verzi 2. x nebo 3. x sady .NET SDK. Toto je příklad, proč se doporučuje vždy spustit nejnovější verzi sady SDK.
* Vytvoření více instancí DocumentClient může vést k potížím s vypršením sporů a s časovým limitem. Postupujte podle [tipů ke zvýšení výkonu](performance-tips.md)a v celém procesu použijte jednu instanci DocumentClient.
* Uživatelům se někdy zobrazuje zvýšené latence nebo časový limit požadavku, protože jejich kolekce jsou nedostatečně zřízené, požadavky na omezení back-endu a interní opakované pokusy klienta. Ověřte [metriky portálu](monitor-accounts.md).
* Azure Cosmos DB distribuuje celkovou zřízenou propustnost rovnoměrně napříč fyzickými oddíly. Zkontrolujte metriky portálu, abyste zjistili, jestli úloha nenalezne [klíč oddílu](partition-data.md)s příchodem. Tato akce způsobí, že agregovaná spotřebovaný propustnost (RU/s) bude pravděpodobně pod zřízeným Ruem, ale propustnost (RU/s), která byla využita v jednom oddílu, bude přesáhnout zřízenou propustnost. 
* Sada SDK 2,0 navíc přidává sémantiku kanálu k přímým nebo TCP připojením. Jedno připojení TCP se používá pro více požadavků současně. To může v určitých případech vést k dvěma problémům:
    * Vysoká míra souběžnosti může vést k kolizí na kanálu.
    * Velké žádosti nebo odpovědi mohou vést k blokování na úrovni řádků a exacerbate, a to i s poměrně nízkou mírou souběžnosti.
    * Pokud případ spadá do některé z těchto dvou kategorií (nebo pokud je podezření na vysoké využití procesoru), jsou to možné zmírnění:
        * Zkuste aplikaci škálovat nebo zmenšit.
        * Kromě toho se protokoly SDK dají zachytit prostřednictvím [naslouchacího procesu trasování](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) a získat tak další podrobnosti.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Vysoká latence sítě
Vysoká latence sítě se dá identifikovat pomocí [diagnostického řetězce](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) v sadě v2 SDK nebo v [Diagnostics](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) v sadě V3 SDK.

Pokud nejsou k dispozici žádné [časové limity](#request-timeouts) a diagnostika zobrazí jednotlivé požadavky, u kterých je vysoká latence zřejmá na rozdíl mezi `ResponseTime` a `RequestStartTime` , například (>300 milisekund v tomto příkladu):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Tato latence může mít několik příčin:

* Vaše aplikace neběží ve stejné oblasti jako váš Azure Cosmos DB účet.
* Vaše konfigurace [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) nebo [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) není správná a pokouší se připojit k jiné oblasti, kde je aplikace momentálně spuštěná.
* Kvůli vysokému provozu může být síťové rozhraní v kritickém případě. Pokud aplikace běží na Azure Virtual Machines, existují možná alternativní řešení:
    * Zvažte použití [virtuálního počítače s povolenými akcelerovanými síťovými](../virtual-network/create-vm-accelerated-networking-powershell.md)službami.
    * Povolí [akcelerované síťové služby na stávajícím virtuálním počítači](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Zvažte použití [vyššího koncového virtuálního počítače](../virtual-machines/windows/sizes.md).

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Vyčerpání portů Azure SNAT (PAT)

Pokud je vaše aplikace nasazená v [Azure Virtual Machines bez veřejné IP adresy](../load-balancer/load-balancer-outbound-connections.md), ve výchozím nastavení [porty Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) naváže připojení ke koncovému bodu mimo váš virtuální počítač. Počet připojení povolených z virtuálního počítače do koncového bodu Azure Cosmos DB je omezen [konfigurací Azure SNAT](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Tato situace může vést k omezení připojení, ukončení připojení nebo výše uvedeným [časovým limitům požadavků](#request-timeouts).

 Porty Azure SNAT se používají jenom v případě, že váš virtuální počítač má privátní IP adresu, která se připojuje k veřejné IP adrese. Omezení Azure SNAT (za předpokladu, že už v celé aplikaci používáte jednu instanci klienta) se vyhnete dvěma řešením:

* Přidejte koncový bod služby Azure Cosmos DB do podsítě vaší virtuální sítě Azure Virtual Machines. Další informace najdete v tématu [koncové body služby Azure Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Pokud je povolen koncový bod služby, žádosti již nejsou odesílány z veřejné IP adresy do Azure Cosmos DB. Místo toho se pošle identita virtuální sítě a podsítě. Tato změna může vést k poklesu brány firewall, pokud jsou povolené jenom veřejné IP adresy. Pokud používáte bránu firewall a povolíte koncový bod služby, přidejte do brány firewall podsíť pomocí [Virtual Network ACL](../virtual-network/virtual-networks-acl.md).
* Přiřaďte [k virtuálnímu počítači Azure veřejnou IP adresu](../load-balancer/troubleshoot-outbound-connection.md#assignilpip).

### <a name="http-proxy"></a>Proxy server HTTP
Pokud používáte proxy server HTTP, ujistěte se, že může podporovat počet připojení nakonfigurovaných v sadě SDK `ConnectionPolicy` .
Jinak čelíte problémům s připojením.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>Příliš velký počet požadavků
"Počet požadavků je moc velký" nebo kód chyby 429 označuje, že vaše požadavky jsou omezené, protože spotřebované propustnosti (RU/s) překročila [zřízenou propustnost](set-throughput.md). Sada SDK bude automaticky opakovat požadavky na základě zadaných [zásad opakování](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Pokud se toto selhání často dostanou, zvažte zvýšení propustnosti kolekce. Zkontrolujte [metriky na portálu](use-metrics.md) , abyste viděli, jestli se vám zobrazují chyby 429. Zkontrolujte [klíč oddílu](partitioning-overview.md#choose-partitionkey) a ujistěte se, že je výsledkem ještě distribuce úložiště a objemu požadavků. 

### <a name="slow-query-performance"></a>Pomalý výkon dotazů
[Metrika dotazu](sql-api-query-metrics.md) vám pomůže určit, kde je dotaz ve většině případů útrat. Z metriky dotazu vidíte, kolik z nich je stráveno na back-endu a klientovi.
* Pokud se back-end dotaz rychle vrátí, a při kontrole zatížení počítače stráví na klientovi velký čas. Pravděpodobně není dostatek prostředků a sada SDK čeká, až budou prostředky k dispozici pro zpracování odpovědi.
* Pokud je back-endové dotaz pomalý, zkuste [optimalizovat dotaz](optimize-cost-queries.md) a podívat se na aktuální [zásady indexování](index-overview.md) . 

### <a name="http-401-the-mac-signature-found-in-the-http-request-is-not-the-same-as-the-computed-signature"></a>HTTP 401: podpis MAC nalezený v požadavku HTTP není stejný jako vypočítaný podpis.
Pokud se vám zobrazila následující chybová zpráva 401: Podpis MAC v požadavku HTTP neodpovídá vypočítanému podpisu, může to být způsobeno následujícími scénáři.

1. Došlo k obměně klíče, která se neřídila [osvědčenými postupy](secure-access-to-data.md#key-rotation). Toto je obvyklá příčina. Obměna klíčů účtu Cosmos DB může v závislosti na velikosti účtu Cosmos DB trvat od několika sekund až po několik dnů.
   1. K chybě 401 kvůli podpisu MAC dochází krátce po obměně klíčů a nakonec k ní přestane docházet bez nutnosti provádět jakékoli změny. 
1. Klíč je v aplikaci chybně nakonfigurovaný, takže neodpovídá účtu.
   1. K problému s chybou 401 kvůli podpisu MAC bude docházet konzistentně u všech volání.
1. Aplikace používá [klíče jen pro čtení](secure-access-to-data.md#master-keys) pro operace zápisu.
   1. K problému s chybou 401 kvůli podpisu MAC dojde pouze tehdy, když aplikace bude odesílat požadavky na zápis, ale požadavky na čtení proběhnou úspěšně.
1. Při vytváření kontejneru dochází ke konfliktu časování. Instance aplikace se pokouší získat přístup ke kontejneru před dokončením jeho vytváření. Nejběžnějším scénářem je, když je aplikace spuštěná a přitom dojde k odstranění kontejneru a jeho opětovnému vytvoření se stejným názvem. Sada SDK se pokusí použít nový kontejner, ale vytváření kontejneru stále probíhá, takže nemá klíče.
   1. K problému s chybou 401 kvůli podpisu MAC dochází krátce po vytvoření kontejneru a trvá pouze do dokončení vytváření kontejneru.
 
 ### <a name="http-error-400-the-size-of-the-request-headers-is-too-long"></a>Chyba protokolu HTTP 400. Velikost hlaviček požadavku je příliš dlouhá.
 Velikost záhlaví se zvětšila na velkou a překračuje maximální povolenou velikost. Vždycky se doporučuje použít nejnovější sadu SDK. Ujistěte se, že používáte aspoň verzi [3. x](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/changelog.md) nebo [2. x](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md), která do zprávy výjimky přidá trasování velikosti hlavičky.

Mít
 1. Token relace se zvětšil příliš velký. Token relace roste jako počet oddílů, které se v kontejneru zvyšují.
 2. Token pro pokračování byl nárůst na velká. Různé dotazy budou mít různé velikosti tokenů pro pokračování.
 3. Je to způsobeno kombinací tokenu relace a tokenu pokračování.

Řešení:
   1. Postupujte podle [tipů k výkonu](performance-tips.md) a převeďte aplikaci na režim připojení Direct + TCP. Přímý a TCP nemá omezení velikosti záhlaví, jako je HTTP, což tomuto problému zabrání.
   2. Pokud je token relace příčinou, pak je dočasné zmírnění restartování aplikace. Restartování instance aplikace obnoví token relace. Pokud se výjimky zastaví po restartu, potvrdí to příčinu tokenu relace. Nakonec se zvětší zpátky na velikost, která způsobí výjimku.
   3. Pokud aplikace nemůže být převedena na přímý + TCP a token relace je příčinou, je možné zmírnit jejich zmírnění změnou [úrovně konzistence](consistency-levels.md)klienta. Token relace se používá pouze pro konzistenci relací, což je výchozí hodnota pro Cosmos DB. Žádná jiná úroveň konzistence nebude používat token relace. 
   4. Pokud aplikaci nelze převést na přímý + TCP a token pro pokračování je příčinou, pak zkuste nastavit možnost ResponseContinuationTokenLimitInKb. Možnost lze najít v FeedOptions pro v2 nebo QueryRequestOptions v v3.

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Příliš velký počet požadavků]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
