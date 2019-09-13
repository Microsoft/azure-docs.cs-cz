---
title: Diagnostika a řešení potíží při použití Azure Cosmos DB .NET SDK
description: K identifikaci, diagnostice a řešení potíží s Azure Cosmos DB při použití sady .NET SDK použijte funkce, jako je protokolování na straně klienta a další nástroje třetích stran.
author: j82w
ms.service: cosmos-db
ms.date: 05/28/2019
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 51b37c43b94ad59090f32af0d57bbefaa57f30fa
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932563"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnostika a řešení potíží při použití Azure Cosmos DB .NET SDK
Tento článek popisuje běžné problémy, alternativní řešení, diagnostické kroky a nástroje, pokud používáte [sadu .NET SDK](sql-api-sdk-dotnet.md) s Azure Cosmos DB účty rozhraní SQL API.
Sada .NET SDK poskytuje logickou reprezentaci na straně klienta pro přístup k Azure Cosmos DB rozhraní SQL API. Tento článek popisuje nástroje a přístupy, které vám pomůžou při nastavování jakýchkoli problémů.

## <a name="checklist-for-troubleshooting-issues"></a>Kontrolní seznam pro řešení problémů:
Před přesunutím aplikace do produkčního prostředí Vezměte v úvahu následující kontrolní seznam. Při použití kontrolního seznamu se zabrání několik běžných problémů, které byste mohli vidět. Můžete také rychle diagnostikovat situaci, kdy dojde k problému:

*   Použijte nejnovější [sadu SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md). Sady SDK verze Preview by se neměly používat pro produkční prostředí. Zabráníte tak známým problémům, které již byly opraveny.
*   Projděte si [tipy ke zvýšení výkonu](performance-tips.md)a řiďte se doporučenými postupy. To vám pomůže zabránit škálování, latenci a dalším problémům s výkonem.
*   Povolení protokolování sady SDK, které vám může pomoct vyřešit problém. Povolení protokolování může mít vliv na výkon, takže je nejvhodnější ho povolit jenom při řešení problémů. Můžete povolit následující protokoly:
    *   [Metriky protokolu](monitor-accounts.md) pomocí Azure Portal. Metriky portálu ukazují Azure Cosmos DB telemetrii, což je užitečné, pokud chcete zjistit, jestli tento problém odpovídá Azure Cosmos DB nebo pokud je ze strany klienta.
    *   Protokoluje [řetězec diagnostiky](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) z odpovědí na operace bodu.
    *   Protokoluje [metriky dotazů SQL](sql-api-query-metrics.md) ze všech odpovědí na dotazy. 
    *   Postupujte podle pokynů pro nastavení [protokolování sady SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md) .

Prohlédněte si část [běžné problémy a alternativní řešení](#common-issues-workarounds) v tomto článku.

Podívejte se na [část problémy GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) , která je aktivně monitorována. Zkontrolujte, zda je již archivován případný podobný problém s alternativním řešením. Pokud jste nenalezli řešení, zapište problém GitHubu. Můžete otevřít značku podpory pro naléhavé problémy.


## <a name="common-issues-workarounds"></a>Běžné problémy a jejich řešení

### <a name="general-suggestions"></a>Obecné návrhy
* Pokud je to možné, spouštějte svou aplikaci ve stejné oblasti Azure jako účet Azure Cosmos DB. 
* V důsledku nedostatku prostředků na klientském počítači se můžete setkat s problémy s připojením nebo dostupností. Doporučujeme monitorovat využití CPU na uzlech, na kterých běží klient Azure Cosmos DB, a při vysokém zatížení se škálováním na více instancí.

### <a name="check-the-portal-metrics"></a>Kontrolovat metriky portálu
Kontrola [metrik portálu](monitor-accounts.md) vám pomůže určit, jestli se jedná o problém na straně klienta, nebo jestli došlo k potížím se službou. Pokud například metriky obsahují vysokou míru omezeného počtu požadavků (kód stavu HTTP 429), což znamená, že požadavek je omezený, zkontrolujte, zda je počet požadavků [Příliš velký počet požadavků] . 

### <a name="request-timeouts"></a>Vypršení časových limitů požadavků
K RequestTimeout obvykle dochází při použití Direct/TCP, ale může se stát v režimu brány. Jedná se o běžné známé příčiny a návrhy, jak tento problém vyřešit.

* Využití procesoru je vysoké, což způsobí latenci nebo časový limit požadavku. Zákazník může škálovat hostitelský počítač, aby získal více prostředků, nebo může být zatížení distribuováno do více počítačů.
* Dostupnost soketu/portu může být nízká. Při spuštění v Azure můžou klienti, kteří používají .NET SDK, dosáhnout vyčerpání portů Azure SNAT (PAT). Chcete-li snížit riziko, že se na tento problém zasáhne, použijte nejnovější verzi 2. x nebo 3. x sady .NET SDK. Toto je příklad, proč doporučujeme vždy spustit nejnovější verzi sady SDK.
* Vytvoření více instancí DocumentClient může vést k potížím s vypršením sporů a s časovým limitem. Postupujte podle [tipů ke zvýšení výkonu](performance-tips.md)a v celém procesu použijte jednu instanci DocumentClient.
* Uživatelům se někdy zobrazuje latence zvýšené úrovně nebo časový limit požadavku, protože jejich kolekce jsou zřízeny nedostatečně, požadavky na omezení back-endu a klient se interně opakuje, aniž by to zpřístupnění volajícímu. Ověřte [metriky portálu](monitor-accounts.md).
* Azure Cosmos DB distribuuje celkovou zřízenou propustnost rovnoměrně napříč fyzickými oddíly. Zkontrolujte metriky portálu, abyste zjistili, jestli úloha nenalezne [klíč oddílu](partition-data.md)s příchodem. Tato akce způsobí, že agregovaná spotřebovaný propustnost (RU/s) bude pravděpodobně pod zřízeným Ruem, ale propustnost (RU/s), která byla využita v jednom oddílu, bude přesáhnout zřízenou propustnost. 
* Sada SDK 2,0 navíc přidává sémantiku kanálu k přímým nebo TCP připojením. Jedno připojení TCP se používá pro více požadavků současně. To může v určitých případech vést k dvěma problémům:
    * Vysoká míra souběžnosti může vést k kolizí na kanálu.
    * Velké žádosti nebo odpovědi mohou vést k blokování na úrovni řádků a exacerbate, a to i s poměrně nízkou mírou souběžnosti.
    * Pokud případ spadá do některé z těchto dvou kategorií (nebo pokud je podezření na vysoké využití procesoru), jsou to možné zmírnění:
        * Zkuste aplikaci škálovat nebo zmenšit.
        * Kromě toho se protokoly SDK dají zachytit prostřednictvím [naslouchacího procesu trasování](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) a získat tak další podrobnosti.

### <a name="connection-throttling"></a>Omezování připojení
Omezení připojení může nastat kvůli omezení počtu připojení na hostitelském počítači. Předchozí až 2,0 mohli klienti běžící v Azure dosáhnout [vyčerpání portů Azure SNAT (Pat)].

### <a name="snat"></a>Vyčerpání portů Azure SNAT (PAT)

Pokud je vaše aplikace nasazená v Azure Virtual Machines bez veřejné IP adresy, ve výchozím nastavení [porty Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) naváže připojení ke koncovému bodu mimo váš virtuální počítač. Počet připojení povolených z virtuálního počítače do koncového bodu Azure Cosmos DB je omezen [konfigurací Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Porty Azure SNAT se používají jenom v případě, že váš virtuální počítač má privátní IP adresu a proces z virtuálního počítače se pokusí připojit k veřejné IP adrese. Omezení Azure SNAT se vyhnete dvěma řešením:

* Přidejte koncový bod služby Azure Cosmos DB do podsítě vaší virtuální sítě Azure Virtual Machines. Další informace najdete v tématu [koncové body služby Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Pokud je povolen koncový bod služby, žádosti již nejsou odesílány z veřejné IP adresy do Azure Cosmos DB. Místo toho se pošle identita virtuální sítě a podsítě. Tato změna může vést k poklesu brány firewall, pokud jsou povolené jenom veřejné IP adresy. Pokud používáte bránu firewall a povolíte koncový bod služby, přidejte do brány firewall podsíť pomocí [Virtual Network ACL](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Přiřaďte k VIRTUÁLNÍmu počítači Azure veřejnou IP adresu.

### <a name="http-proxy"></a>Proxy server HTTP
Pokud používáte proxy server HTTP, ujistěte se, že může podporovat počet připojení nakonfigurovaných v sadě SDK `ConnectionPolicy`.
Jinak čelíte problémům s připojením.

### Příliš velký počet požadavků<a name="request-rate-too-large"></a>
"Počet požadavků je moc velký" nebo kód chyby 429 označuje, že vaše požadavky jsou omezené, protože spotřebované propustnosti (RU/s) překročila zřízenou propustnost. Sada SDK bude automaticky opakovat požadavky na základě zadaných [zásad opakování](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Pokud se toto selhání často dostanou, zvažte zvýšení propustnosti kolekce. Zkontrolujte [metriky na portálu](use-metrics.md) , abyste viděli, jestli se vám zobrazují chyby 429. Zkontrolujte [klíč oddílu](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) a ujistěte se, že je výsledkem ještě distribuce úložiště a objemu požadavků. 

### <a name="slow-query-performance"></a>Pomalý výkon dotazů
[Metrika dotazu](sql-api-query-metrics.md) vám pomůže určit, kde je dotaz ve většině případů útrat. Z metriky dotazu vidíte, kolik z nich je stráveno na back-endu a klientovi.
* Pokud se back-end dotaz rychle vrátí, a při kontrole zatížení počítače stráví na klientovi velký čas. Pravděpodobně není dostatek prostředků a sada SDK čeká, až budou prostředky k dispozici pro zpracování odpovědi.
* Pokud je back-endové dotaz pomalý, zkuste [optimalizovat dotaz](optimize-cost-queries.md) a podívat se na aktuální [zásady indexování](index-overview.md) . 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Příliš velký počet požadavků]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Vyčerpání portů Azure SNAT (PAT)]: #snat
[Production check list]: #production-check-list


