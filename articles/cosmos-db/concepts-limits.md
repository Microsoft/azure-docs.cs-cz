---
title: Kvóty služby Azure Cosmos DB
description: Azure Cosmos DB kvóty služeb a výchozí omezení pro různé typy prostředků.
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: d3d09d466e05c97de215542c66987aa6b723afce
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827985"
---
# <a name="azure-cosmos-db-service-quotas"></a>Kvóty služby Azure Cosmos DB

Tento článek poskytuje přehled výchozích kvót nabízených různým prostředkům v Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Úložiště a propustnost

Po vytvoření účtu Azure Cosmos v rámci svého předplatného můžete spravovat data v účtu [vytvořením databází, kontejnerů a položek](databases-containers-items.md). Propustnost můžete zřídit na úrovni kontejneru nebo databáze na úrovni jednotlivých [jednotek požadavků (ru/s nebo ru)](request-units.md). V následující tabulce jsou uvedeny limity pro úložiště a propustnost na jeden kontejner nebo databázi.

| Resource | Výchozí omezení |
| --- | --- |
| Maximální počet ru na kontejner ([zřízený režim vyhrazené propustnosti](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 ve výchozím nastavení. Můžete ho rozšířit podáním [lístku podpory Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) . |
| Maximální počet ru na databázi ([zřízený režim sdílené propustnosti](databases-containers-items.md#azure-cosmos-containers)) | 1 000 000 ve výchozím nastavení. Můžete ho rozšířit podáním [lístku podpory Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) . |
| Maximální ru na (logický) klíč oddílu | 10,000 |
| Maximální velikost úložiště napříč všemi položkami na (logický) klíč oddílu| 10 GB |
| Maximální počet různých (logických) klíčů oddílu | Unlimited |
| Maximální velikost úložiště na kontejner | Unlimited |
| Maximální velikost úložiště na databázi | Unlimited |

> [!NOTE]
> Osvědčené postupy pro správu úloh, které mají klíče oddílů, které vyžadují vyšší limity úložiště nebo propustnosti, najdete v tématu [design for Hot Key Keys](synthetic-partition-keys.md) .
>

Cosmos kontejner (nebo sdílená databáze propustnosti) musí mít minimální propustnost 400 ru. Při zvětšování kontejneru závisí minimální podporovaná propustnost i na následujících faktorech:

* Minimální propustnost, kterou můžete nastavit na kontejneru, závisí na maximální propustnosti, která je v kontejneru trvale zřízena. Služba podporuje snížení propustnosti kontejneru na 10% poskytnutého maxima. Pokud se například propustnost zvýšila na 10000 ru, pak bude nejnižší možná zajištěná propustnost 1000 ru
* Minimální propustnost ve sdílené databázi propustnosti také závisí na celkovém počtu kontejnerů, které jste dříve vytvořili ve sdílené databázi propustnosti měřenou v 100 ru na kontejner. Pokud jste například vytvořili pět kontejnerů v rámci sdílené databáze propustnosti, musí být propustnost alespoň 500 ru.

Aktuální a minimální propustnost kontejneru nebo databáze lze načíst z Azure Portal nebo sad SDK. Další informace najdete v tématu [zřízení propustnosti u kontejnerů a databází](set-throughput.md). 

> [!NOTE]
> V některých případech může být možné snížit propustnost na méně než 10%. Použijte rozhraní API k získání přesného minima ru na kontejner.
>

V části Souhrn jsou zde uvedená minimální omezení pro podávání RU. 

| Resource | Výchozí omezení |
| --- | --- |
| Minimální počet ru na kontejner ([zřízený režim vyhrazené propustnosti](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimální počet ru na databázi ([zřízený režim sdílené propustnosti](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimální počet ru na kontejner v rámci sdílené databáze propustnosti | 100 |

Cosmos DB podporuje Elastické škálování propustnosti (ru) na kontejner nebo databázi prostřednictvím sad SDK nebo portálu. Každý kontejner se může synchronně a hned škálovat v rozmezí od 10 do 100 časů mezi minimální a maximální hodnotou. Pokud je požadovaná hodnota propustnosti mimo rozsah, škálování se provádí asynchronně. Asynchronní škálování může trvat několik minut, než se dokončí v závislosti na požadované propustnosti a velikosti úložiště dat v kontejneru.  

## <a name="control-plane-operations"></a>Řízení operací roviny

[Účet Azure Cosmos můžete zřídit a spravovat](how-to-manage-database-account.md) pomocí šablon Azure Portal, Azure PowerShell, Azure CLI a Azure Resource Manager. V následující tabulce jsou uvedené limity pro každé předplatné, účet a počet operací.

| Resource | Výchozí omezení |
| --- | --- |
| Maximální počet účtů databáze na předplatné | 50 ve výchozím nastavení. Můžete ho rozšířit podáním [lístku podpory Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) .|
| Maximální počet regionálních převzetí služeb při selhání | ve výchozím nastavení 1 hodina. Můžete ho rozšířit podáním [lístku podpory Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) .|

> [!NOTE]
> Regionální převzetí služeb při selhání platí jenom pro jeden region zápis účtů. Účty pro zápis ve více oblastech nevyžadují nebo nemají omezení pro změnu oblasti zápisu.

Cosmos DB automaticky provede zálohování vašich dat v pravidelných intervalech. Podrobnosti o intervalech uchovávání záloh a Windows najdete [v tématu obnovení dat online zálohování a na vyžádání v Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Omezení podle účtů

| Resource | Výchozí omezení |
| --- | --- |
| Maximální počet databází | Unlimited |
| Maximální počet kontejnerů na databázi (nebo účet) | Unlimited |
| Maximální počet oblastí | Bez omezení (všechny oblasti Azure) |

## <a name="per-container-limits"></a>Omezení podle kontejneru

V závislosti na tom, jaké rozhraní API používáte, může kontejner Azure Cosmos představovat buď kolekci, tabulku, nebo graf. Kontejnery podporují konfigurace pro [omezení jedinečnosti klíčů](unique-keys.md), [uložené procedury, triggery a UDF](stored-procedures-triggers-udfs.md)a [zásady indexování](how-to-manage-indexing-policy.md). V následující tabulce jsou uvedeny limity specifické pro konfigurace v rámci kontejneru. 

| Resource | Výchozí omezení |
| --- | --- |
| Maximální délka názvu databáze nebo kontejneru | 255 |
| Maximální počet uložených procedur na kontejner | 100 <sup>*</sup>|
| Maximální počet UDF na kontejner | 25 <sup>*</sup>|
| Maximální počet cest v zásadách indexování| 100 <sup>*</sup>|
| Maximální počet jedinečných klíčů na kontejner|10 <sup>*</sup>|
| Maximální počet cest na jedinečné omezení klíče|16 <sup>*</sup>|

<sup>*</sup>Můžete zvýšit množství těchto omezení na kontejner tím, že se obrátíte na podporu Azure.

## <a name="per-item-limits"></a>Omezení podle položek

V závislosti na tom, jaké rozhraní API používáte, může položka Azure Cosmos představovat buď dokument v kolekci, řádek v tabulce nebo uzel nebo okraj v grafu. V následující tabulce jsou uvedeny limity na položku v Cosmos DB. 

| Resource | Výchozí omezení |
| --- | --- |
| Maximální velikost položky | 2 MB (délka UTF-8 reprezentace JSON) |
| Maximální délka hodnoty klíče oddílu | 2048 bajtů |
| Maximální délka hodnoty ID | 1024 bajtů |
| Maximální počet vlastností na položku | Žádné praktické omezení |
| Maximální hloubka vnořování | Žádné praktické omezení |
| Maximální délka názvu vlastnosti | Žádné praktické omezení |
| Maximální délka hodnoty vlastnosti | Žádné praktické omezení |
| Maximální délka hodnoty řetězcové vlastnosti | Žádné praktické omezení |
| Maximální délka číselné hodnoty vlastnosti | IEEE754 s dvojitou přesností 64-bit |

Neexistují žádná omezení na datové části položky, jako je počet vlastností a hloubka vnořování, s výjimkou omezení délky pro klíče oddílu a hodnoty ID a omezení celkové velikosti 2 MB. Možná budete muset nakonfigurovat zásady indexování pro kontejnery s velkými nebo složitými strukturami položek a snížit tak spotřebu RU. V tématu [modelování položek v Cosmos DB](how-to-model-partition-example.md) najdete příklad reálného světa a vzory pro správu velkých položek.

## <a name="per-request-limits"></a>Omezení podle požadavků

Cosmos DB podporuje [operace CRUD a dotazování](https://docs.microsoft.com/rest/api/cosmos-db/) u prostředků, jako jsou kontejnery, položky a databáze.  

| Resource | Výchozí omezení |
| --- | --- |
| Maximální doba provádění pro jednu operaci (například spuštění uložené procedury nebo jedno načtení stránky dotazu)| 5 sekund |
| Maximální velikost požadavku (uložená procedura, CRUD)| 2 MB |
| Maximální velikost odpovědi (například na stránkovaném dotazu) | 4 MB |

Jakmile operace jako dotaz dosáhne časového limitu spuštění nebo omezení velikosti odpovědi, vrátí klientovi stránku výsledků a token pro pokračování, aby bylo možné pokračovat v provádění. Neexistuje žádné praktické omezení pro dobu, po kterou může jeden dotaz běžet na stránkách nebo v pokračování.

Cosmos DB používá pro autorizaci HMAC. K prostředkům, jako jsou kontejnery, klíče oddílů nebo položky, můžete použít buď hlavní klíč, nebo [tokeny prostředků](secure-access-to-data.md) pro jemně odstupňované řízení přístupu. Následující tabulka uvádí omezení autorizačních tokenů v Cosmos DB.

| Resource | Výchozí omezení |
| --- | --- |
| Maximální doba vypršení platnosti hlavního tokenu | 15 min  |
| Minimální čas vypršení platnosti tokenu prostředku | 10 min  |
| Maximální doba vypršení platnosti tokenu prostředku | ve výchozím nastavení je 24 h. Můžete ho rozšířit podáním [lístku podpory Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) .|
| Maximální časové zkosení pro autorizaci tokenů| 15 min |

Cosmos DB podporuje provádění triggerů během zápisu. Služba podporuje maximálně jednu aktivační událost a jednu operaci post-Trigger na operaci zápisu. 

## <a name="sql-query-limits"></a>Omezení dotazu SQL

Cosmos DB podporuje dotazování na položky pomocí [jazyka SQL](how-to-sql-query.md). V následující tabulce jsou popsána omezení v příkazech dotazu, například v podmínkách počtu klauzulí nebo délka dotazu.

| Resource | Výchozí omezení |
| --- | --- |
| Maximální délka dotazu SQL| 256 KB<sup>*</sup>|
| Maximální počet spojení na dotaz| 5 <sup>*</sup>|
| Maximální počet and na dotaz| 2000 <sup>*</sup>|
| Maximální počet ORs na dotaz| 2000 <sup>*</sup>|
| Maximální počet UDF na dotaz| 10 <sup>*</sup>|
| Maximální počet argumentů na výraz| 6000 <sup>*</sup>|
| Maximální počet bodů na mnohoúhelník| 4096 <sup>*</sup>|

<sup>*</sup>Můžete zvýšit libovolné z těchto omezení dotazu SQL kontaktováním podpory Azure.

## <a name="mongodb-api-specific-limits"></a>Omezení specifická pro rozhraní API MongoDB

Cosmos DB podporuje přenosový protokol MongoDB pro aplikace napsané proti MongoDB. Podporované příkazy a verze protokolů najdete v [podporovaných funkcích MongoDB a syntaxi](mongodb-feature-support.md).

V následující tabulce jsou uvedeny limity, které jsou specifické pro podporu funkcí MongoDB. Další omezení služby uvedená pro rozhraní API SQL (Core) se vztahují také na rozhraní MongoDB API.

| Resource | Výchozí omezení |
| --- | --- |
| Maximální velikost paměti pro dotaz na MongoDB | 40 MB |
| Maximální doba provádění operací MongoDB| 30 s |

## <a name="try-cosmos-db-free-limits"></a>Vyzkoušet Cosmos DB omezení Free

V následující tabulce jsou uvedeny limity pro [testovací Azure Cosmos DB pro bezplatnou](https://azure.microsoft.com/try/cosmosdb/) zkušební verzi.

| Resource | Výchozí omezení |
| --- | --- |
| Doba trvání zkušební verze | 30 dní (může být několikrát obnoveno) |
| Maximální počet kontejnerů na předplatné (SQL, Gremlin, rozhraní API pro tabulky) | 1 |
| Maximální počet kontejnerů na předplatné (rozhraní API MongoDB) | 3 |
| Maximální propustnost na kontejner | 5000 |
| Maximální propustnost na sdílenou databázi propustnosti | 20000 |
| Maximální celková velikost úložiště na účet | 10 GB |

Zkuste Cosmos DB podporuje globální distribuci jenom v oblastech Střed USA, Severní Evropa a jihovýchodní Asie. Lístky podpory Azure nelze vytvořit pro účty try Azure Cosmos DB. Nicméně podpora je poskytována pro předplatitele se stávajícími plány podpory.

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o hlavních konceptech Cosmos DB [globální distribuci](distribute-data-globally.md) a vytváření [oddílů](partitioning-overview.md) a [zřízené propustnosti](request-units.md).

Začínáme se službou Azure Cosmos DB s využitím jedné ze čtyř šablon Rychlý start:

* [Začínáme s rozhraním SQL API služby Azure Cosmos DB](create-sql-api-dotnet.md)
* [Začínáme s rozhraním API Azure Cosmos DB pro MongoDB](create-mongodb-nodejs.md)
* [Začínáme s rozhraním Cassandra API služby Azure Cosmos DB](create-cassandra-dotnet.md)
* [Začínáme s rozhraním Gremlin API služby Azure Cosmos DB](create-graph-dotnet.md)
* [Začínáme s rozhraním Table API služby Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Vyzkoušejte si Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)
