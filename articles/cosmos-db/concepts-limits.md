---
title: Kvóty služby Azure Cosmos DB
description: Azure Cosmos DB kvóty služeb a výchozí omezení pro různé typy prostředků.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: e67346eb1a0fccc7a788e8698df734536e1e395b
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708947"
---
# <a name="azure-cosmos-db-service-quotas"></a>Kvóty služby Azure Cosmos DB

Tento článek obsahuje přehled výchozích kvót nabízených pro různé prostředky ve službě Azure Cosmos DB.

## <a name="storage-and-database-operations"></a>Operace úložiště a databáze

Po vytvoření účtu Azure Cosmos v rámci svého předplatného můžete spravovat data v účtu [vytvořením databází, kontejnerů a položek](databases-containers-items.md).

### <a name="provisioned-throughput"></a>Zřízená propustnost

Propustnost můžete zřídit na úrovni kontejneru nebo databáze na úrovni jednotlivých [jednotek požadavků (ru/s nebo ru)](request-units.md). V následující tabulce jsou uvedeny limity pro úložiště a propustnost na jeden kontejner nebo databázi.

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální počet ru na kontejner ([zřízený režim vyhrazené propustnosti](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 ve výchozím nastavení. Můžete ho rozšířit [podáním lístku podpory Azure](create-support-request-quota-increase.md) . |
| Maximální počet ru na databázi ([zřízený režim sdílené propustnosti](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 ve výchozím nastavení. Můžete ho rozšířit [podáním lístku podpory Azure](create-support-request-quota-increase.md) . |
| Maximální počet ru na (logický) oddíl | 10 000 |
| Maximální velikost úložiště napříč všemi položkami na (logický) oddíl | 20 GB |
| Maximální počet různých (logických) klíčů oddílu | Unlimited |
| Maximální velikost úložiště na kontejner | Unlimited |
| Maximální velikost úložiště na databázi | Unlimited |
| Maximální velikost přílohy na účet (funkce přílohy se už nepoužívá) | 2 GB |
| Minimální ru vyžadovaná za 1 GB | 10 RU/s |

> [!NOTE]
> Další informace o osvědčených postupech pro správu úloh, které mají klíče oddílů vyžadující vyšší limity pro úložiště nebo propustnost, najdete v tématu [Vytvoření syntetického klíče oddílu](synthetic-partition-keys.md).

Cosmos kontejner (nebo sdílená databáze propustnosti) musí mít minimální propustnost 400 RU/s. Při zvětšování kontejneru závisí minimální podporovaná propustnost i na následujících faktorech:

* Maximální propustnost, která byla v kontejneru trvale zřízena. Pokud se například propustnost zvýšila na 50 000 RU/s, pak nejnižší možná zajištěná propustnost bude 500 RU/s.
* Aktuální úložiště v GB v kontejneru. Pokud má váš kontejner například 100 GB úložiště, bude nejnižší možná zajištěná propustnost 1000 RU/s.
* Minimální propustnost ve sdílené databázi propustnosti také závisí na celkovém počtu kontejnerů, které jste dříve vytvořili ve sdílené databázi propustnosti, měřeno v 100 RU/s na jeden kontejner. Pokud jste například vytvořili pět kontejnerů v rámci sdílené databáze propustnosti, musí být propustnost alespoň 500 RU/s.

Aktuální a minimální propustnost kontejneru nebo databáze lze načíst z Azure Portal nebo sad SDK. Další informace najdete v tématu [zřízení propustnosti u kontejnerů a databází](set-throughput.md). 

> [!NOTE]
> V některých případech může být možné snížit propustnost na méně než 10%. Použijte rozhraní API k získání přesného minima ru na kontejner.

V části Souhrn jsou zde uvedená minimální omezení pro podávání RU. 

| Prostředek | Výchozí omezení |
| --- | --- |
| Minimální počet ru na kontejner ([zřízený režim vyhrazené propustnosti](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimální počet ru na databázi ([zřízený režim sdílené propustnosti](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimální počet ru na kontejner v rámci sdílené databáze propustnosti | 100 |

Cosmos DB podporuje Elastické škálování propustnosti (ru) na kontejner nebo databázi prostřednictvím sad SDK nebo portálu. Každý kontejner se může synchronně a hned škálovat v rozmezí od 10 do 100 časů mezi minimální a maximální hodnotou. Pokud je požadovaná hodnota propustnosti mimo rozsah, škálování se provádí asynchronně. Asynchronní škálování může trvat několik minut, než se dokončí v závislosti na požadované propustnosti a velikosti úložiště dat v kontejneru.  

### <a name="serverless"></a>Bez serveru

Bez [serveru](serverless.md) můžete používat prostředky Azure Cosmos DB v závislosti na spotřebě. Následující tabulka uvádí omezení pro zatížení úložiště a propustnosti na kontejner nebo databázi.

| Prostředek | Omezení |
| --- | --- |
| Maximální počet RU/s na kontejner | 5 000 |
| Maximální počet RU/s na (logický) oddíl | 5 000 |
| Maximální velikost úložiště napříč všemi položkami na (logický) oddíl | 20 GB |
| Maximální počet různých (logických) klíčů oddílu | Unlimited |
| Maximální velikost úložiště na kontejner | 50 GB |

## <a name="control-plane-operations"></a>Řízení operací roviny

[Účet Azure Cosmos můžete zřídit a spravovat](how-to-manage-database-account.md) pomocí šablon Azure Portal, Azure PowerShell, Azure CLI a Azure Resource Manager. V následující tabulce jsou uvedené limity pro každé předplatné, účet a počet operací.

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální počet účtů databáze na předplatné | 50 ve výchozím nastavení. Můžete ho rozšířit [podáním lístku podpory Azure](create-support-request-quota-increase.md) .|
| Maximální počet regionálních převzetí služeb při selhání | ve výchozím nastavení 1 hodina. Můžete ho rozšířit [podáním lístku podpory Azure](create-support-request-quota-increase.md) .|

> [!NOTE]
> Regionální převzetí služeb při selhání platí jenom pro jeden region zápis účtů. Účty pro zápis ve více oblastech nevyžadují nebo nemají omezení pro změnu oblasti zápisu.

Cosmos DB automaticky provede zálohování vašich dat v pravidelných intervalech. Podrobnosti o intervalech uchovávání záloh a Windows najdete [v tématu obnovení dat online zálohování a na vyžádání v Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Omezení podle účtů

### <a name="provisioned-throughput"></a>Zřízená propustnost

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální počet databází | Unlimited |
| Maximální počet kontejnerů na databázi se sdílenou propustností |25 |
| Maximální počet kontejnerů na databázi nebo účet s vyhrazenou propustností  |Bez omezení |
| Maximální počet oblastí | Bez omezení (všechny oblasti Azure) |

### <a name="serverless"></a>Bez serveru

| Prostředek | Omezení |
| --- | --- |
| Maximální počet databází | Unlimited |
| Maximální počet kontejnerů na účet  | 100 |
| Maximální počet oblastí | 1 (jakákoli oblast Azure) |

## <a name="per-container-limits"></a>Omezení podle kontejneru

V závislosti na tom, jaké rozhraní API používáte, může kontejner Azure Cosmos představovat buď kolekci, tabulku, nebo graf. Kontejnery podporují konfigurace pro [omezení jedinečnosti klíčů](unique-keys.md), [uložené procedury, triggery a UDF](stored-procedures-triggers-udfs.md)a [zásady indexování](how-to-manage-indexing-policy.md). V následující tabulce jsou uvedeny limity specifické pro konfigurace v rámci kontejneru. 

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální délka názvu databáze nebo kontejneru | 255 |
| Maximální počet uložených procedur na kontejner | 100 <sup>*</sup>|
| Maximální počet UDF na kontejner | 0,25 <sup>*</sup>|
| Maximální počet cest v zásadách indexování| 100 <sup>*</sup>|
| Maximální počet jedinečných klíčů na kontejner|10pruhový <sup>*</sup>|
| Maximální počet cest na jedinečné omezení klíče|16bitovém <sup>*</sup>|
| Maximální hodnota TTL |2147483647|

<sup>*</sup> Můžete zvýšit libovolný z těchto limitů pro kontejner vytvořením [support Request Azure](create-support-request-quota-increase.md).

## <a name="per-item-limits"></a>Omezení podle položek

V závislosti na tom, jaké rozhraní API používáte, může položka Azure Cosmos představovat buď dokument v kolekci, řádek v tabulce nebo uzel nebo okraj v grafu. V následující tabulce jsou uvedeny limity na položku v Cosmos DB. 

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální velikost položky | 2 MB (délka UTF-8 reprezentace JSON) |
| Maximální délka hodnoty klíče oddílu | 2048 bajtů |
| Maximální délka hodnoty ID | 1023 bajtů |
| Maximální počet vlastností na položku | Žádné praktické omezení |
| Maximální hloubka vnořování | Žádné praktické omezení |
| Maximální délka názvu vlastnosti | Žádné praktické omezení |
| Maximální délka hodnoty vlastnosti | Žádné praktické omezení |
| Maximální délka hodnoty řetězcové vlastnosti | Žádné praktické omezení |
| Maximální délka číselné hodnoty vlastnosti | IEEE754 s dvojitou přesností 64-bit |
| Maximální úroveň vnoření pro vložené objekty/pole | 128 |
| Maximální hodnota TTL |2147483647|

Neexistují žádná omezení na datové části položky, jako je počet vlastností a hloubka vnořování, s výjimkou omezení délky pro klíče oddílu a hodnoty ID a omezení celkové velikosti 2 MB. Možná budete muset nakonfigurovat zásady indexování pro kontejnery s velkými nebo složitými strukturami položek a snížit tak spotřebu RU. V tématu [modelování položek v Cosmos DB](how-to-model-partition-example.md) najdete příklad reálného světa a vzory pro správu velkých položek.

## <a name="per-request-limits"></a>Omezení podle požadavků

Azure Cosmos DB podporuje [operace CRUD a dotazování](/rest/api/cosmos-db/) u prostředků, jako jsou kontejnery, položky a databáze. Podporuje také [transakční dávkové požadavky](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) na více položek se stejným klíčem oddílu v kontejneru.

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální doba provádění pro jednu operaci (například spuštění uložené procedury nebo jedno načtení stránky dotazu)| 5 sekund |
| Maximální velikost požadavku (například uložená procedura, CRUD)| 2 MB |
| Maximální velikost odpovědi (například na stránkovaném dotazu) | 4 MB |
| Maximální počet operací v transakční dávce | 100 |

Jakmile operace jako dotaz dosáhne časového limitu spuštění nebo omezení velikosti odpovědi, vrátí klientovi stránku výsledků a token pro pokračování, aby bylo možné pokračovat v provádění. Neexistuje žádné praktické omezení pro dobu, po kterou může jeden dotaz běžet na stránkách nebo v pokračování.

Cosmos DB používá pro autorizaci HMAC. Pro podrobné řízení přístupu k prostředkům, jako jsou kontejnery, klíče oddílů nebo položky, můžete použít buď primární klíč, nebo [tokeny prostředků](secure-access-to-data.md) . Následující tabulka uvádí omezení autorizačních tokenů v Cosmos DB.

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální doba vypršení platnosti primárního tokenu | 15 min  |
| Minimální čas vypršení platnosti tokenu prostředku | 10 min  |
| Maximální doba vypršení platnosti tokenu prostředku | ve výchozím nastavení je 24 h. Můžete ho rozšířit [podáním lístku podpory Azure](create-support-request-quota-increase.md) .|
| Maximální časové zkosení pro autorizaci tokenů| 15 min |

Cosmos DB podporuje provádění triggerů během zápisu. Služba podporuje maximálně jednu aktivační událost a jednu operaci post-Trigger na operaci zápisu.

## <a name="metadata-request-limits"></a>Omezení požadavků na metadata

Azure Cosmos DB udržuje systémová metadata pro každý účet. Tato metadata vám umožní vytvořit výčet kolekcí, databází, dalších Azure Cosmos DBch prostředků a jejich konfigurací zdarma.

| Prostředek | Výchozí omezení |
| --- | --- |
|Maximální počet vytvoření kolekce za minutu| 5|
|Maximální počet vytvoření databáze za minutu|   5|
|Maximální zajištěná četnost aktualizací propustnosti za minutu| 5|

## <a name="limits-for-autoscale-provisioned-throughput"></a>Omezení pro zřízenou propustnost automatického škálování

Podrobnější vysvětlení propustnosti a omezení úložiště s využitím automatického škálování najdete v článku věnovaném [automatickému škálování](provision-throughput-autoscale.md#autoscale-limits) a [nejčastějším dotazům](autoscale-faq.md#lowering-the-max-rus) .

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální RU/s, na které se může systém škálovat |  `Tmax`, automatické škálování má nastavené maximum RU/s.|
| Minimální RU/s, na které se může systém škálovat | `0.1 * Tmax`|
| Aktuální RU/s: systém se škáluje na  |  `0.1*Tmax <= T <= Tmax`na základě využití|
| Minimální fakturovatelné RU/s za hodinu| `0.1 * Tmax` <br></br>Fakturace se provádí po hodinách, kde se vám bude účtovat nejvyšší RU/s, po který se účtuje za hodinu, nebo na základě `0.1*Tmax` toho, co je vyšší. |
| Minimální automatické škálování max. RU/s pro kontejner  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)` zaokrouhlit na nejbližší 1000 RU/s |
| Minimální automatické škálování max. RU/s pro databázi  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`, zaokrouhlené na nejbližší 1000 RU/s. <br></br>Všimněte si, že pokud má vaše databáze více než 25 kontejnerů, systém zvýší minimální hodnotu automatického škálování maximum RU/s o 1000 RU/s na další kontejner. Pokud máte například 30 kontejnerů, nejnižší maximální velikost pro automatické škálování, kterou můžete nastavit, je 9000 RU/s (stupnice v rozmezí 900-9000 RU/s).

## <a name="sql-query-limits"></a>Omezení dotazu SQL

Cosmos DB podporuje dotazování na položky pomocí [jazyka SQL](how-to-sql-query.md). V následující tabulce jsou popsána omezení v příkazech dotazu, například v podmínkách počtu klauzulí nebo délka dotazu.

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální délka dotazu SQL| 256 kB |
| Maximální počet spojení na dotaz| čl <sup>*</sup>|
| Maximální počet UDF na dotaz| 10pruhový <sup>*</sup>|
| Maximální počet bodů na mnohoúhelník| 4 096 |
| Maximální počet zahrnutých cest na kontejner| 500 |
| Maximální počet vyloučených cest na kontejner| 500 |
| Maximální vlastnosti ve složeném indexu| 8 |

<sup>*</sup> Můžete zvýšit libovolné z těchto omezení SQL dotazu vytvořením [support Request Azure](create-support-request-quota-increase.md).

## <a name="mongodb-api-specific-limits"></a>Omezení specifická pro rozhraní API MongoDB

Cosmos DB podporuje přenosový protokol MongoDB pro aplikace napsané proti MongoDB. Podporované příkazy a verze protokolů najdete v [podporovaných funkcích MongoDB a syntaxi](mongodb-feature-support.md).

V následující tabulce jsou uvedeny limity, které jsou specifické pro podporu funkcí MongoDB. Další omezení služby uvedená pro rozhraní API SQL (Core) se vztahují také na rozhraní MongoDB API.

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální velikost paměti pro dotaz na MongoDB (Toto omezení je jenom pro 3,2 verze serveru). | 40 MB |
| Maximální doba provádění operací MongoDB| 30 s |
| Časový limit nečinného připojení pro uzavření připojení na straně serveru * | 30 minut |

\* Doporučujeme, aby klientské aplikace nastavily časový limit nečinného připojení v nastavení ovladače na 2-3 minut, protože [výchozí časový limit pro vyrovnávání zatížení Azure je 4 minuty](../load-balancer/load-balancer-tcp-idle-timeout.md).  Tento časový limit zajistí, aby nečinné připojení neuzavřelo zprostředkující Nástroj pro vyrovnávání zatížení mezi klientským počítačem a Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Vyzkoušet Cosmos DB omezení Free

V následující tabulce jsou uvedeny limity pro [testovací Azure Cosmos DB pro bezplatnou](https://azure.microsoft.com/try/cosmosdb/) zkušební verzi.

| Prostředek | Výchozí omezení |
| --- | --- |
| Doba trvání zkušební verze | 30 dní (novou zkušební verzi si můžete vyžádat po vypršení platnosti) <br> Po vypršení platnosti se uložené informace odstraní. |
| Maximální počet kontejnerů na předplatné (SQL, Gremlin, rozhraní API pro tabulky) | 1 |
| Maximální počet kontejnerů na předplatné (rozhraní API MongoDB) | 3 |
| Maximální propustnost na kontejner | 5000 |
| Maximální propustnost na sdílenou databázi propustnosti | 20000 |
| Maximální celková velikost úložiště na účet | 10 GB |

Zkuste Cosmos DB podporuje globální distribuci jenom v oblastech Střed USA, Severní Evropa a jihovýchodní Asie. Lístky podpory Azure nelze vytvořit pro účty try Azure Cosmos DB. Nicméně podpora je poskytována pro předplatitele se stávajícími plány podpory.

## <a name="free-tier-account-limits"></a>Limity účtů úrovně Free
V následující tabulce jsou uvedeny limity [Azure Cosmos DB účtů úrovně Free.](optimize-dev-test.md#azure-cosmos-db-free-tier)

| Prostředek | Výchozí omezení |
| --- | --- |
| Počet účtů bezplatné úrovně na předplatné Azure | 1 |
| Doba trvání slevy na volné úrovni | Doba života účtu. Při vytváření účtu musí být výslovný souhlas. |
| Maximální RU/s zdarma | 400 RU/s |
| Maximální úložiště zdarma | 5 GB |
| Maximální počet sdílených databází propustnosti | 5 |
| Maximální počet kontejnerů ve sdílené databázi propustnosti | 25 <br>V účtech bezplatné úrovně je minimální RU/s pro sdílenou databázi propustnosti s až 25 kontejnery 400 RU/s. |

  Kromě výše uvedeného platí [omezení podle účtů](#per-account-limits) i pro účty bezplatné úrovně.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o hlavních konceptech Cosmos DB [globální distribuci](distribute-data-globally.md) a vytváření [oddílů](partitioning-overview.md) a [zřízené propustnosti](request-units.md).

Začínáme se službou Azure Cosmos DB s využitím jedné ze čtyř šablon Rychlý start:

* [Začínáme s rozhraním SQL API služby Azure Cosmos DB](create-sql-api-dotnet.md)
* [Začínáme s rozhraním API Azure Cosmos DB pro MongoDB](create-mongodb-nodejs.md)
* [Začínáme s rozhraním Cassandra API služby Azure Cosmos DB](create-cassandra-dotnet.md)
* [Začínáme s rozhraním Gremlin API služby Azure Cosmos DB](create-graph-dotnet.md)
* [Začínáme s rozhraním Table API služby Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)
