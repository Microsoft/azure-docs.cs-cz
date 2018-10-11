---
title: Vytváření oddílů a horizontální škálování ve službě Azure Cosmos DB | Dokumentace Microsoftu
description: Další informace o tom, jak dělení funguje v Azure Cosmos DB, jak nakonfigurovat, vytváření oddílů a klíče oddílu a jak vybrat správný oddíl klíč pro vaši aplikaci.
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 968651e2bd06d54c8b735bf2418e0d84b94f315d
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078558"
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Dělení a škálování ve službě Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) je globálně distribuovaná a vícemodelová databázová služba navržená tak, aby vám pomohou dosáhnout rychlého, předvídatelného výkonu. Spolu s vaší aplikací škáluje se bez problémů. Tento článek obsahuje přehled, jak modely dělení funguje pro všechna data ve službě Azure Cosmos DB. Také popisuje, jak nakonfigurovat službu Azure Cosmos DB kontejnerů a efektivní škálování vašich aplikací.

Azure Cosmos DB podporuje následující typy kontejnerů na všechna rozhraní API:

- **Pevný kontejner**: těchto kontejnerů můžete uložit graf databáze až 10 GB velikosti s délkou maximálně 10 000 jednotek žádostí za sekundu přiřazen. Vytvořte kontejner pevnou není nutné zadat vlastnost klíče oddílu v datech.

- **Neomezený kontejner**: těchto kontejnerů může automaticky škálovat na uložení grafu nad limit 10 GB až horizontální dělení. Každý oddíl uloží 10 GB a data budou automaticky vyváženy podle **klíč zadaný oddíl**, které budou požadovaný parametr při používání neomezeného kontejneru. Tento typ kontejneru můžete uložit data prakticky neomezené velikosti a můžete povolit až 100 000 jednotek žádostí za sekundu, nebo více [kontaktovat podporu](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request).

## <a name="partitioning-in-azure-cosmos-db"></a>Dělení ve službě Azure Cosmos DB
Azure Cosmos DB poskytuje kontejnery pro ukládání dat s názvem kolekce (pro dokumenty), grafy nebo tabulky. Kontejnery jsou logické prostředky, které můžou využívat jeden nebo více fyzických oddílů nebo serverů. Počet oddílů je určena na základě velikosti úložiště služby Azure Cosmos DB a propustnosti zřízené pro kontejner nebo sadu kontejnerů. 

### <a name="physical-partition"></a>Fyzický oddíl

A *fyzické* oddíl je pevně stanovený objem rezervovaných zálohují na disky SSD úložiště v kombinaci s různého množství výpočetních prostředků (procesoru a paměti). Jednotlivé fyzické oddíly se replikují pro zajištění vysoké dostupnosti. Každá sada kontejnerů může sdílet jeden nebo více fyzických oddílů. Fyzický oddíl správy je plně spravovaná služba Azure Cosmos DB a není potřeba psát složitý kód nebo spravovat oddíly. Kontejnery služby Azure Cosmos DB neomezené z hlediska úložiště a propustnosti. Fyzické oddíly jsou interní konceptu služby Azure Cosmos DB a jsou přechodné. Azure Cosmos DB bude automaticky škálovat počet fyzických oddílů na základě vašich úloh. Proto by neměl corelate návrhu databáze na základě počtu fyzických oddílů místo toho můžete by měl nezapomeňte vybrat správný oddíl klíče, který určuje logické oddíly. 

### <a name="logical-partition"></a>Logický oddíl

A *logické* oddíl je v rámci fyzický oddíl, který ukládá všechna data přidružená k jednu hodnotu klíče oddílu. Více logické oddíly můžete skončit v jednom fyzickém oddílu. V následujícím diagramu jeden kontejner má tři logické oddíly. Každý logický oddíl uchovává data pro jeden klíč oddílu, LAX AMS a MEL v uvedeném pořadí. Všechny logické oddíly LAX AMS a MEL nemůže rozšířit nad rámec logický oddíl maximální limit 10 GB. 

![Vytváření oddílů prostředků](./media/introduction/azure-cosmos-db-partitioning.png) 

Když se splní kontejneru [dělení požadavky](#prerequisites), dělení je pro aplikaci zcela transparentní. Azure Cosmos DB zajišťuje distribuci dat napříč fyzické a logické oddíly a směrování požadavků na dotazy na správný oddíl. 

## <a name="how-does-partitioning-work"></a>Jak funguje dělení

Každý dokument musí mít *klíč oddílu* a *klíč řádku*, která ji pak jednoznačně identifikuje. Klíč oddílu funguje jako logický oddíl pro vaše data a poskytuje služby Azure Cosmos DB přirozené hranice pro distribuci dat napříč fyzickými oddíly. **Data pro jeden logický oddíl se musí nacházet uvnitř jednoho oddílu fyzického a správu fyzický oddíl je spravované službou Azure Cosmos DB**. 

Stručně řečeno tady je dělení fungování ve službě Azure Cosmos DB:

* Zřízení sadu kontejnery služby Azure Cosmos DB s **T** propustnost RU/s (počet požadavků za sekundu).  
* Na pozadí služby Azure Cosmos DB zřídí fyzických oddílů, které potřebujete k obsluze **T** požadavků za sekundu. Pokud **T** je vyšší než maximální propustnost na fyzický oddíl **t**, pak služby Azure Cosmos DB ustanovení **N T nebo t =** fyzické oddíly. Hodnota maximální propustnost na partition(t) nakonfigurovaný službou Azure Cosmos DB, tato hodnota je přiřazena na základě vaše celková zřízená propustnost a používá konfiguraci hardwaru.  
* Azure Cosmos DB přiděluje prostoru klíče oddílu klíče hash rovnoměrně napříč **N** fyzické oddíly. Tak počet logické oddíly se každý fyzický oddíl hostitele **1 nebo N** * počet hodnot klíče oddílu.  
* Když fyzický oddíl **p** dosáhne limitu úložiště služby Azure Cosmos DB bezproblémově rozdělí **p** na dva nové fyzické oddíly, **p1** a **p2**. Ale distribuuje hodnoty odpovídající přibližně polovina klíče pro každý z nové fyzické oddíly. Tato operace rozdělení je úplně skrytá do vaší aplikace. Pokud fyzický oddíl dosáhne svého limitu úložiště a všechna data v fyzický oddíl patří do stejného logického oddílu klíče, operace rozdělení se nevyskytuje. Je to proto, že všechna data pro jeden logický oddíl klíč se musí nacházet v jednom fyzickém oddílu. V takovém případě měly by být použity klíčovou strategií jiný oddíl.  
* Pokud zřídíte propustnost vyšší, než **t * N**, Azure Cosmos DB rozdělí jeden nebo více fyzických oddílů pro podporu vyšší propustnost.

Sémantika pro klíče oddílů jsou mírně odlišné tak, aby odpovídaly sémantiku každé rozhraní API, jak je znázorněno v následující tabulce:

| Rozhraní API | Klíč oddílu | Klíč řádku |
| --- | --- | --- |
| SQL | Cesta ke klíči vlastní oddíl | Oprava `id` | 
| MongoDB | Klíč horizontálního oddílu vlastní  | Oprava `_id` | 
| Gremlin | Klíčovou vlastností vlastní oddíl | Oprava `id` | 
| Table | Oprava `PartitionKey` | Oprava `RowKey` | 

Azure Cosmos DB používá dělení podle algoritmu hash. Při psaní položku služby Azure Cosmos DB hashuje hodnotu klíče oddílu a výslednou hodnotu hash používá k určení oddíl, který se pro uložení položky v. 

> [!NOTE]
> Azure Cosmos DB ukládá všechny položky se stejným klíčem oddílu v jednom fyzickém oddílu. 

## <a name="best-practices-when-choosing-a-partition-key"></a>Osvědčené postupy při výběru klíče oddílu

Volba klíče oddílu je důležité rozhodnutí, která je nutné provést v době návrhu. Vyberte název vlastnosti, která má široký rozsah hodnot a má i vzorce přístupu. Je vhodné mít klíč oddílu s velkým počtem jedinečných hodnot (například stovky nebo tisíce). Umožňuje rovnoměrně distribuuje úlohy mezi tyto hodnoty. Klíčem oddílu ideální je ten, který zobrazuje často jako filtr v dotazech a má dostatek kardinalitu Ujistěte se, že je škálovatelné řešení.

Pokud fyzický oddíl dosáhne svého limitu úložiště a data v oddílu mají stejný klíč oddílu, vrátí funkce služby Azure Cosmos DB *"klíč oddílu bylo dosaženo maximální velikosti 10 GB"* není rozdělena zprávu a oddílu. Volba vhodným klíčem oddílu je velmi důležité rozhodnutí. 

Zvolit klíč oddílu tak, aby:

* Distribuce úložiště je i přes všechny klíče.  
* Zvolte klíč oddílu, který bude rovnoměrně distribuovat data napříč oddíly.

  Je vhodné zkontrolovat, jak vaše data jsou distribuovaná napříč oddíly. Zkontrolujte distribuci dat na portálu přejděte ke svému účtu Azure Cosmos DB a klikněte na **metriky** v **monitorování** a potom klikněte na **úložiště** kartu pro zobrazení jak vaše data jsou rozdělená mezi různé fyzické oddíly.

  ![Vytváření oddílů prostředků](./media/partition-data/partitionkey-example.png)

  Levý obrázek nahoře ukazuje výsledek klíč chybný oddílu a na správné obrázku výše vidíte výsledek, pokud byla zvolena vhodným klíčem oddílu. Levého obrázku vidíte, že se data rovnoměrně distribuovaná napříč oddíly. Je nutné snažit se zvolit klíč oddílu, který distribuuje data tak, aby vypadala podobně jako na obrázku vpravo.

* Optimalizace dotazů k získání dat v rámci hranic oddílů, pokud je to možné. Optimální strategie dělení by mělo být zarovnáno na dotazování vzory. Dotazy, které získávají data z jednoho oddílu poskytují nejlepší možný výkon. Dotazy, které jsou vyvolány s vysokou souběžnosti můžete efektivně směrovat včetně klíče oddílu v predikátu filtru.  

* Volba klíče oddílu s vyšší Kardinalita je obecně upřednostňované – dokončila se obvykle poskytuje lepší distribuci a škálovatelnost. Například můžete být tvořen syntetické klíč řetězení hodnot z více vlastností pro zvýšení Kardinalita.  

Když zvolit klíč oddílu s nad aspekty, není nutné se starat o počet oddílů nebo jakou propustnost je přidělena na fyzický oddíl, protože Azure Cosmos DB škálovat počet fyzických oddílů, a také můžete škálovat jednotlivé oddíly podle potřeby.

## <a name="prerequisites"></a>Předpoklady pro dělení

Kontejnery služby Azure Cosmos DB je možné vytvořit, jak je stanoveno nebo neomezený. Kontejnery s pevnou velikostí mají omezení maximální velikosti 10 GB a propustnosti 10 000 RU/s. Pokud chcete vytvořit kontejner jako neomezený, je nutné zadat klíč oddílu a minimální propustnost 1 000 RU/s. Kontejnery služby Azure Cosmos DB můžete vytvořit také tak, aby sdílejí propustnost. V takových případech musíte každý kontejner určením klíče oddílu a můžou růst neomezený počet. 

Toto jsou požadavky ke zvážení pro dělení a škálování:

* Při vytváření kontejneru (např. kolekce, graf nebo tabulku) na webu Azure Portal, vyberte **Unlimited** možnost kapacity úložiště využívat neomezené škálování. Na fyzické oddíly automaticky rozdělit do **p1** a **p2** jak je popsáno v [jak dělení funguje](#how-does-partitioning-work) článku, je nutné vytvořit kontejner s propustností 1 000 RU/s nebo Další (nebo sdílené složky propustnosti mezi sadu kontejnerů) a musí být zadaný klíč oddílu. 

* Je-li vytvořit kontejner s počáteční propustnosti větší než nebo rovno 1 000 RU/s a zadejte klíč oddílu, můžete využít výhod neomezené škálování bez nutnosti jakkoli měnit svůj kontejner. To znamená, že i když vytvoříte **Fixed** kontejneru, je-li počáteční kontejneru se vytvoří s propustností alespoň 1 000 RU/s a pokud je zadaný klíč oddílu, kontejner funguje jako neomezený kontejner.

* Všechny kontejnery nakonfigurován pro sdílení propustnost jako součást sady kontejnery jsou považovány za **Unlimited** kontejnery.

Pokud jste vytvořili **Fixed** kontejneru bez oddílu klíč nebo propustnost menší než 1 000 RU/s, kontejner není automatického škálování. Chcete-li migrovat data z pevný kontejner do neomezeného kontejneru, budete muset použít [nástroj pro migraci dat](import-data.md) nebo [knihovna Change Feed](change-feed.md). 

## <a name="designing-for-partitioning"></a> Vytvoření klíče oddílu 
Na webu Azure portal nebo Azure CLI slouží k vytvoření kontejnerů a umožňují škálování do kdykoli. Tato část ukazuje, jak vytvořit kontejnery a zadejte zřízenou propustnost a oddíl klíče pomocí každé rozhraní API.


### <a name="sql-api"></a>SQL API
Následující příklad ukazuje, jak vytvořit kontejner (kolekci) pomocí rozhraní SQL API. 

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

Můžete si přečíst objekt pomocí položky (dokument) `GET` metoda v rozhraní REST API nebo pomocí `ReadDocumentAsync` v jedné ze sad SDK.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

Další informace najdete v tématu [dělení ve službě Azure Cosmos DB pomocí rozhraní SQL API](sql-api-partition-data.md).

### <a name="mongodb-api"></a>Rozhraní MongoDB API
Pomocí rozhraní MongoDB API můžete vytvořit horizontálně dělenou kolekci pomocí oblíbeného nástroje, ovladač nebo sady SDK. V tomto příkladu používáme Mongo Shell můžete vytvořit kolekci.

V prostředí Mongo Shell:

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

K vytvoření tabulky pomocí rozhraní Table API, použijte `CreateIfNotExists` metody. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

Zřízená propustnost je nastaven jako argument `CreateIfNotExists`. Klíč oddílu se implicitně vytvoří jako `PartitionKey` hodnotu. 

Pomocí následujícího kódu můžete načíst jednu entitu:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Další informace najdete v tématu [vývoj pomocí rozhraní Table API](tutorial-develop-table-dotnet.md).

### <a name="gremlin-api"></a>Rozhraní Gremlin API

Pomocí rozhraní Gremlin API můžete na webu Azure portal nebo rozhraní příkazového řádku Azure k vytvoření kontejneru, který představuje grafu. Případně protože Azure Cosmos DB je vícemodelová, vám pomůže jeden z rozhraní API vytvořit a škálovat svůj kontejner grafu.

> [!NOTE]
> `/id` a `/label` nejsou podporovány jako klíče oddílu pro kontejner ve službě Gremlin API nevztahují.

Na základě klíče oddílu a ID v Gremlin můžete číst všechny vrchol nebo Microsoft edge. Graf s oblasti ("USA") jako klíč oddílu a "Seattle" jako klíč řádku, například můžete najít vrcholu pomocí následující syntaxe:

```
g.V(['USA', 'Seattle'])
```

Okraj můžete odkazovat pomocí klíče oddílu a klíč řádku.

```
g.E(['USA', 'I5'])
```

Další informace najdete v tématu [pomocí dělené grafu ve službě Azure Cosmos DB](graph-partitioning.md).

## <a name="form-partition-key-by-concatenating-multiple-fields"></a>Klíč oddílu formuláře zřetězením více polí

Klíč oddílu vytvoříte, zřetězení a odsazení více hodnot vlastností do jednoho umělé "partitionKey" vlastnosti položky. Tyto klíče se označují jako syntetická klíče.

Například máte dokument, který bude vypadat takto:

```json
{
"deviceId": "abc-123",
"date": 2018
}
```

Jednou z možností je nastavit na /deviceId nebo /date partitionKey. Pokud chcete k vytvoření klíče oddílu pro id zařízení a data. Tyto dvě hodnoty na vlastnost umělé "partitionKey" zřetězit a nastavit klíč oddílu /partitionKey.

```json
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

V reálném čase scénáře mohou obsahovat tisíce dokumenty, takže byste měli definovat logiku na straně klienta pro řetězení hodnot do syntetické klíče, vložte syntetické klíč dokumenty a použilo ji k určení klíče oddílu.

## <a name="designing-for-scale"></a> Návrh pro zajištění škálování
Efektivně škálovat s využitím služby Azure Cosmos DB, budete muset vybrat vhodným klíčem oddílu při vytváření vašeho kontejneru. Existují dva hlavní faktory pro výběr vhodným klíčem oddílu:

* **Dotazování hranice a transakce**. Zvoleného klíče oddílu by měl zajistit rovnováhu mezi nutnost používat transakce proti požadavku entity distribuovat napříč více klíčů oddílů pro zajištění škálovatelné řešení. Na jeden extreme stejným klíčem oddílu můžete nastavit pro všechny položky, ale tato možnost může omezit škálovatelnost řešení. V jiných extreme můžete přiřadit jedinečného klíče oddílu pro každou položku. Tato volba je vysoce škálovatelné, ale to zabrání pomocí transakce mezi dokumenty pomocí uložené procedury a triggery. Klíčem oddílu ideální vám umožní použít efektivní dotazy a má dostatek kardinalitu Ujistěte se, že je škálovatelné řešení. 
* **Žádné úložiště a výkon kritických bodů**. Je důležité vybrat vlastnost, která umožňuje zápis distribuovat napříč různými jedinečných hodnot. Požadavky na stejný klíč oddílu nemůže být delší než zřízená propustnost přidělené do oddílu a bude míra limited. Proto je důležité pro výběr klíče oddílu, který není výsledkem "aktivní body" v rámci vaší aplikace. Protože veškerá data pro jeden klíč oddílu musí být uložen v rámci oddílu, měli byste se vyhnout klíče oddílů, které mají velké objemy dat pro stejnou hodnotu. 

Podívejme se na několik scénářů reálného světa a správného klíče pro každý:
* Pokud implementujete uživatelského profilu back-endu, *ID uživatele* je dobrou volbou pro klíč oddílu.
* Pokud ukládáte data IoT, například stav zařízení *ID zařízení* je dobrou volbou pro klíč oddílu.
* Pokud používáte službu Azure Cosmos DB pro protokolování dat časových řad *hostname* nebo *ID procesu* je dobrou volbou pro klíč oddílu.
* Pokud máte víceklientské architektury, *ID tenanta* je dobrou volbou pro klíč oddílu.

V některých používat případy jako IoT a uživatelské profily, klíč oddílu může být stejný jako vaše *ID* (klíč dokumentu). V jiné, jako jsou data časových řad, bude pravděpodobně klíč oddílu, který se liší od *ID*.

### <a name="partitioning-and-loggingtime-series-data"></a>Vytváření oddílů a protokolování/časových řad dat
Jednou z běžných případů použití ve službě Azure Cosmos DB je protokolování a telemetrie. Je důležité vybrat v tomto scénáři vhodným klíčem oddílu, protože možná budete muset obrovské objemy dat pro čtení a zápisu. Volba vhodná pro klíč oddílu závisí na sazby pro čtení a zápis a typy dotazů, které chcete spustit. Tady jsou některé tipy, jak zvolit vhodným klíčem oddílu:

* Pokud vašemu případu použití zahrnuje malý počet zápisů, které se shromažďují za dlouhou dobu a které potřebujete k dotazování podle rozsahů časová razítka s jinými filtry, použijte kumulativní časového razítka. Dobrý nápad je třeba použít datum jako klíč oddílu. S tímto přístupem můžete vyhledávat napříč všemi daty daného data z jednoho oddílu. 
* Pokud je úloha malého rozsahu zápisu náročná na výkon, což je velmi běžné v tomto scénáři, použijte klíč oddílu, který není založen na časové razítko. V důsledku toho služby Azure Cosmos DB můžete distribuovat a škálovat zápisy rovnoměrně mezi různé oddíly. Tady *hostname*, *ID procesu*, *ID aktivity*, nebo jiné vlastnosti s velkou mohutností, je dobrou volbou. 
* Další možností je s hybridním přístupem, pokud máte více kontejnerů, jeden pro každý den/měsíc, a klíče oddílu je podrobnější vlastnost jako *hostname*. Tento přístup má výhodu, kterou můžete nastavit různé propustnost pro každý kontejner nebo sadu kontejnerů na základě časového okna a potřebám výkonu a škálování. Například kontejner pro aktuální měsíc mohou být poskytnuty služby vyšší propustnost, protože slouží čte a zapisuje. Předchozí měsíce může být poskytnuty služby nižší propustnost, protože slouží pouze čtení.

### <a name="partitioning-and-multitenancy"></a>Vytváření oddílů a víceklientskou architekturu
Pokud implementujete víceklientské aplikaci využívající službu Azure Cosmos DB, existují dvě oblíbené návrhy vzít v úvahu: *jeden oddíl klíčem na každého tenanta* a *jeden kontejner každého tenanta*. Tady jsou výhody a nevýhody pro každou:

* **Jeden oddíl klíčem na každého tenanta**. V tomto modelu se tenanti společně umístěných v rámci jednoho kontejneru. Dotazy a operace vložení pro jednoho tenanta může být provedena proti jednoho oddílu. Můžete také implementovat transakční logiky přes všechny položky, které patří do tenanta. Vzhledem k tomu, že několik tenantů sdílí kontejneru, můžete lépe využít úložiště a zřízenou propustnost vytváření fondů zdrojů pro všechny klienty v rámci jednoho kontejneru, spíše než zřizování pro každého tenanta. Nevýhodou je, že není nutné izolaci výkonu každého tenanta. Zvýšení propustnosti pro zajištění výkonu použije k celému kontejneru s za všechny tenanty a cílové zvýšení pro jednoho klienta.
* **Jeden kontejner každého tenanta**. V tomto modelu každý tenant má vlastní kontejner a rezervujete propustnost s garantovanou výkon jednotlivých tenantů. Tento model je úspornější pro víceklientské aplikace s několika klienty.

Můžete použít také s hybridním přístupem, které společně colocates malých tenantů a izoluje větší tenanti pro své vlastní kontejnery.

## <a name="next-steps"></a>Další postup
V tomto článku jsme zajistili Přehled konceptů a osvědčené postupy pro škálování a rozdělení do oddílů ve službě Azure Cosmos DB. 

* Další informace o [zřízenou propustnost v databázi Azure Cosmos DB](request-units.md).
* Další informace o [globální distribuce ve službě Azure Cosmos DB](distribute-data-globally.md).



