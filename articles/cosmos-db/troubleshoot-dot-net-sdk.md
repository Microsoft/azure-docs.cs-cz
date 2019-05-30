---
title: Diagnostika a řešení problémů při používání .NET SDK služby Azure Cosmos DB
description: Použití funkcí, jako je protokolování na straně klienta a další nástroje třetích stran k identifikaci, diagnostice a Azure Cosmos DB začít řešit potíže s použitím sady .NET SDK.
author: j82w
ms.service: cosmos-db
ms.date: 05/28/2019
ms.author: jawilley
ms.devlang: c#
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7e48809537acc21edbcf12d299a333df486c258f
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257157"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnostika a řešení problémů při používání .NET SDK služby Azure Cosmos DB
Tento článek popisuje běžné problémy, alternativní řešení, kroky pro diagnostiku a nástroje, při použití [sady .NET SDK](sql-api-sdk-dotnet.md) s účty SQL API služby Azure Cosmos DB.
Sada .NET SDK poskytuje logickou reprezentaci na straně klienta pro přístup k SQL API služby Azure Cosmos DB. Tento článek popisuje nástroje a přístupy k vám, pokud narazíte na případné problémy.

## <a name="checklist-for-troubleshooting-issues"></a>Kontrolní seznam pro řešení potíží:
Zvažte následující kontrolní seznam před přesunutím vaši aplikaci do produkčního prostředí. Použití kontrolního seznamu, nebudou moct několik běžných problémů, které se můžou objevovat. Můžete také rychle diagnostikovat, když dojde k problému:

*   Použít nejnovější [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md). Ve verzi Preview sady SDK by neměl být sloužící k produkčním účelům. To zabrání dosažení známých problémů, které jsou už opravená.
*   Zkontrolujte [tipy ke zvýšení výkonu](performance-tips.md)a dodržujte doporučené postupy zabezpečení. To vám pomůže zabránit škálování, latence a dalších problémů s výkonem.
*   Povolení protokolování sady SDK můžete vyřešit nějaký problém. Povolení protokolování může ovlivnit výkon, takže je vhodné ho povolit pouze při řešení problémů. Můžete povolit tyto protokoly:
    *   [Protokolování metrik](monitor-accounts.md) pomocí webu Azure portal. Portálu metriky ukazují telemetrická data služby Azure Cosmos DB, což je užitečné určit, jestli problém odpovídá ke službě Azure Cosmos DB, nebo pokud je ze strany klienta.
    *   Protokol [diagnostiky řetězec](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) z odpovědí na operaci bodu.
    *   Protokol [metriky dotaz SQL](sql-api-query-metrics.md) z všechny odpovědi na dotaz 
    *   Postupujte podle nastavení [protokolování sady SDK]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Podívejte se na [častých problémů a jejich řešení](#common-issues-workarounds) části v tomto článku.

Zkontrolujte [potíže Githubu](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) , která je aktivně sledována. Zaškrtněte, pokud chcete zobrazit, pokud všechny podobné potíže s řešením je již zařazen. Pokud jste nenašli řešení, pak založte problém na Githubu. Podpora značek pro naléhavé problémy můžete otevřít.


## <a name="common-issues-workarounds"></a>Běžné problémy a řešení

### <a name="general-suggestions"></a>Obecná doporučení
* Spuštění aplikace ve stejné oblasti Azure jako účet služby Azure Cosmos DB, kdykoli je to možné. 
* Můžete jej spustit do problémy s připojením nebo dostupnosti z důvodu nedostatku prostředků v klientském počítači. Doporučujeme vám monitorovat vaše využití procesoru na uzly, které běží klientem služby Azure Cosmos DB a škálování nahoru/vypršení limitu, pokud máte spuštěnou při velkém zatížení.

### <a name="check-the-portal-metrics"></a>Zkontrolujte metriky portálu
Kontroluje [portálu metriky](monitor-accounts.md) vám pomůže určit, jestli je problém na straně klienta, nebo pokud se vyskytl problém se službou. Například pokud metriky obsahovat vysoký počet požadavků míra limited (kód stavu HTTP 429) to znamená, že požadavek na získání omezený zkontrolujte [Frekvence požadavků, které jsou moc velká] oddílu. 

### <a name="request-timeouts"></a>Časový limit žádosti
RequestTimeout obvykle dochází při používání přímé/TCP, ale může nastat v režimu brány. Jsou běžné příčiny a návrhy k vyřešení problému.

* Využití procesoru je vysoké, který bude způsobit, že latence a/nebo požádat o vypršení časového limitu. Odběratele můžete vertikálně navýšit kapacitu hostitelský počítač nabízí další prostředky nebo zatížení je možné distribuovat mezi více počítačů.
* Soket / Port dostupnosti může být malé. Při použití sady .NET SDK vydané dřív než verze 2.0, klienty běžící v Azure by mohla přístupů [Vyčerpání portů Azure SNAT PAT]. Tento příklad proč doporučuje se vždycky spouštět nejnovější verze sady SDK.
* Vytvoření víc instancí DocumentClient může vést k připojení kolize a vypršení časového limitu. Postupujte podle [tipy ke zvýšení výkonu](performance-tips.md)a použijte jednu instanci DocumentClient napříč celým procesem.
* Uživatelé někdy zobrazit se zvýšenými oprávněními latence nebo abychom si vyžádali vypršení časového limitu, protože jejich kolekce se zřizují nedostatečně, back endu, omezí požadavků a klient pokus obnovuje interně bez toto zpřístupnění volajícímu. Zkontrolujte, [portálu metriky](monitor-accounts.md).
* Azure Cosmos DB celkovou zřízenou propustnost rovnoměrně distribuuje mezi všemi fyzickými oddíly. Zkontrolujte portálu metriky, abyste viděli, pokud zatížení je zjištění úrovně hot [klíč oddílu](partition-data.md). To způsobí, že celková propustnost (RU/s) se zobrazí pod zřízených ru spotřebovaných, ale jeden oddíl spotřebované propustnost (RU/s) dojde k překročení zřízené propustnosti. 
* 2.0 SDK kromě toho přidá sémantiku kanálu pro připojení přímo/TCP. Jedno připojení TCP se používá pro více požadavků ve stejnou dobu. To může vést k dva problémy v určitých případech:
    * Vysoký stupeň souběžnosti může vést ke kolizi na kanálu.
    * Velké žádostí a odpovědí může vést k blokování head řádku na kanálu a podněcují kolize, i s relativně nízký stupeň souběžnosti.
    * Pokud tento případ spadá do některé z těchto dvou kategorií (nebo podezření vysoké využití procesoru), jedná se o možná zmírnění rizik:
        * Zkuste škálovat nahoru vstupně-výstupní aplikaci.
        * Kromě toho můžou být zachytávané protokolům SDK [naslouchací proces trasování](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) zobrazíte další podrobnosti.

### <a name="connection-throttling"></a>Omezení šířky pásma připojení
Omezení šířky pásma připojení může dojít kvůli limitu připojení na hostitelském počítači. Vytváření 2.0, může přístupů klienty běžící v Azure [Vyčerpání portů Azure SNAT PAT].

### <a name="snat"></a>Vyčerpání portů Azure SNAT PAT

Pokud vaše aplikace je nasazená ve službě Azure Virtual Machines bez veřejné IP adresy, ve výchozím nastavení [porty Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) k navázání spojení s libovolný koncový bod mimo váš virtuální počítač. Počet připojení povolených z virtuálního počítače do koncového bodu služby Azure Cosmos DB je omezena [konfigurace Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Azure SNAT porty se používají pouze v případě, že váš virtuální počítač má privátní IP adresu a proces z virtuálního počítače se pokusí připojit k veřejné IP adresy. Existují dvě alternativní řešení, aby se zabránilo Azure SNAT omezení:

* Přidáte koncový bod služby Azure Cosmos DB k podsíti virtuální sítě Azure Virtual Machines. Další informace najdete v tématu [koncové body služeb virtuální sítě Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Když je povolený koncový bod služby, žádosti už odesílají z veřejnou IP adresu do služby Azure Cosmos DB. Místo toho jsou odeslány virtuální síť a podsíť identity. Tato změna může vést drops brány firewall, pokud pouze veřejné IP adresy jsou povoleny. Pokud používáte bránu firewall, při povolení koncového bodu služby, přidejte podsíť brány firewall pomocí [virtuální sítě ACL](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Přiřadíte veřejnou IP adresu svého virtuálního počítače Azure.

### <a name="http-proxy"></a>HTTP proxy
Pokud používáte proxy server HTTP, ujistěte se, že počet připojení nakonfigurovaná v sadě SDK, které může podporovat `ConnectionPolicy`.
Jinak kterými se setkáváte problémy s připojením.

### Frekvence požadavků, které jsou moc velká<a name="request-rate-too-large"></a>
'Požádat o rychlost příliš velká' nebo kód chyby 429 označuje, že požadavků se omezují, protože spotřebovaná propustnost (RU/s) překročil zřízenou propustnost. Sada SDK automaticky zopakuje požadavků podle zadaného [zásady opakování](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Pokud se zobrazí tato chyba často, zvažte zvýšení propustnosti na kolekci. Zkontrolujte, [metrik na portálu](use-metrics.md) zobrazíte, pokud se zobrazuje chyby 429. Zkontrolujte vaše [klíč oddílu](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) zajistit, je výsledkem rozdělení objemu úložiště a požadavek. 

### <a name="slow-query-performance"></a>Výkon pomalých dotazů
[Dotazování metrik](sql-api-query-metrics.md) vám pomohou určit, kde dotaz spotřebuje většinu času. V dotazu metriky, můžete zobrazit, nakolik se věnovali vs back-end klienta.
* Pokud je dotaz back-end vrací rychle a stráví velké čas na klientském zkontrolujte zatížení na počítači. Je pravděpodobné, že nejsou k dispozici dostatek prostředků a sady SDK je čekání na prostředky k dispozici pro zpracování odpovědi.
* Pokud back-end dotaz je pomalý zkuste [optimalizaci dotazu](optimize-cost-queries.md) a vyhledávání v aktuální [zásady indexování](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Frekvence požadavků, které jsou moc velká]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Vyčerpání portů Azure SNAT PAT]: #snat
[Production check list]: #production-check-list


