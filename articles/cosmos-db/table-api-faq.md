---
title: Nejčastější dotazy týkající se rozhraní API pro tabulky v Azure Cosmos DB
description: Získejte odpovědi na nejčastější dotazy týkající se rozhraní API pro tabulky v Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: sngun
ms.openlocfilehash: 65f276662ac4837003c7a7078b6197ba155eadc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88167585"
---
# <a name="frequently-asked-questions-about-the-table-api-in-azure-cosmos-db"></a>Nejčastější dotazy týkající se rozhraní API pro tabulky v Azure Cosmos DB

Rozhraní API pro tabulky Azure Cosmos DB je k dispozici v [Azure Portal](https://portal.azure.com) nejprve musíte se zaregistrovat k předplatnému Azure. Po registraci můžete do svého předplatného Azure přidat účet Azure Cosmos DB rozhraní API pro tabulky a pak do svého účtu přidat tabulky. Podporované jazyky a související rychlé starty najdete v [úvodu k Azure Cosmos DB rozhraní API pro tabulky](table-introduction.md).

## <a name="table-api-in-azure-cosmos-db-vs-azure-table-storage"></a><a id="table-api-vs-table-storage"></a>rozhraní API pro tabulky ve službě Azure Cosmos DB vs Azure Table Storage

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>V čem není rozhraní API pro tabulky shodné s chováním s Azure Table Storage?

Existují některé rozdíly v chování, které uživatelé přicházejí z Azure Table Storage, a chtějí vytvářet tabulky s Azure Cosmos DB rozhraní API pro tabulky by měli mít na paměti:

* Azure Cosmos DB rozhraní API pro tabulky používá rezervovaný model kapacity, aby zajistil Zaručený výkon, ale to znamená, že po vytvoření tabulky platí za kapacitu, a to i v případě, že se kapacita nepoužívá. S Azure Table Storage jedna platí jenom pro využitou kapacitu. To pomáhá vysvětlit, proč rozhraní API pro tabulky může nabízet smlouvu SLA 10 MS pro čtení a 15 MS Write na 99 percentilu, zatímco Azure Table Storage nabízí 10 sekund SLA. Ale jako důsledek s rozhraní API pro tabulky tabulek, a to i s prázdnými tabulkami bez jakýchkoli požadavků, a to za účelem zajištění dostupnosti kapacity pro zpracování všech žádostí o smlouvu SLA nabízených Azure Cosmos DB.

* Výsledky dotazu vrácené rozhraní API pro tabulky nejsou seřazené v pořadí podle klíče oddílu nebo klíče řádku, protože jsou v úložišti tabulek Azure.

* Klíče řádků můžou být jenom až 255 bajtů.

* Dávky můžou mít až 2 MB.

* CORS není v současné době podporovaná.

* Názvy tabulek v Azure Table Storage nerozlišují velká a malá písmena, ale jsou v Azure Cosmos DB rozhraní API pro tabulky.

* Některé z interních formátů Azure Cosmos DB pro kódování informací, jako jsou například binární pole, nejsou aktuálně tak efektivní jako jedna. Proto to může způsobit neočekávané omezení velikosti dat. Aktuálně jedna z nich například nemůže použít celý jeden typ MB entity tabulky pro ukládání binárních dat, protože kódování zvyšuje velikost dat.

* Název vlastnosti entity ' ID ' aktuálně není podporován.

* TableQuery TakeCount není omezen na 1000.

* V souvislosti s REST API je k dispozici několik možností pro koncové body nebo dotazy, které nepodporují Azure Cosmos DB rozhraní API pro tabulky:

  | Metody REST | Možnost koncový bod nebo dotaz REST | Adresy URL dokumentů | Vysvětlení |
  | ------------| ------------- | ---------- | ----------- |
  | ZÍSKAT, PUT | `/?restype=service@comp=properties`| [Nastavení vlastností služby Table](/rest/api/storageservices/set-table-service-properties) a [získání vlastností služby Table Service](/rest/api/storageservices/get-table-service-properties) | Tento koncový bod se používá k nastavení pravidel CORS, konfigurace analýzy úložiště a nastavení protokolování. CORS není momentálně podporovaná a analýzy a protokolování se v Azure Cosmos DB než v Azure Storage tabulkách zpracovávají jinak. |
  | NASTAVENÍ | `/<table-resource-name>` | [Požadavek na tabulku CORS před lety](/rest/api/storageservices/preflight-table-request) | Toto je součást CORS, kterou Azure Cosmos DB v současnosti nepodporuje. |
  | GET | `/?restype=service@comp=stats` | [Získat statistiku služby Table](/rest/api/storageservices/get-table-service-stats) | Poskytuje informace o tom, jak rychle se data replikují mezi primárním a sekundárním serverem. V Cosmos DB to není potřeba, protože replikace je součástí zápisů. |
  | ZÍSKAT, PUT | `/mytable?comp=acl` | [Získat seznam ACL tabulky](/rest/api/storageservices/get-table-acl) a [nastavit seznam ACL tabulky](/rest/api/storageservices/set-table-acl) | Tím se získá a nanastaví uložené zásady přístupu používané ke správě sdílených přístupových podpisů (SAS). I když se SAS podporuje, nastavují a spravují jinak. |

* Azure Cosmos DB rozhraní API pro tabulky podporuje jenom formát JSON, ne ATOM.

* I když Azure Cosmos DB podporuje sdílené přístupové podpisy (SAS), existují určité zásady, které nepodporují, konkrétně ty, které se týkají operací správy, jako je právo vytvářet nové tabulky.

* Konkrétně pro sadu .NET SDK existují některé třídy a metody, které Azure Cosmos DB aktuálně nepodporují.

  | Třída | Nepodporovaná metoda |
  |-------|-------- |
  | Cloudtableclient vám | \*ServiceProperties* |
  |                  | \*ServiceStats* |
  | CloudTable | SetPermissions* |
  |            | GetPermissions * |
  | TableServiceContext | * (Tato třída je zastaralá.) |
  | TableServiceEntity | " " |
  | TableServiceExtensions | " " |
  | TableServiceQuery | " " |

## <a name="other-frequently-asked-questions"></a>Další nejčastější dotazy

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Potřebuji pro používání rozhraní API pro tabulky novou sadu SDK?

Ne, existující sady SDK úložiště by měly pořád fungovat. Doporučuje se ale, aby jeden vždy dostal nejnovější sady SDK na nejlepší podporu a v mnoha případech vynikající výkon. Seznam dostupných jazyků najdete v tématu [Úvod do Azure Cosmos DB rozhraní API pro tabulky](table-introduction.md).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Jaký je připojovací řetězec, který potřebuji použít pro připojení k rozhraní API pro tabulky?

Připojovací řetězec:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmosDB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Připojovací řetězec můžete získat na stránce připojovací řetězec v Azure Portal.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Návody přepsat nastavení konfigurace pro možnosti žádosti v sadě .NET SDK pro rozhraní API pro tabulky?

Některá nastavení jsou zpracována v metodě CreateCloudTableClient a jiné prostřednictvím app.config v části appSettings v klientské aplikaci. Informace o nastavení konfigurace najdete v tématu [možnosti Azure Cosmos DB](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Existují nějaké změny pro zákazníky, kteří používají existující sady SDK služby Azure Table Storage?

Žádné Pro stávající nebo nové zákazníky, kteří používají existující sady SDK služby Azure Table Storage, nejsou žádné změny.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Návody zobrazit tabulková data uložená v Azure Cosmos DB pro použití s rozhraní API pro tabulky?

K procházení dat můžete použít Azure Portal. Můžete také použít kód rozhraní API pro tabulky nebo nástroje, které jsou uvedené v další odpovědi.

### <a name="which-tools-work-with-the-table-api"></a>Které nástroje pracují s rozhraní API pro tabulky?

Můžete použít [Průzkumník služby Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Nástroje s flexibilitou pro povýšení připojovacího řetězce ve výše zadaném formátu mohou podporovat nové rozhraní API pro tabulky. Seznam nástrojů tabulky je k dispozici na stránce [Azure Storage klientských nástrojů](../storage/common/storage-explorers.md) .

### <a name="is-the-concurrency-on-operations-controlled"></a>Je souběžnost řízených operací?

Ano, Optimistická souběžnost je poskytována prostřednictvím použití mechanismu ETag.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Je model dotazů OData podporovaný pro entity?

Ano, rozhraní API pro tabulky podporuje dotazy OData a dotaz LINQ.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Můžu se připojit k Azure Table Storage a Azure Cosmos DB rozhraní API pro tabulky vedle sebe ve stejné aplikaci?

Ano, můžete se připojit tak, že vytvoříte dvě samostatné instance Cloudtableclient vám, z nichž každý odkazuje na vlastní identifikátor URI prostřednictvím připojovacího řetězce.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Návody migrovat existující aplikaci Azure Table Storage do této nabídky?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) a [Nástroj pro migraci dat Azure Cosmos DB](import-data.md) jsou podporované.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Jak funguje rozšíření velikosti úložiště pro tuto službu, pokud například začínáte *n* GB dat a data se v průběhu času zvětší na 1 TB?

Azure Cosmos DB je navržený tak, aby poskytoval neomezené úložiště pomocí horizontálního škálování. Služba může monitorovat a efektivně rozšiřovat vaše úložiště.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Návody monitorovat nabídku rozhraní API pro tabulky?

K monitorování požadavků a využití úložiště můžete použít podokno **metriky** rozhraní API pro tabulky.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Návody vypočítat požadovanou propustnost?

Kapacitu Estimator můžete použít k výpočtu TableThroughput, který je potřeba pro operace. Další informace najdete v tématu [odhad jednotek žádostí a úložiště dat](https://www.documentdb.com/capacityplanner). Obecně můžete zobrazit vaši entitu jako JSON a zadat čísla pro vaše operace.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Můžu použít sadu rozhraní API pro tabulky SDK lokálně s emulátorem?

V tuto chvíli to není možné.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Může moje existující aplikace pracovat s rozhraní API pro tabulky?

Ano, je podporováno stejné rozhraní API.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Potřebuji migrovat stávající aplikace služby Azure Table Storage do sady SDK, pokud nechcete používat funkce rozhraní API pro tabulky?

Ne, můžete vytvářet a používat existující prostředky služby Azure Table Storage bez přerušení jakéhokoli druhu. Pokud ale rozhraní API pro tabulky nepoužíváte, nemůžete využít automatický index, další možnost konzistence nebo globální distribuci.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Návody přidat replikaci dat v rozhraní API pro tabulky napříč více než jednou oblastí Azure?

[Nastavení globální replikace](tutorial-global-distribution-sql-api.md#portal) Azure Cosmos DBového portálu můžete použít k přidání oblastí, které jsou vhodné pro vaši aplikaci. Chcete-li vytvořit globálně distribuovanou aplikaci, měli byste také přidat aplikaci s informacemi PreferredLocation nastavenými do místní oblasti pro zajištění nízké latence čtení.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Návody změnit primární oblast zápisu pro účet v rozhraní API pro tabulky?

K přidání oblasti a následnému převzetí služeb při selhání v požadované oblasti můžete použít podokno Azure Cosmos DB globálního replikačního portálu. Pokyny najdete v tématu [vývoj s využitím účtů Azure Cosmos dB ve více oblastech](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Návody nakonfigurovat preferované oblasti čtení pro nízkou latenci při distribuci dat?

K usnadnění čtení z místního umístění použijte PreferredLocation klíč v souboru app.config. U existujících aplikací rozhraní API pro tabulky vyvolá chybu, pokud je nastavené LocationMode. Odeberte tento kód, protože rozhraní API pro tabulky přebírá tyto informace ze souboru app.config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Jak se mám domnívat o úrovních konzistence v rozhraní API pro tabulky?

Azure Cosmos DB poskytuje dobře odůvodněné kompromisy mezi konzistencí, dostupností a latencí. Azure Cosmos DB nabízí pro rozhraní API pro tabulky vývojářům pět úrovní konzistence, takže můžete zvolit správný model konzistence na úrovni tabulky a při dotazování na data vytvořit jednotlivé požadavky. Když se klient připojí, může určit úroveň konzistence. Úroveň můžete změnit pomocí argumentu consistencyLevel pro CreateCloudTableClient.

Rozhraní API pro tabulky poskytuje s nízkou latencí čtení s "čtenými vlastními zápisy" s konzistencí s vazbou na neaktuálnost jako výchozí. Další informace najdete v tématu [úrovně konzistence](consistency-levels.md).

Ve výchozím nastavení poskytuje Azure Table Storage silnou konzistenci v rámci oblasti a konečnou konzistenci v sekundárních lokalitách.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Nabízí Azure Cosmos DB rozhraní API pro tabulky větší úrovně konzistence než Azure Table Storage?

Ano, informace o tom, jak těžit z distribuované povahy Azure Cosmos DB, najdete v tématu [úrovně konzistence](consistency-levels.md). Vzhledem k tomu, že záruky jsou k dispozici pro úrovně konzistence, můžete je použít s jistotou.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Pokud je povolená globální distribuce, jak dlouho trvá replikace dat?

Azure Cosmos DB potvrdí data trvale v místní oblasti a hned je pošle do jiných oblastí, a to v milisekundách. Tato replikace závisí jenom na době odezvy datacentra (RTT). Další informace o schopnostech globální distribuce Azure Cosmos DB najdete v tématu [Azure Cosmos DB: globálně distribuovaná databázová služba v Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Je možné změnit úroveň konzistence žádosti o čtení?

Pomocí Azure Cosmos DB můžete nastavit úroveň konzistence na úrovni kontejneru (v tabulce). Pomocí sady .NET SDK můžete změnit úroveň tak, že v souboru app.config zadáte hodnotu pro klíč TableConsistencyLevel. Možné hodnoty jsou: silná, ohraničená neaktuálnost, relace, konzistentní předpona a případné. Další informace najdete v tématu [přizpůsobitelné úrovně konzistence dat v Azure Cosmos DB](consistency-levels.md). Klíčovým nápadem je, že úroveň konzistence žádosti nemůžete nastavit na více než nastavení pro tabulku. Například nemůžete nastavit úroveň konzistence pro tabulku, kdykoli a úroveň konzistence žádosti na silný.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Jak rozhraní API pro tabulky převzetí služeb při selhání v případě výpadku oblasti?

Rozhraní API pro tabulky využívá globálně distribuovanou platformu Azure Cosmos DB. Aby bylo zajištěno, že vaše aplikace může tolerovat výpadky datového centra, povolte alespoň jednu oblast pro účet na portálu Azure Cosmos DB, [vývoj s využitím Azure Cosmos DBch účtů ve více oblastech](high-availability.md). Prioritu oblasti můžete nastavit pomocí portálu, který [se vyvíjí s Azure Cosmos DBmi účty ve více oblastech](high-availability.md).

Pro účet můžete přidat tolik oblastí, kolik chcete, a určit, kde při převzetí služeb při selhání bude možné převzít služby při selhání. Chcete-li použít databázi, je nutné zadat také aplikaci. Když to uděláte, vaši zákazníci nebudou mít žádný výpadek. [Nejnovější klientská sada SDK pro .NET](table-sdk-dotnet.md) je automatická odstavování, ale ostatní sady SDK ne. To znamená, že dokáže detekovat oblast, která je mimo provoz, a automaticky převezme služby při selhání nové oblasti.

### <a name="is-the-table-api-enabled-for-backups"></a>Je rozhraní API pro tabulky povoleno zálohování?

Ano, rozhraní API pro tabulky využívá platformu Azure Cosmos DB k zálohování. Zálohování se provádí automaticky. Další informace najdete v tématu [zálohování a obnovení online pomocí Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Má rozhraní API pro tabulky index všechny atributy entity ve výchozím nastavení?

Ano, všechny atributy entity jsou indexovány ve výchozím nastavení. Další informace najdete v tématu [Azure Cosmos DB: zásady indexování](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Neznamená to, že nemusíte vytvořit více než jeden index, aby bylo možné vyhovět dotazům?

Ano, Azure Cosmos DB rozhraní API pro tabulky poskytuje automatické indexování všech atributů bez definice schématu. Tato automatizace uvolní vývojářům, aby se zaměřili na aplikaci, a ne na vytváření a správu indexů. Další informace najdete v tématu [Azure Cosmos DB: zásady indexování](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Můžu změnit zásady indexování?

Ano, zásady indexování můžete změnit zadáním definice indexu. Musíte správně zakódovat a napřed nastavit řídicí.

V případě sad non-.NET SDK se dá zásada indexování nastavit jenom na portálu na **Průzkumník dat**, přejděte na konkrétní tabulku, kterou chcete změnit, a potom přejděte do části **škálování & nastavení**– >zásadu indexování, proveďte požadovanou změnu a pak ji **uložte**.

Ze sady .NET SDK je možné odeslat soubor app.config:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB jako platforma se jeví mít spoustu možností, jako je řazení, agregace, hierarchie a další funkce. Budete tyto možnosti přidávat do rozhraní API pro tabulky?

Rozhraní API pro tabulky poskytuje stejné funkce dotazů jako úložiště tabulek Azure. Azure Cosmos DB podporuje také řazení, agregace, geoprostorové dotazy, hierarchie a širokou škálu integrovaných funkcí. Další informace najdete v tématu [SQL dotazy](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Kdy bych měl změnit TableThroughput rozhraní API pro tabulky?

TableThroughput byste měli změnit, pokud platí některá z následujících podmínek:

* Provádíte extrakci, transformaci a načítání dat (ETL), nebo chcete v krátké době nahrávat velké množství dat.
* Potřebujete větší propustnost z kontejneru nebo ze sady kontejnerů na back-endu. Vidíte například, že použitá propustnost je větší než zajištěná propustnost a že se vám přihlásilo omezení. Další informace najdete v tématu [nastavení propustnosti pro kontejnery Azure Cosmos](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Můžu snížit propustnost rozhraní API pro tabulky tabulky a snížit jejich velikost?

Ano, k škálování propustnosti můžete použít podokno škále Azure Cosmos DBového portálu. Další informace najdete v tématu [nastavení propustnosti](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Je výchozí TableThroughput sada pro nově zřízené tabulky?

Ano, Pokud nepřepíšete TableThroughput prostřednictvím app.config a nepoužijete předem vytvořený kontejner v Azure Cosmos DB, služba vytvoří tabulku s propustností 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Je u stávajících zákazníků služby Azure Table Storage nějaká změna cen?

Žádné U stávajících zákazníků Azure Table Storage se cena nemění.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Jak se počítá cena za rozhraní API pro tabulky?

Cena závisí na přidělených TableThroughput.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Návody zpracování všech sazeb v tabulkách v nabídce rozhraní API pro tabulky?

Pokud je frekvence požadavků větší než kapacita zřízené propustnosti pro příslušný kontejner nebo sadu kontejnerů, zobrazí se chyba a sada SDK se znovu pokusí zavolat pomocí zásad opakování.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Proč potřebuji zvolit propustnost mimo PartitionKey a RowKey, abyste mohli využít Azure Cosmos DB rozhraní API pro tabulky nabídky?

Azure Cosmos DB nastaví výchozí propustnost pro váš kontejner, pokud ho neposkytnete v souboru app.config nebo prostřednictvím portálu.

Azure Cosmos DB poskytuje záruky pro výkon a latenci a horní meze provozu. Tato záruka je možná, když modul může vyhovět zásadám správného řízení operací klienta. Nastavení TableThroughput zajistí, že získáte zaručenou propustnost a latenci, protože platforma rezervuje tuto kapacitu a zaručuje provozní úspěch.

Pomocí specifikace propustnosti je možné elasticky změnit, aby vytěžit z sezónnost vaší aplikace, splnění potřeb propustnosti a úspory nákladů.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Služba Azure Table Storage je pro mě nenákladná, protože mám platit jenom za uložení dat a jenom zřídka dotazování. V nabídce Azure Cosmos DB rozhraní API pro tabulky se jeví, že se mi bude účtovat, i když jsem neprováděl jednu transakci nebo cokoli neuložilo. Můžete vysvětlit?

Azure Cosmos DB je navržený tak, aby byl globálně distribuovaný systém založený na smlouvě SLA se zárukami na dostupnost, latenci a propustnost. Když rezervujete propustnost v Azure Cosmos DB, zaručuje se to na rozdíl od propustnosti jiných systémů. Azure Cosmos DB poskytuje další možnosti, které si zákazníci požádali, jako jsou sekundární indexy a globální distribuce.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Nikdy nezískám plnou kvótu "oznámení" (což znamená, že oddíl je plný) při ingestování dat do služby Azure Table Storage. Po rozhraní API pro tabulky se zobrazí tato zpráva. Je tato nabídka omezená a přinutil jsem, aby mě změnila existující aplikace?

Azure Cosmos DB je systém založený na smlouvě SLA, který poskytuje neomezenou škálu, se zárukami na latenci, propustnost, dostupnost a konzistenci. Abyste zajistili Zaručený výkon Premium, ujistěte se, že je velikost a index dat spravovatelná a škálovatelná. Limit 20 GB u počtu entit nebo položek na klíč oddílu je, aby bylo zajištěno, že budeme poskytovat skvělé vyhledávání a dotaz na výkon. Aby se zajistilo, že se vaše aplikace dobře škáluje, i když Azure Storage, doporučujeme, abyste nevytvořili aktivní oddíl tím, *že budete ukládat* všechny informace v jednom oddílu a dotazovat se na něj.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Takže PartitionKey a RowKey se pořád vyžadují s rozhraní API pro tabulky?

Ano. Vzhledem k tomu, že plocha oblasti rozhraní API pro tabulky je podobná sadě SDK služby Azure Table Storage, klíč oddílu poskytuje efektivní způsob, jak distribuovat data. Klíč řádku je v rámci tohoto oddílu jedinečný. Klíč řádku musí být přítomen a nemůže být null jako v rámci standardní sady SDK. Délka RowKey je 255 bajtů a délka PartitionKey je 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Jaké jsou chybové zprávy pro rozhraní API pro tabulky?

Azure Table Storage a Azure Cosmos DB rozhraní API pro tabulky používají stejné sady SDK, takže většina chyb bude stejná.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Proč se při pokusu o vytvoření velké části tabulek jednu po druhé v rozhraní API pro tabulky vyomezovat?

Azure Cosmos DB je systém založený na smlouvě SLA, který poskytuje latenci, propustnost, dostupnost a záruky konzistence. Vzhledem k tomu, že se jedná o zřízený systém, rezervuje prostředky, aby tyto požadavky zaručoval. Je detekována rychlá míra vytváření tabulek a omezení. Doporučujeme se podívat na rychlost vytváření tabulek a snížit ji na méně než 5 za minutu. Pamatujte, že rozhraní API pro tabulky je zřízeným systémem. Okamžik, kdy ho zřídíte, začnete platit za něj.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Návody poskytnout zpětnou vazbu k sadě SDK nebo chybám?

Svůj názor můžete sdílet některým z následujících způsobů:

* [User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Stránka s otázkou Microsoft Q&](https://docs.microsoft.com/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow je nejvhodnější pro otázky týkající se programování. Ujistěte se, že je váš dotaz [v rámci vašeho tématu](https://stackoverflow.com/help/on-topic) [, a uveďte co nejvíce podrobností a vymažte otázku a odpovězte na](https://stackoverflow.com/help/how-to-ask)ně.

## <a name="next-steps"></a>Další kroky

* [Vytvoření aplikace rozhraní API pro tabulky pomocí sady .NET SDK a Azure Cosmos DB](create-table-dotnet.md)
* [Vytvoření aplikace Java pro správu rozhraní API pro tabulky dat Azure Cosmos DB](create-table-java.md)