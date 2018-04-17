---
title: Vytváření oddílů a horizontální škálování v Azure Cosmos DB | Microsoft Docs
description: Další informace o tom, jak rozdělení funguje v Azure Cosmos DB, jak nakonfigurovat, vytváření oddílů a oddílu klíče a jak vybrat klíč správné oddílu pro vaši aplikaci.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fcb33dff131106fd801b72a0bfaafd528d9f1af9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Oddíl a škálování v Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) je služba globálně distribuované, multimodel databáze navržené tak, aby vám pomohou dosáhnout rychlé, předvídatelný výkon. Se škáluje bezproblémově společně s vaší aplikace ho s růstem. Tento článek obsahuje přehled jak modely dělení platí pro všechna data v Azure Cosmos DB. Také popisuje konfiguraci kontejnery Azure Cosmos DB efektivní škálování vašich aplikací.

Dělení a klíče oddílu jsou popsané v tomto videu Azure Cosmos DB Program Správce Andrew Liu:

> [!VIDEO https://www.youtube.com/embed/SS6WrQ-HJ30]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Vytváření oddílů v Azure Cosmos DB
V Azure DB Cosmos můžete ukládat a zadávat dotazy na data bez schémat s pořadí milisekundu odezvy v jakémkoli měřítku. Azure Cosmos DB poskytuje kontejnery pro ukládání dat volat *kolekce* (pro dokumenty), *grafy*, nebo *tabulky*. 

Kontejnery jsou logické prostředky a může mít rozsah jeden nebo více fyzických oddílů nebo serverů. Počet oddílů je dáno Azure DB Cosmos na základě velikosti úložiště a zřízené propustnosti kontejneru. 

Fyzickém oddílu je pevně stanovený objem vyhrazeného úložiště SSD zálohována. Každý fyzický oddíl se replikují pro vysokou dostupnost. Jeden nebo více fyzických oddílů tvoří kontejner. Správa fyzickém oddílu je plně spravovat Azure Cosmos DB a není nutné zapsat složitý kód nebo spravovat vaše oddíly. Kontejnery Azure Cosmos DB neomezená z hlediska úložiště a propustnosti. 

Logický oddíl je oddíl v rámci fyzické oddílu, který ukládá všechna data související s hodnotou klíče jeden oddíl. Logický oddíl má maximální 10 GB. V následujícím diagramu jediný kontejner má tři logické oddíly. Každý logický oddíl ukládá data pro jeden klíč oddílu, LAX AMS a MEL v uvedeném pořadí. Všechny logické oddíly LAX AMS a MEL nelze růst překračuje limit maximální logický oddíl 10 GB. 

![Dělení prostředků](./media/introduction/azure-cosmos-db-partitioning.png) 

Když kolekci splňuje [dělení požadavky](#prerequisites), v rámci vytváření oddílů je transparentní do vaší aplikace. Azure Cosmos DB podporuje rychlé čtení a zápisy, dotazy, transakční logiku, úrovně konzistence a řízení přístupu podrobných prostřednictvím metody nebo rozhraní API pro jediný kontejner prostředků. Služba zpracovává rozděluje data mezi fyzické a logické oddíly a směrování dotaz požadavky na pravém oddílu. 

## <a name="how-does-partitioning-work"></a>Jak funguje dělení

Jak funguje dělení Každá položka musí mít klíč oddílu a klíč řádku, které jeho jednoznačné identifikaci. Klíč oddílu funguje jako logický oddíl pro vaše data a poskytne Azure Cosmos DB přirozené hranice pro rozděluje data mezi fyzické oddíly. Mějte na paměti, že data pro jeden logický oddíl se musí nacházet v jednom fyzickém oddílu, ale fyzickém oddílu management spravuje Azure Cosmos DB. 

Stručně řečeno zde je Princip vytváření oddílů v Azure Cosmos DB:

* Zřídit kontejner Azure Cosmos DB s **T** požadavků za druhé propustnost.
* Na pozadí Azure Cosmos DB zřídí oddíly, které jsou potřebné k obsluze **T** požadavků za sekundu. Pokud **T** je vyšší než maximální propustnost na oddíl **t**, pak Azure Cosmos DB zřizuje **N = T/t** oddíly.
* Azure Cosmos DB přiděluje místo na klíče oddílu klíče hash rovnoměrně napříč **N** oddíly. Takže každý oddíl (fyzickém oddílu) hostitele **1 nebo N** oddílu hodnoty klíče (logické oddíly).
* Při fyzickém oddílu **p** dosáhnou limitu úložiště Azure Cosmos DB bezproblémově rozdělí **p** do dvou nových oddílů, **p1** a **p2** . Distribuuje hodnoty odpovídající přibližně poloviční klíče pro každou nadefinovaných oddílů. Toto rozdělení operace je pro vaše aplikace skrytá. Pokud fyzickém oddílu dosáhne limitu úložiště a všechna data na fyzickém oddílu patří do stejného klíče logický oddíl, neproběhne operaci rozdělení. Je to proto, že všechna data pro klíč jeden logický oddíl se musí nacházet v jednom fyzickém oddílu, a proto fyzickém oddílu nelze rozdělit na p1 a p2. V takovém případě měly by být použity strategie klíče jiný oddíl.
* Pokud zřídíte propustnost vyšší než **t * N**, Azure Cosmos DB rozdělí jeden nebo více oddíly mohou podporovat vyšší propustnost.

Sémantika pro klíče oddílů je mírně odlišný tak, aby odpovídaly sémantika každé rozhraní API, jak je znázorněno v následující tabulce:

| Rozhraní API | Klíč oddílu | Klíč řádku |
| --- | --- | --- |
| Azure Cosmos DB | Cesta klíče vlastní oddíl | Oprava `id` | 
| MongoDB | Vlastní sdílený klíč  | Oprava `_id` | 
| Graph | klíčovou vlastností vlastní oddíl | Oprava `id` | 
| Table | Oprava `PartitionKey` | Oprava `RowKey` | 

Azure Cosmos DB používá algoritmus HMAC rozdělení do oddílů. Při zápisu položky Azure Cosmos DB hashuje hodnotu klíče oddílu a hash výsledek používá k určení oddíl, který k uložení položky v. Azure Cosmos DB ukládá všechny položky se stejným klíčem oddílu v jednom fyzickém oddílu. Volba klíč oddílu je důležité rozhodnutí, která je nutné provést v době návrhu. Je třeba vybrat název vlastnosti, která má široký rozsah hodnot a má i přístupové vzorce. Pokud fyzickém oddílu dosáhne úložiště limit a stejným klíčem oddílu je na všech dat v oddílu, Azure Cosmos DB vrátí chybu "klíč oddílu dosáhl maximální velikosti 10 GB" a není rozdělena na oddíl, proto výběr klíč oddílu je velmi import ant rozhodnutí.

> [!NOTE]
> Je vhodné mít klíč oddílu s mnoha jedinečných hodnot (několika set k tisícům minimálně).
>

Kontejnery Azure Cosmos DB se dá vytvořit jako *pevné* nebo *neomezená* na portálu Azure. Kontejnery s pevnou velikostí mají omezení maximální velikosti 10 GB a propustnosti 10 000 RU/s. Pokud chcete vytvořit kontejner jako neomezená, musíte zadat minimální propustnost 1000 RU/s a je nutné zadat klíč oddílu.

Je vhodné zkontrolovat, jak se vaše data rozděluje v oddílech. Chcete-li zaškrtněte toto políčko portálu, přejděte ke svému účtu Azure Cosmos DB a klikněte na **metriky** v **monitorování** tématu a potom v pravém podokně klikněte na **úložiště** zjistit, jak vaše data jsou oddíly v různých fyzických oddílu.

![Dělení prostředků](./media/partition-data/partitionkey-example.png)

Na levém obrázku vidíte výsledek klíč oddílu chybnou a pravém obrázek ukazuje výsledek vhodným klíčem oddílu. V levém bitovou kopii uvidíte, že data není rovnoměrně rozdělené mezi oddílů. Měli snažit distribuci dat tak, že vaše grafu vypadá podobně jako správné bitové kopie.

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>Požadavky pro vytváření oddílů

Pro fyzický oddíly, které mají automaticky rozdělení do **p1** a **p2** jak je popsáno v [jak rozdělení funguje](#how-does-partitioning-work), kontejneru musí být vytvořeny s propustností RU/s 1 000 nebo více , a je třeba zadat klíč oddílu. Při vytváření kontejneru na portálu Azure, vyberte **neomezený** možnost kapacity úložiště využívat výhod vytváření oddílů a automatické škálování. 

Pokud jste vytvořili kontejner na portálu Azure nebo z programu a počáteční propustnost je 1 000 RU/s nebo informace a data obsahují klíč oddílu, můžete využít výhod oddíly bez změny do vašeho kontejneru – to zahrnuje **opraveno**  velikost kontejnery, tak dlouho, dokud počáteční kontejner byl vytvořen s alespoň 1 000 RU/s v througput a klíč oddílu je k dispozici v datech.

Pokud jste vytvořili **pevné** velikost kontejner s žádné oddílem klíče nebo vytvořené **pevné** velikost kontejneru propustnost menší než 1 000 RU/s, kontejner nelze automaticky rozdělení jak je popsáno v tomto článku. Chcete-li migrovat data z kontejneru takto s kontejnerem neomezená (jedna s alespoň 1 000 RU/s v propustnosti a klíč oddílu), je potřeba použít [nástroj pro migraci dat](import-data.md) nebo [změnu kanálu knihovny](change-feed.md) k migrujte změny. 

## <a name="partitioning-and-provisioned-throughput"></a>Vytváření oddílů a zřízené propustnosti
Azure Cosmos DB je určená pro předvídatelný výkon. Když vytvoříte kontejner, můžete vyhradit propustnost z hlediska  *[požadované jednotky](request-units.md) (RU) za sekundu*. Každý požadavek není přiřazen RU zdarma, který tvoří hlavně množství systémové prostředky jako procesoru, paměti a vstupně-výstupní operace spotřebovávají operaci. Čtení 1 KB dokumentu s konzistence typu relace využívá 1 RU. Pro čtení je 1 RU bez ohledu na počet položek, které jsou uložené nebo počet souběžných požadavků, které se spouští ve stejnou dobu. Položky větší vyžadují vyšší RUs v závislosti na velikosti. Pokud znáte velikost vaší entity a počet čtení, které budete potřebovat k podpoře pro aplikaci, můžete zřídit přesné množství propustnost požadované pro vaše aplikace je vyžaduje čtení. 

> [!NOTE]
> K dosažení úplné propustnosti kontejneru, je nutné zvolit klíč oddílu, který umožňuje rovnoměrně rozdělit požadavky mezi některé hodnoty klíče jedinečné oddílu.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Práce s Azure Cosmos DB rozhraní API
Portál Azure nebo rozhraní příkazového řádku Azure slouží k vytvoření kontejnerů a škálování je kdykoli. Tato část ukazuje způsob vytvoření kontejnerů a zadejte definici klíče propustnost a oddílu v každé z podporovaných rozhraní API.

### <a name="sql-api"></a>SQL API
Následující příklad ukazuje, jak vytvořit kontejner (kolekce) pomocí rozhraní API služby Azure Cosmos databáze SQL. 

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Si můžete přečíst položku (dokument) pomocí `GET` metoda v rozhraní REST API nebo pomocí `ReadDocumentAsync` v jednom ze sady SDK.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

Další informace najdete v tématu [vytváření oddílů v Azure DB Cosmos pomocí rozhraní SQL API](sql-api-partition-data.md).

### <a name="mongodb-api"></a>Rozhraní MongoDB API
S rozhraním API pro MongoDB můžete vytvořit kolekci horizontálně dělené prostřednictvím vaše oblíbené nástroje, ovladače nebo sady SDK. V tomto příkladu používáme prostředí Mongo pro vytvoření kolekce.

V prostředí Mongo:

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
Výsledky:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

### <a name="table-api"></a>Rozhraní Table API

K vytvoření tabulky pomocí rozhraní API služby Azure DB Cosmos tabulky, použijte metodu CreateIfNotExists. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

Propustnost je nastaven jako argument CreateIfNotExists.

Klíč oddílu je vytvořena implicitně jako `PartitionKey` hodnotu. 

Jedna entita můžete načíst pomocí následující fragment kódu:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Další informace najdete v tématu [vývoj s rozhraním API pro tabulku](tutorial-develop-table-dotnet.md).

### <a name="graph-api"></a>Graph API

Rozhraní Graph API musíte použít portál Azure nebo rozhraní příkazového řádku Azure k vytvoření kontejnerů. Alternativně vzhledem k tomu, že Azure Cosmos DB multimodel, můžete další modely vytvořit a škálovat vaše kontejneru grafu.

Pomocí klíč oddílu a ID v Gremlin může číst všechny vrchol nebo Microsoft edge. Například pro graf s oblasti ("USA") jako klíč oddílu a "Seattle" jako klíč řádku, můžete vyhledat vrchol pomocí následující syntaxe:

```
g.V(['USA', 'Seattle'])
```

Okraj můžete odkazovat pomocí klíč oddílu a klíč řádku.

```
g.E(['USA', 'I5'])
```

Další informace najdete v tématu [pomocí oddílů grafu v Azure Cosmos DB](graph-partitioning.md).


<a name="designing-for-partitioning"></a>
## <a name="design-for-partitioning"></a>Návrh pro dělení
Efektivní škálování s Azure Cosmos DB, musíte vybrat vhodným klíčem oddílu, při vytváření vašeho kontejneru. Existují dvě hlavní faktory pro výběr klíč oddílu:

* **Hranice pro dotaz a transakce**. Vaši volbu klíč oddílu by měl vyrovnávat potřeba povolit používání transakce proti požadavku, distribuovat vaší entity napříč více klíčů oddílů zajistit škálovatelné řešení. V jedné extreme stejným klíčem oddílu můžete nastavit pro všechny položky, ale tato možnost může omezit škálovatelnost řešení. V jiných extreme můžete přiřadit oddílu jedinečný klíč pro každou položku. Tato volba je vysoce škálovatelné, ale jeho zabrání pomocí transakce mezi dokumenty prostřednictvím uložených procedur a aktivačních událostí. Klíčem ideální oddílu vám umožní využít efektivní dotazy a má dostatek mohutnost zajistit, že vaše řešení je škálovatelná. 
* **Žádné úložiště a výkon kritických bodů**. Je důležité vybrat vlastnost, která umožňuje zápis být distribuován do různých jedinečných hodnot. Požadavky na stejným klíčem oddílu nesmí být delší než propustnost jednoho oddílu a jsou omezené. Proto je důležité vybrat klíč oddílu, který nevede k "aktivní body" v rámci vaší aplikace. Vzhledem k tomu, že všechna data pro jeden oddíl klíč musí být uložen v rámci oddílu, neměli byste klíče oddílů, které mají velký objem dat pro stejnou hodnotu. 

Podívejme se na několik reálných scénářů a klíče dobrý oddílů pro každou:
* Pokud jste implementace uživatelského profilu back-end, ID uživatele je vhodná pro klíč oddílu.
* Pokud ukládáte data IoT, například stav zařízení je ID zařízení dobrou volbou pro klíč oddílu.
* Pokud používáte Azure Cosmos DB pro protokolování data časové řady, název hostitele nebo proces ID je vhodná pro klíč oddílu.
* Pokud máte víceklientské architektury, ID klienta je vhodná pro klíč oddílu.

V některých případech, jako je IoT a uživatelské profily použití, klíč oddílu může být stejný jako vaše ID (klíč dokumentu). V jiných případech jako data časové řady, můžete mít klíč oddílu, který se liší od ID.

### <a name="partitioning-and-loggingtime-series-data"></a>Vytváření oddílů a protokolování nebo časových řad dat
Mezi běžné případy použití Azure Cosmos databáze je pro protokolování a telemetrie. Je důležité vybrat vhodným klíčem oddílu, protože může být nutné obrovské objemy dat pro čtení a zápis. Volba závisí na sazby pro čtení a zápis a typy dotazů, které chcete spustit. Zde jsou některé tipy, jak zvolit vhodným klíčem oddílu:

* Pokud váš případ použití zahrnuje malý počet zápisů, které se nashromáždí přes dlouhou dobu a které potřebujete k dotazování podle rozsahů časová razítka a ostatní filtry, použijte souhrnu časového razítka. Například je dobrý způsob použití datum jako klíč oddílu. Při tomto postupu se můžete dotazovat přes všechna data pro datum z jednoho oddílu. 
* Pokud vaše úlohy je zapsán náročné, což je více obvyklé, použijte klíč oddílu, který není založen na časové razítko. S tímto přístupem můžete rovnoměrně rozmístit zápisy přes různé oddíly Azure Cosmos DB. Název hostitele, ID procesu, ID aktivity nebo jinou vlastnost s vysokou kardinalitou tady je vhodné použít. 
* Další možností je hybridní, jeden kde máte více kontejnerů, jednu pro každý den/měsíc, a klíč oddílu je podrobné vlastnosti, jako je název hostitele. Tento přístup má výhodu, kterou můžete nastavit různé propustnost podle časový interval. Například je zajištěna kontejner pro aktuální měsíc s vyšší propustnost, protože slouží čte a zapisuje. Předchozích měsíců jsou zřizovány s nižší propustnost, protože slouží pouze čtení.

### <a name="partitioning-and-multitenancy"></a>Vytváření oddílů a víceklientskou architekturu
Pokud jste implementace víceklientské aplikace pomocí Azure Cosmos DB, jsou dva oblíbených vzory: klíč jeden oddíl na klienta a jednoho kontejneru typu každého klienta. Zde jsou výhody a nevýhody pro každou:

* **Klíč oddílu jeden na každého klienta**. V tomto modelu jsou společně umístěná klientů v rámci jednoho kontejneru. Ale dotazy a vložení pro položky v rámci jednoho klienta je možné provádět proti jeden oddíl. Můžete také implementovat logiku transakcí mezi všechny položky v rámci klienta. Vzhledem k tomu, že více klientů sdílet kontejner, můžete uložit náklady na úložiště a propustnost sdružování prostředků pro klienty v rámci jednoho kontejneru spíše než zřizování navíc rezervou pro každého klienta. Nevýhodou je, že nemáte izolaci výkonu každého klienta. Zvýšení výkonu nebo propustnost platí pro celou kontejneru versus cílové zvyšuje pro klienty.
* **Jeden kontejner každého klienta**. V tomto modelu má každý klient vlastní kontejner a je možné rezervovat výkonu každého klienta. Tento model je s Azure DB Cosmos nový zajišťování ceny, cenově pro víceklientské aplikace s několika klienty.

Můžete také vrstvené nebo kombinaci přístup, collocates malé klientů a migraci větší klienty do svých vlastních kontejneru.

## <a name="next-steps"></a>Další postup
V tomto článku jsme poskytuje přehled o konceptech a osvědčené postupy pro vytváření oddílů s jakéhokoli rozhraní API Azure Cosmos DB. 

* Další informace o [zřízené propustnosti v Azure Cosmos DB](request-units.md).
* Další informace o [globální distribuce v Azure Cosmos DB](distribute-data-globally.md).



