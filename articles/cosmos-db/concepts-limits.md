---
title: Kvóty služeb Azure Cosmos DB
description: Kvóty služeb Azure Cosmos DB a výchozí limity pro různé typy prostředků.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: b24d7db679bb9cb9dacd5e1db8e6410b883548cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415695"
---
# <a name="azure-cosmos-db-service-quotas"></a>Kvóty služeb Azure Cosmos DB

Tento článek obsahuje přehled výchozích kvót nabízených pro různé prostředky ve službě Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Úložiště a propustnost

Po vytvoření účtu Azure Cosmos v rámci předplatného můžete spravovat data ve svém účtu [vytvořením databází, kontejnerů a položek](databases-containers-items.md). Můžete zřídit propustnost na úrovni kontejneru nebo na úrovni databáze z hlediska [jednotek požadavků (RU/s nebo RU)](request-units.md). V následující tabulce jsou uvedena omezení pro úložiště a propustnost na kontejner/databázi.

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální ru na kontejner[(vyhrazený režim zřízeného propustností)](databases-containers-items.md#azure-cosmos-containers) | 1 000 000 ve výchozím nastavení. Můžete zvýšit [vyplněním lístku podpory Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Maximální ru na databázi[(režim zřízené sdílenou propustností)](databases-containers-items.md#azure-cosmos-containers) | 1 000 000 ve výchozím nastavení. Můžete zvýšit [vyplněním lístku podpory Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Maximální ru na (logický) klíč oddílu | 10 000 |
| Maximální úložiště napříč všemi položkami na (logický) klíč oddílu| 20 GB |
| Maximální počet odlišných (logických) klíčů oddílů | Unlimited |
| Maximální úložiště na kontejner | Unlimited |
| Maximální úložiště na databázi | Unlimited |
| Maximální velikost přílohy na účet (funkce přílohy se odepisuje) | 2 GB |
| Minimální ru potřebné pro 1 GB | 10 ŽP/s |

> [!NOTE]
> Informace o osvědčených postupech pro správu úloh, které mají klíče oddílů, které vyžadují vyšší omezení pro úložiště nebo propustnost, naleznete [v tématu Vytvoření syntetického klíče oddílu](synthetic-partition-keys.md).
>

Kontejner Cosmos (nebo databáze sdílené propustností) musí mít minimální propustnost 400 ru. Jak kontejner roste, minimální podporovaná propustnost závisí také na následujících faktorech:

* Minimální propustnost, kterou můžete nastavit na kontejneru, závisí na maximální propustnosti, která kdy byla zřízena v kontejneru. Například pokud vaše propustnost byla zvýšena na 10000 ru, pak nejnižší možné zřízená propustnost by 1000 RU
* Minimální propustnost v databázi sdílené propustností také závisí na celkovém počtu kontejnerů, které jste kdy vytvořili v databázi sdílené propustnost, měřeno na 100 ru na kontejner. Pokud jste například vytvořili pět kontejnerů v rámci databáze sdílené propustnost, musí být propustnost alespoň 500 ru

Aktuální a minimální propustnost kontejneru nebo databáze lze načíst z portálu Azure nebo sad SDK. Další informace naleznete [v tématu Provision propustnost na kontejnery a databáze](set-throughput.md). 

> [!NOTE]
> V některých případech může být možné snížit propustnost na menší než 10 %. Pomocí rozhraní API získáte přesné minimální ru na kontejner.
>

Stručně řečeno, zde jsou minimální stanovené limity ŽP. 

| Prostředek | Výchozí omezení |
| --- | --- |
| Minimální ru na kontejner[(vyhrazený režim zřízeného propustností)](databases-containers-items.md#azure-cosmos-containers) | 400 |
| Minimální ru na databázi ([režim zřízené sdílenou propustností](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimální ru na kontejner v rámci databáze sdílené propustnosti | 100 |

Cosmos DB podporuje elastické škálování propustnost (RU) na kontejner nebo databázi prostřednictvím sad SDK nebo portálu. Každý kontejner může škálovat synchronně a okamžitě v rozsahu stupnice 10 až 100 krát, mezi minimální a maximální hodnoty. Pokud je požadovaná hodnota propustnost mimo rozsah, škálování se provádí asynchronně. Asynchronní škálování může trvat několik minut až hodin v závislosti na požadované propustnost a velikost úložiště dat v kontejneru.  

## <a name="control-plane-operations"></a>Provoz řídicí roviny

Svůj [účet Azure Cosmos](how-to-manage-database-account.md) můžete zřídit a spravovat pomocí azure portalu, Azure PowerShellu, Azure CLI a šablon Azure Resource Manager. V následující tabulce jsou uvedena omezení na předplatné, účet a počet operací.

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální počet databázových účtů na odběr | 50 ve výchozím nastavení. Můžete zvýšit [vyplněním lístku podpory Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Maximální počet regionálních převzetí služeb při selhání | 1/hod ve výchozím nastavení. Můžete zvýšit [vyplněním lístku podpory Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|

> [!NOTE]
> Regionální převzetí služeb při selhání platí pouze pro účty zápisů v jedné oblasti. Víceregionální účty zápisu nevyžadují nebo mají žádná omezení pro změnu oblasti zápisu.

Cosmos DB automaticky přebírá zálohy dat v pravidelných intervalech. Podrobnosti o intervalech uchovávání záloh a oknech najdete [v tématu Zálohování online a obnovení dat na vyžádání v Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Limity pro účet

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální počet databází | Unlimited |
| Maximální počet kontejnerů na databázi se sdílenou propustností |25 |
| Maximální počet kontejnerů na databázi nebo účet s vyhrazenou propustností  |Bez omezení |
| Maximální počet regionů | Bez omezení (všechny oblasti Azure) |

## <a name="per-container-limits"></a>Limity pro kontejnery

V závislosti na rozhraní API, které používáte, může kontejner Azure Cosmos představovat kolekci, tabulku nebo graf. Kontejnery podporují konfigurace pro [omezení jedinečných klíčů](unique-keys.md), uložené [procedury, aktivační události a ufl](stored-procedures-triggers-udfs.md)a [zásady indexování](how-to-manage-indexing-policy.md). V následující tabulce jsou uvedena omezení specifická pro konfigurace v rámci kontejneru. 

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální délka názvu databáze nebo kontejneru | 255 |
| Maximální počet skladovaných procedur na kontejner | 100<sup>*</sup>|
| Maximální Objem ufl na kontejner | 25<sup>*</sup>|
| Maximální počet cest v zásadách indexování| 100<sup>*</sup>|
| Maximální počet jedinečných klíčů na kontejner|10<sup>*</sup>|
| Maximální počet cest na jedinečné omezení klíče|16<sup>*</sup>|

<sup>*</sup>Můžete zvýšit některý z těchto limitů pro každý kontejner kontaktováním podpory Azure.

## <a name="per-item-limits"></a>Limity pro položky

V závislosti na rozhraní API, které používáte, může položka Azure Cosmos představovat dokument v kolekci, řádek v tabulce nebo uzel nebo okraj v grafu. V následující tabulce jsou uvedeny limity pro položku v Cosmos DB. 

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální velikost položky | 2 MB (UTF-8 délka reprezentace JSON) |
| Maximální délka hodnoty klíče oddílu | 2048 bajtů |
| Maximální délka hodnoty id | 1023 bajtů |
| Maximální počet vlastností na položku | Žádné praktické omezení |
| Maximální hloubka vnoření | Žádné praktické omezení |
| Maximální délka názvu vlastnosti | Žádné praktické omezení |
| Maximální délka hodnoty nemovitosti | Žádné praktické omezení |
| Maximální délka hodnoty vlastnosti řetězce | Žádné praktické omezení |
| Maximální délka číselné hodnoty vlastnosti | IEEE754 dvojitá přesnost 64bitová |

Neexistují žádná omezení pro datové části položky, jako je počet vlastností a hloubka vnoření, s výjimkou omezení délky klíče oddílu a id a omezení celkové velikosti 2 MB. Možná budete muset nakonfigurovat zásady indexování pro kontejnery s velkými nebo složitými strukturami položek, abyste snížili spotřebu ru. Viz [Modelování položek v Cosmos DB](how-to-model-partition-example.md) pro příklad reálného světa a vzory pro správu velkých položek.

## <a name="per-request-limits"></a>Limity pro vyžádání

Azure Cosmos DB podporuje [CRUD a dotazoperace](https://docs.microsoft.com/rest/api/cosmos-db/) proti prostředkům, jako jsou kontejnery, položky a databáze. Podporuje také [transakční dávkové požadavky](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) proti více položek se stejným klíčem oddílu v kontejneru.

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální doba provádění pro jednu operaci (například spuštění uložené procedury nebo načítání stránky jednoho dotazu)| 5 s |
| Maximální velikost požadavku (například uložená procedura, CRUD)| 2 MB |
| Maximální velikost odpovědi (například stránkovaný dotaz) | 4 MB |
| Maximální počet operací v transakční dávce | 100 |

Jakmile operace, jako je dotaz dosáhne časového limitu spuštění nebo limit velikosti odpovědi, vrátí stránku výsledků a token pokračování klientovi k obnovení provádění. Neexistuje žádné praktické omezení doby trvání jednoho dotazu může běžet na příčce nebo pokračování.

Cosmos DB používá HMAC pro autorizaci. Můžete použít buď hlavní klíč nebo [tokeny prostředků](secure-access-to-data.md) pro jemně odstupňované řízení přístupu k prostředkům, jako jsou kontejnery, klíče oddílů nebo položky. V následující tabulce jsou uvedena omezení pro tokeny autorizace v Cosmos DB.

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální doba vypršení platnosti hlavního tokenu | 15 min  |
| Minimální doba vypršení platnosti tokenu prostředků | 10 min  |
| Maximální doba vypršení platnosti tokenu prostředku | 24 h ve výchozím nastavení. Můžete zvýšit [vyplněním lístku podpory Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Maximální zkosení hodin pro autorizaci tokenu| 15 min |

Cosmos DB podporuje provádění aktivačních událostí během zápisů. Služba podporuje maximálně jednu předběžnou aktivační událost a jednu operaci po aktivaci na zápis. 

## <a name="autopilot-mode-limits"></a>Omezení režimu autopilota

Omezení propustnost a úložiště v režimu autopilota naleznete v článku [autopilota.](provision-throughput-autopilot.md#autopilot-limits)

## <a name="sql-query-limits"></a>Omezení dotazů SQL

Cosmos DB podporuje dotazování položek pomocí [SQL](how-to-sql-query.md). Následující tabulka popisuje omezení v příkazech dotazu, například z hlediska počtu klauzulí nebo délky dotazu.

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální délka dotazu SQL| 256 kB |
| Maximální hodnota JOIN na dotaz| 5<sup>*</sup>|
| Maximální ufl na dotaz| 10<sup>*</sup>|
| Maximální počet bodů na polygon| 4 096 |
| Maximální zahrnuté cesty na kontejner| 500 |
| Maximální počet vyloučených cest na kontejner| 500 |
| Maximální vlastnosti ve složeném indexu| 8 |

<sup>*</sup>Tato omezení dotazů SQL můžete zvýšit tak, že se obrátíte na podporu Azure.

## <a name="mongodb-api-specific-limits"></a>Limity specifické pro rozhraní API MongoDB

Cosmos DB podporuje drátový protokol MongoDB pro aplikace napsané proti MongoDB. Podporované příkazy a verze protokolů naleznete na [podporovaných funkcích a syntaxi MongoDB](mongodb-feature-support.md).

V následující tabulce jsou uvedeny limity specifické pro podporu funkcí MongoDB. Další omezení služeb uvedená pro rozhraní API SQL (jádro) platí také pro rozhraní MONGODB API.

| Prostředek | Výchozí omezení |
| --- | --- |
| Maximální velikost paměti dotazu MongoDB (Toto omezení je pouze pro verzi serveru 3.2) | 40 MB |
| Maximální doba provádění operací MongoDB| 30s |
| Časový limit nečinnosti připojení pro uzavření připojení na straně serveru* | 30 minut |

\*Doporučujeme, aby klientské aplikace nastavit časový limit připojení nečinnosti v nastavení ovladače na 2-3 minuty, protože [výchozí časový limit pro Azure LoadBalancer je 4 minuty](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout).  Tento časový limit zajistí, že nečinná připojení nejsou uzavřena zprostředkujícím systémem vyrovnávání zatížení mezi klientským počítačem a službou Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Vyzkoušejte limity Cosmos DB Free

V následující tabulce jsou uvedena omezení zkušební verze [Try Azure Cosmos DB for Free.](https://azure.microsoft.com/try/cosmosdb/)

| Prostředek | Výchozí omezení |
| --- | --- |
| Délka hodnocení | 30 dní (lze obnovit libovolný počet případů) |
| Maximální počet kontejnerů na předplatné (SQL, Gremlin, Table API) | 1 |
| Maximální počet kontejnerů na předplatné (Rozhraní API MongoDB) | 3 |
| Maximální propustnost na kontejner | 5000 |
| Maximální propustnost na databázi sdílené propustnosti | 20000 |
| Maximální celkové úložiště na účet | 10 GB |

Try Cosmos DB podporuje globální distribuci pouze v oblastech střední USA, Severní Evropy a jihovýchodní Asie. Lístky podpory Azure nelze vytvořit pro účty Try Azure Cosmos DB. Podpora je však k dispozici pro předplatitele s existující plány podpory.

## <a name="free-tier-account-limits"></a>Omezení účtu úrovně free
V následující tabulce jsou uvedena omezení pro [účty volné úrovně Azure Cosmos DB.](optimize-dev-test.md#azure-cosmos-db-free-tier)

| Prostředek | Výchozí omezení |
| --- | --- |
| Počet bezplatných účtů úrovní na předplatné Azure | 1 |
| Doba trvání slevy z volné úrovně | Životnost účtu. Musí se přihlásit během vytváření účtu. |
| Maximální Ru/s zdarma | 400 RU/s |
| Maximální úložiště zdarma | 5 GB |
| Maximální počet databází sdílené propustností | 5 |
| Maximální počet kontejnerů v databázi sdílené propustnosti | 25 <br>V účtech volné úrovně je minimální RU/s pro sdílenou databázi propustností s až 25 kontejnery 400 RU/s. |

  Kromě výše uvedeného se [limity pro účet na účet](#per-account-limits) vztahují také na účty volných úrovní.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o základních konceptech Cosmos DB [globální distribuce](distribute-data-globally.md) a [dělení](partitioning-overview.md) a [zřízené propustnosti](request-units.md).

Začínáme se službou Azure Cosmos DB s využitím jedné ze čtyř šablon Rychlý start:

* [Začínáme s rozhraním SQL API služby Azure Cosmos DB](create-sql-api-dotnet.md)
* [Začínáme s rozhraním API Azure Cosmos DB pro MongoDB](create-mongodb-nodejs.md)
* [Začínáme s rozhraním Cassandra API služby Azure Cosmos DB](create-cassandra-dotnet.md)
* [Začínáme s rozhraním Gremlin API služby Azure Cosmos DB](create-graph-dotnet.md)
* [Začínáme s rozhraním Table API služby Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)
