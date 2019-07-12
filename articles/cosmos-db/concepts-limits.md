---
title: Kvóty služby Azure Cosmos DB
description: Výchozí omezení na různých typů prostředků a kvót služby Azure Cosmos DB.
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 74df0038676e8459028084890da569ed3b75a682
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797286"
---
# <a name="azure-cosmos-db-service-quotas"></a>Kvóty služby Azure Cosmos DB

Tento článek obsahuje přehled výchozích kvót, nabízí různé prostředky v Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Úložiště a propustnosti

Po vytvoření účtu Azure Cosmos v rámci vašeho předplatného, můžete spravovat data ve vašem účtu pomocí [vytváření databází, kontejnerů a položky](databases-containers-items.md). Můžete zřídit propustnost na úrovni kontejneru nebo na úrovni databáze z hlediska [jednotky (RU/s nebo RUs) žádosti](request-units.md). Následující tabulka uvádí omezení úložiště a propustnost za kontejner a databáze.

| Resource | Výchozí omezení |
| --- | --- |
| Maximální jednotek požadavku za kontejner ([režimu zřízenou vyhrazenou propustnost](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 ve výchozím nastavení. Zvětšit podle [vyplňte lístek podpory Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) nebo kontaktujete nás prostřednictvím [dotaz Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| Maximální jednotek ru na databázi ([sdílené propustnosti zřízené režimu](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 ve výchozím nastavení. Zvětšit podle [vyplňte lístek podpory Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) nebo kontaktujete nás prostřednictvím [dotaz Cosmos DB](mailto:askcosmosdb@microsoft.com) |
| Maximální jednotek požadavku za klíč oddílu (logická) | 10,000 |
| Maximální úložiště v rámci všech položek na klíč oddílu (logická)| 10 GB |
| Maximální počet klíčů oddílů distinct (logická) | Unlimited |
| Maximální velikost úložiště na kontejner | Unlimited |
| Maximální velikost úložiště na databázi | Unlimited |

> [!NOTE]
> Osvědčené postupy pro správu úloh, které mají klíče oddílů, které potřebovat vyšší limity pro úložiště nebo propustnost, naleznete v tématu [návrh a výměně klíčů oddílů](synthetic-partition-keys.md)
>

Cosmos kontejneru (nebo sdílené propustnosti databáze), musí mít minimální propustnost 400 RU. S růstem kontejneru minimální podporovaná propustnost také závisí na následujících faktorech:

* Maximální propustnost, někdy zřízený v kontejneru. Tato služba podporuje snížit propustnosti kontejneru má 10 % zřízeného maximum. Například pokud propustnost se tak zvýšil na 10000 RU, pak nejnižší možné zřízená propustnost by 1000 ru
* Celkový počet kontejnerů, které jste již někdy vytvářeli v databázi sdílené propustnost se měří ve 100 ru na kontejner. Například pokud vytvoříte pět kontejnery v rámci sdílené propustnosti databáze, pak propustnost musí být aspoň 500 ru

Aktuální a minimální propustnosti kontejneru nebo databáze můžete získat z webu Azure portal nebo sad SDK. Další informace najdete v tématu [zřizování propustnosti na kontejnerech a databází](set-throughput.md). Stručně řečeno tady jsou minimální zřízené RU omezení. 

| Resource | Výchozí omezení |
| --- | --- |
| Minimální jednotek požadavku za kontejner ([režimu zřízenou vyhrazenou propustnost](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimální jednotek ru na databázi ([sdílené propustnosti zřízené režimu](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimální jednotek požadavku za kontejner v rámci sdílené propustnosti databáze | 100 |

Cosmos DB podporuje elastické škálování propustnosti (ru) za kontejner nebo databáze prostřednictvím sad SDK nebo portálu. Každý kontejner můžete škálovat okamžitě a synchronně do rozsahu stupnice 10 až 100krát mezi minimální a maximální hodnoty. Pokud hodnota požadované propustnosti je mimo rozsah, škálování se provádí asynchronně. Asynchronní škálování může trvat až hodin v závislosti na požadované propustnosti a velikosti úložiště dat v kontejneru.  

## <a name="control-plane-operations"></a>Operace roviny řízení

Je možné [zřizovat a spravovat váš účet Azure Cosmos](how-to-manage-database-account.md) pomocí webu Azure portal, prostředí Azure PowerShell, Azure CLI a šablon Azure Resource Manageru. Následující tabulka uvádí omezení pro předplatné, účet a počet operací.

| Resource | Výchozí omezení |
| --- | --- |
| Maximální databáze účtů na předplatné | 50 ve výchozím nastavení. Zvětšit podle [vyplňte lístek podpory Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) nebo kontaktujete nás prostřednictvím [dotaz Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Maximální počet regionální převzetí služeb při selhání | 1 za hodinu ve výchozím nastavení. Zvětšit podle [vyplňte lístek podpory Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) nebo kontaktujete nás prostřednictvím [dotaz Cosmos DB](mailto:askcosmosdb@microsoft.com)|

> [!NOTE]
> Regionální převzetí služeb při selhání platí jenom pro účty v jedné oblasti zápisu. Více oblastí zápisu účty nevyžadují ani mít omezený Změna oblasti zápisu.

Cosmos DB automaticky provede zálohování dat v pravidelných intervalech. Podrobnosti o intervaly uchovávání záloh a windows, naleznete v tématu [zálohování Online a na vyžádání dat obnovení ve službě Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-container-limits"></a>Omezení na kontejner

V závislosti na tom, které můžete použít rozhraní API můžete kontejneru Azure Cosmos představují kolekci, tabulky nebo grafu. Kontejnery podporují konfigurace pro [omezení jedinečných klíčů](unique-keys.md), [uložené procedury, triggery a uživatelem definovanými funkcemi](stored-procedures-triggers-udfs.md), a [zásady indexování](how-to-manage-indexing-policy.md). Následující tabulka uvádí omezení, které jsou specifické pro konfigurace v rámci kontejneru. 

| Resource | Výchozí omezení |
| --- | --- |
| Maximální délka názvu databáze nebo kontejneru | 255 |
| Maximální uložených procedur na kontejner | 100 <sup>*</sup>|
| Maximální UDF na kontejner | 25 <sup>*</sup>|
| Maximální počet cest v zásady indexování| 100 <sup>*</sup>|
| Maximální počet jedinečných klíčů na kontejner|10 <sup>*</sup>|
| Maximální počet cest za omezení unique key|16 <sup>*</sup>|

<sup>*</sup> Můžete zvýšit některý z těchto omezení na kontejner kontaktovat podporu Azure nebo kontaktujete nás prostřednictvím [dotaz Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="per-item-limits"></a>Omezení na jednotlivé položky

V závislosti na tom, které můžete použít rozhraní API položka Azure Cosmos může představovat buď dokumentů v kolekci řádek v tabulce, nebo uzel nebo okraj v grafu. Následující tabulka uvádí omezení pro položky v Cosmos DB. 

| Resource | Výchozí omezení |
| --- | --- |
| Maximální velikost položky | 2 MB (délka reprezentaci JSON UTF-8) |
| Maximální délka hodnoty klíče oddílu | 2 048 bajtů |
| Maximální délka hodnoty id | 1024 bajtů |
| Maximální počet vlastností pro jednotlivé položky | Žádný praktický limit |
| Maximální hloubka vnoření | Žádný praktický limit |
| Maximální délka názvu vlastnosti | Žádný praktický limit |
| Maximální délka hodnoty vlastnosti | Žádný praktický limit |
| Maximální délka řetězce hodnoty vlastnosti | Žádný praktický limit |
| Maximální délka hodnoty číselné vlastnosti | Dvojitá přesnost IEEE754 64-bit |

Neexistují žádná omezení v datových částí položky jako je počet vlastností a vnoření hloubky, s výjimkou omezení délky na klíč oddílu a hodnoty id a celkové velikosti omezení 2 MB. Bude pravděpodobně nutné nakonfigurovat zásady indexování pro kontejnery s velký nebo složitý položky struktury snížit spotřebu RU. Zobrazit [modelování položky ve službě Cosmos DB](how-to-model-partition-example.md) reálný příklad a vzory ke správě velkých položek.

## <a name="per-request-limits"></a>Omezení na základě žádosti

Cosmos DB podporuje [operace CRUD a dotazů](https://docs.microsoft.com/rest/api/cosmos-db/) proti prostředkům, jako jsou kontejnery, položky a databází.  

| Resource | Výchozí omezení |
| --- | --- |
| Maximální doba spuštění pro jednu operaci (např. spuštění uložené procedury nebo načtení stránky jeden dotaz)| 5 s |
| Žádost o maximální velikosti (uložené procedury, CRUD)| 2 MB |
| Maximální velikost odpovědi (například stránkovaných dotazů) | 4 MB |

Po operaci jako dotaz dosáhne limitu spuštění vypršení časového limitu nebo odpověď, vrátí stránku výsledků a token pro pokračování tak klientovi obnovit spuštění. Neexistuje žádné praktické omezení na dobu, po kterou můžete spustit jeden dotaz napříč stránek/pokračování.

Cosmos DB používá HMAC pro autorizaci. Můžete použít buď hlavní klíč, nebo [tokenech prostředků](secure-access-to-data.md) pro detailní kontrolu přístupu k prostředkům, jako jsou kontejnery, oddíly klíče nebo položky. Následující tabulka uvádí omezení pro ověřování tokenů ve službě Cosmos DB.

| Resource | Výchozí omezení |
| --- | --- |
| Maximální hlavní platnost tokenu vypršela doba | 15 min  |
| Čas vypršení platnosti tokenu minimální prostředku | 10 minut  |
| Čas vypršení platnosti tokenu maximální prostředku | 24 h ve výchozím nastavení. Zvětšit podle [vyplňte lístek podpory Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) nebo kontaktujete nás prostřednictvím [dotaz Cosmos DB](mailto:askcosmosdb@microsoft.com)|
| Posun maximální hodin pro token autorizace| 15 min |

Cosmos DB podporuje spuštění aktivační události během zápisu. Tato služba podporuje maximálně jednu předběžné aktivační událost a jedna aktivační událost po za operace zápisu. 

## <a name="sql-query-limits"></a>Omezení dotazu SQL

Cosmos DB podporuje dotazování položek pomocí [SQL](how-to-sql-query.md). Následující tabulka popisuje omezení v příkazech dotazu, třeba z hlediska počtu klauzule nebo délkou dotazu.

| Resource | Výchozí omezení |
| --- | --- |
| Maximální délka dotazu SQL| 256 KB <sup>*</sup>|
| Maximální spojení na dotaz| 5 <sup>*</sup>|
| Maximální a každý dotaz| 2000 <sup>*</sup>|
| Maximální or každý dotaz| 2000 <sup>*</sup>|
| Maximální UDF každý dotaz| 10 <sup>*</sup>|
| Maximální argumenty za ve výrazu| 6000 <sup>*</sup>|
| Maximální bodů na mnohoúhelník| 4096 <sup>*</sup>|

<sup>*</sup> Můžete zvýšit některý z těchto omezení dotazu SQL kontaktovat podporu Azure nebo kontaktujete nás prostřednictvím [dotaz Cosmos DB](mailto:askcosmosdb@microsoft.com).

## <a name="mongodb-api-specific-limits"></a>Omezení konkrétní rozhraní API MongoDB

Cosmos DB podporuje přenosový protokol MongoDB pro aplikace napsané pro MongoDB. Můžete najít podporované příkazy a protokol verze na [funkcí a syntaxe MongoDB podporované](mongodb-feature-support.md).

Následující tabulka uvádí omezení, které jsou specifické pro podpora funkce MongoDB. Další omezení služby uvedené výše pro SQL (core) rozhraní API platí také pro rozhraní API pro MongoDB.

| Resource | Výchozí omezení |
| --- | --- |
| Maximální velikost paměti dotazu MongoDB | 40 MB |
| Maximální doba na provedení operací MongoDB| 30 s |

## <a name="try-cosmos-db-free-limits"></a>Zkuste omezení Cosmos DB zdarma

Následující tabulka uvádí omezení pro [vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) zkušební verze.

| Resource | Výchozí omezení |
| --- | --- |
| Během zkušebního období | 30 dní (může být libovolný počet pokusů o obnovení) |
| Maximální kontejnery na jedno předplatné (tabulku SQL, Gremlin, rozhraní API) | 1 |
| Maximální kontejnery na jedno předplatné (rozhraní API MongoDB) | 3 |
| Maximální propustnost za kontejner | 5000 |
| Maximální propustnost na sdílené propustnosti databáze | 20000 |
| Maximální celková velikost úložiště na účet | 10 GB |

Vyzkoušejte služby Cosmos DB podporuje globální distribuci v pouze oblasti střed USA, Severní Evropa a jihovýchodní Asie. Pro účty vyzkoušejte Azure Cosmos DB nelze vytvořit lístky žádostí o podporu Azure. Podpora je však poskytuje předplatitelům s stávajících plánech podpory.

## <a name="next-steps"></a>Další postup

Další informace o Cosmos DB základní koncepty [globální distribuce](distribute-data-globally.md) a [dělení](partitioning-overview.md) a [zřízená propustnost](request-units.md).

Začínáme se službou Azure Cosmos DB s využitím jedné ze čtyř šablon Rychlý start:

* [Začínáme s rozhraním SQL API služby Azure Cosmos DB](create-sql-api-dotnet.md)
* [Začínáme s Azure Cosmos DB přes rozhraní API pro MongoDB](create-mongodb-nodejs.md)
* [Začínáme s rozhraním Cassandra API služby Azure Cosmos DB](create-cassandra-dotnet.md)
* [Začínáme s rozhraním Gremlin API služby Azure Cosmos DB](create-graph-dotnet.md)
* [Začínáme s rozhraním Table API služby Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)
