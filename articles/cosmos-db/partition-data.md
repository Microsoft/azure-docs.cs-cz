---
title: Vytváření oddílů a horizontální škálování v Azure Cosmos DB | Microsoft Docs
description: Další informace o tom, jak rozdělení funguje v Azure Cosmos DB, jak nakonfigurovat, vytváření oddílů a oddílu klíče a jak vybrat klíč správné oddílu pro vaši aplikaci.
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 046e45978d401e05d0ab8154aff994052f5d7717
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36960368"
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Oddíl a škálování v Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) je služba globálně distribuované a více modelech databáze navržené tak, aby vám pomohou dosáhnout rychlé, předvídatelný výkon. Se škáluje bezproblémově společně s vaší aplikace ho s růstem. Tento článek obsahuje přehled jak modely dělení platí pro všechna data v Azure Cosmos DB. Také popisuje konfiguraci kontejnery Azure Cosmos DB efektivní škálování vašich aplikací.

Dělení a klíče oddílu jsou popsané v tomto videu:

> [!VIDEO https://www.youtube.com/embed/SS6WrQ-HJ30]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Vytváření oddílů v Azure Cosmos DB
V Azure DB Cosmos můžete ukládat a zadávat dotazy na data bez schémat s latencí milisekundu jediná číslice v jakémkoli měřítku. Azure Cosmos DB poskytuje kontejnery pro ukládání dat volat *kolekce* (pro dokumenty), *grafy*, nebo *tabulky*. 

Kontejnery jsou logické prostředky a může mít rozsah jeden nebo více fyzických oddílů nebo serverů. Počet oddílů je dáno Azure DB Cosmos na základě velikosti úložiště a zřízené pro kontejner nebo sadu kontejnery propustnosti. 

A *fyzické* oddíl je pevně stanovený objem vyhrazeného zálohovaná na SSD úložiště v kombinaci s proměnnou velikostí výpočetní prostředky (procesor a paměť). Každý fyzický oddíl se replikují pro vysokou dostupnost. Každá sada kontejnerů může sdílet jeden nebo více fyzických oddílů. Správa fyzickém oddílu je plně spravovat Azure Cosmos DB a není nutné zapsat složitý kód nebo spravovat vaše oddíly. Kontejnery Azure Cosmos DB neomezená z hlediska úložiště a propustnosti. 

A *logické* oddíl, je v rámci fyzické oddílu, který ukládá všechna data související s hodnotou klíče jeden oddíl. Více logické oddíly můžou skončit ve stejném fyzickém oddílu. V následujícím diagramu jediný kontejner má tři logické oddíly. Každý logický oddíl ukládá data pro jeden klíč oddílu, LAX AMS a MEL v uvedeném pořadí. Všechny logické oddíly LAX AMS a MEL nelze růst překračuje limit maximální logický oddíl 10 GB. 

![Dělení prostředků](./media/introduction/azure-cosmos-db-partitioning.png) 

Když kontejner splňuje [dělení požadavky](#prerequisites), rozdělení do oddílů je pro aplikaci zcela transparentní. Azure Cosmos DB podporuje rychlé čtení a zápisy, dotazy, transakční logiku, úrovně konzistence a řízení přístupu podrobných prostřednictvím metody nebo rozhraní API pro jediný kontejner prostředků. Služba zpracovává rozděluje data mezi fyzické a logické oddíly a směrování požadavků na dotazy do správné oddílu. 

## <a name="how-does-partitioning-work"></a>Jak funguje dělení

Jak funguje dělení Každá položka musí mít *klíč oddílu* a *klíč řádku*, který jedinečně identifikovat. Klíč oddílu funguje jako logický oddíl pro vaše data a poskytne Azure Cosmos DB přirozené hranice pro rozděluje data mezi fyzické oddíly. Data pro jeden logický oddíl se musí nacházet v jednom fyzickém oddílu, ale fyzickém oddílu management spravuje Azure Cosmos DB. 

Stručně řečeno zde je Princip vytváření oddílů v Azure Cosmos DB:

* Zřizovat sadu Azure Cosmos DB kontejnery s **T** propustnost RU/s (počet požadavků za sekundu).
* Na pozadí Azure Cosmos DB zřídí fyzické oddíly, které jsou potřebné k obsluze **T** požadavků za sekundu. Pokud **T** je vyšší než maximální propustnost za fyzickém oddílu **t**, pak Azure Cosmos DB zřizuje **N = T/t** fyzické oddíly. Hodnota maximální propustnost za partition(t) se nakonfiguruje prostřednictvím Azure Cosmos DB, tato hodnota je přiřazen na základě celkové zřízené propustnosti a používá konfiguraci hardwaru. 
* Azure Cosmos DB přiděluje místo na klíče oddílu klíče hash rovnoměrně napříč **N** fyzické oddíly. Tak počet logické oddíly jednotlivých hostitelů fyzickém oddílu je **1 nebo N** * počet hodnoty klíče oddílu.
* Při fyzickém oddílu **p** dosáhnou limitu úložiště Azure Cosmos DB bezproblémově rozdělí **p** do dvou nových oddílů fyzické **p1** a **p2**. Distribuuje hodnoty odpovídající přibližně polovinu klíče pro každý z nové fyzické oddíly. Toto rozdělení operace je pro vaše aplikace úplně skrytá. Pokud fyzickém oddílu dosáhne limitu úložiště a všechna data na fyzickém oddílu patří do stejného klíče logický oddíl, neproběhne operaci rozdělení. Je to proto, že všechna data pro klíč jeden logický oddíl se musí nacházet v jednom fyzickém oddílu. V takovém případě měly by být použity strategie klíče jiný oddíl.
* Pokud zřídíte propustnost vyšší než **t * N**, Azure Cosmos DB rozdělí jeden nebo více fyzických oddíly mohou podporovat vyšší propustnost.

Sémantika pro klíče oddílů je mírně odlišný tak, aby odpovídaly sémantika každé rozhraní API, jak je znázorněno v následující tabulce:

| Rozhraní API | Klíč oddílu | Klíč řádku |
| --- | --- | --- |
| SQL | Cesta klíče vlastní oddíl | Oprava `id` | 
| MongoDB | Vlastní horizontálních klíč  | Oprava `_id` | 
| Gremlin | Klíčovou vlastností vlastní oddíl | Oprava `id` | 
| Table | Oprava `PartitionKey` | Oprava `RowKey` | 

Azure Cosmos DB používá algoritmus HMAC rozdělení do oddílů. Při zápisu položky Azure Cosmos DB hashuje hodnotu klíče oddílu a hash výsledek používá k určení oddíl, který k uložení položky v. Azure Cosmos DB ukládá všechny položky se stejným klíčem oddílu v jednom fyzickém oddílu. 

## <a name="best-practices-when-choosing-a-partition-key"></a>Osvědčené postupy při výběru klíč oddílu

Volba klíč oddílu je důležité rozhodnutí, která je nutné provést v době návrhu. Vyberte název vlastnosti, která má široký rozsah hodnot a má i přístupové vzorce. Je vhodné mít klíč oddílu s velkým počtem jedinečných hodnot (např. stovkami nebo tisíci). Umožňuje rovnoměrně rozdělit vaše zatížení mezi tyto hodnoty. Klíč ideální oddílu je ten, který se často zobrazí jako filtr ve své dotazy a má dostatek mohutnost zajistit, že vaše řešení je škálovatelná.

Pokud fyzickém oddílu dosáhne limitu úložiště a data v oddílu se stejným klíčem oddílu, vrátí Azure Cosmos DB *"klíč oddílu dosáhl maximální velikosti 10 GB"* zprávu a oddíl není rozdělení. Výběr vhodným klíčem oddílu je velmi důležité rozhodnutí. Fyzické oddíly jsou interní konceptu Azure Cosmos DB a přechodný. Azure Cosmos DB bude automaticky škálovat počet fyzických oddílů podle velikosti pracovní zátěže. Proto by nemělo corelate návrhu databáze na základě počtu fyzických oddíly místo toho byste měli vybrat klíč oddílu vpravo (logické oddíly). 

Vyberte klíč oddílu tak, aby:

* Rozdělení úložiště je i přes všechny klíče.
* Distribuce svazku požadavků k danému bodu v čase je i přes všechny klíče.
* Dotazy, které jsou spuštěny se vysoké souběžnosti může efektivně směrovat tak, že začleníte klíč oddílu v predikátu filtru.  
* Výběr klíč oddílu s vyšší Mohutnost je obecně upřednostňované – becaue je obvykle poskytuje lepší škálovatelnost a distribuce. Složený klíč může být například tvořena zřetězením hodnoty z více vlastností zvýšit na kardinalitu. 

Když zvolíte klíč oddílu s výše aspekty, nemusíte starat o počet oddílů nebo jakou propustnost je přidělený na fyzickém oddílu, protože Azure Cosmos DB škáluje snížit počet fyzických oddíly a můžete škálovat také jednotlivé oddíly podle potřeby.

Kontejnery Azure Cosmos DB se dá vytvořit jako *pevné* nebo *neomezená* na portálu Azure. Kontejnery s pevnou velikostí mají omezení maximální velikosti 10 GB a propustnosti 10 000 RU/s. Pokud chcete vytvořit kontejner jako neomezená, je nutné zadat klíč oddílu a minimální propustnost 1000 RU/s. Kontejnery Azure Cosmos DB lze rovněž konfigurovat sdílení propustnost mezi sadu kontejnery, ve kterých musí každý kontejner určením oddíl klíče a můžou růst neomezená.

Je vhodné zkontrolovat, jak se vaše data distribuuje do oddílů. Pokud chcete zkontrolovat distribuci dat na portálu, přejděte k účtu Azure Cosmos DB a klikněte na **metriky** v **monitorování** tématu a potom klikněte na **úložiště** zjistit, jak vaše data rozdělena mezi různé fyzické oddíly.

![Dělení prostředků](./media/partition-data/partitionkey-example.png)

Na levém obrázku výše vidíte výsledek klíč oddílu chybnou a na pravém obrázku výše vidíte výsledek, pokud jste vybrali vhodným klíčem oddílu. V levém image se zobrazí, data, ke které nedistribuuje rovnoměrně mezi oddílů. Měli snažit vyberte klíč oddílu, která distribuuje data tak, že vypadá podobně jako správné bitové kopie.

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>Požadavky pro vytváření oddílů

Pro fyzický oddíly, které mají automaticky rozdělení do **p1** a **p2** jak je popsáno v [jak rozdělení funguje](#how-does-partitioning-work), kontejneru musí být vytvořeny s propustností RU/s 1 000 nebo více (nebo sdílené složky propustnost mezi sadu kontejnerů), a je třeba zadat klíč oddílu. Při vytváření kontejneru (např. kolekce, graf nebo tabulku) na portálu Azure, vyberte **neomezený** možnost kapacity úložiště využívat výhod neomezené škálování. 

Pokud jste vytvořili kontejner na portálu Azure nebo z programu a počáteční propustnost bylo 1 000 RU/s nebo více a jste zadali klíč oddílu, můžete využít výhod neomezené škálování bez změny do vašeho kontejneru. To zahrnuje **pevné** kontejnery, takže pokud počáteční kontejner byl vytvořen s alespoň 1 000 RU/s propustností a je určen klíč oddílu.

Všechny kontejnery, které jsou nakonfigurované tak, aby sdílet propustnost v rámci sady kontejnery jsou považovány za **neomezený** kontejnery.

Pokud jste vytvořili **pevné** kontejneru žádný klíč oddílu nebo propustnost menší než 1 000 RU/s, bude kontejneru není automatickému škálování jak je popsáno v tomto článku. Chcete-li migrovat data z kontejner pevnou s kontejnerem neomezená (například jeden s alespoň 1 000 RU/s a klíč oddílu), je potřeba použít [nástroj pro migraci dat](import-data.md) nebo [změnu kanálu knihovny](change-feed.md). 

## <a name="partitioning-and-provisioned-throughput"></a>Vytváření oddílů a zřízené propustnosti
Azure Cosmos DB je určená pro předvídatelný výkon. Když vytvoříte kontejner nebo sadu kontejnery, můžete vyhradit propustnost z hlediska  *[jednotky žádosti](request-units.md) (RU) za sekundu*. Každý požadavek díky RU nákladů, která je přímo úměrná množství systémové prostředky jako procesoru, paměti a spotřebovávají operaci vstupně-výstupní operace. Čtení 1 KB dokumentu s konzistence typu relace využívá 1 RU. Pro čtení je 1 RU bez ohledu na počet položek, které jsou uložené nebo počet souběžných požadavků, které se spouští ve stejnou dobu. Položky větší vyžadují vyšší RUs v závislosti na velikosti. Pokud znáte velikost vaší entity a počet čtení, které budete potřebovat k podpoře pro aplikaci, můžete zřídit přesné množství propustnosti vyžaduje pro potřeby vaší aplikace. 

> [!NOTE]
> Plně využít pro kontejner nebo sadu kontejnery zřízené propustnosti, musíte zvolit klíč oddílu, který umožňuje rovnoměrně rozdělit požadavky napříč všechny klíčové hodnoty odlišné oddílu.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Práce s Azure Cosmos DB rozhraní API
Portál Azure nebo rozhraní příkazového řádku Azure slouží k vytvoření kontejnerů a škálování je kdykoli. Tato část ukazuje způsob vytvoření kontejnerů a zadejte zřízené propustnosti a oddíl klíče pomocí každé rozhraní API.


### <a name="sql-api"></a>SQL API
Následující příklad ukazuje, jak vytvořit kontejner (kolekce) pomocí rozhraní API SQL. 

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

Můžete si přečíst k položky (dokument) pomocí `GET` metoda v rozhraní API REST nebo pomocí `ReadDocumentAsync` v jednom ze sady SDK.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

Další informace najdete v tématu [vytváření oddílů v Azure DB Cosmos pomocí rozhraní SQL API](sql-api-partition-data.md).

### <a name="mongodb-api"></a>Rozhraní MongoDB API
S rozhraním API pro MongoDB můžete vytvořit kolekci horizontálně dělené prostřednictvím vaše oblíbené nástroje, ovladače nebo sady SDK. V tomto příkladu používáme prostředí Mongo k vytvoření kolekce.

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

Chcete-li vytvořit tabulku pomocí rozhraní API pro tabulky, použijte `CreateIfNotExists` metoda. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

Zřízené propustnosti je nastaven jako argument `CreateIfNotExists`. Klíč oddílu je vytvořena implicitně jako `PartitionKey` hodnotu. 

Jedna entita můžete načíst pomocí následujícího kódu:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Další informace najdete v tématu [vývoj s rozhraním API pro tabulku](tutorial-develop-table-dotnet.md).

### <a name="gremlin-api"></a>Rozhraní Gremlin API

S rozhraním API Gremlin můžete portál Azure nebo Azure CLI vytvořit kontejner, který představuje graf. Alternativně vzhledem k tomu, že Azure Cosmos DB více modelu, můžete mezi jiná rozhraní API vytvořit a škálovat vaše kontejneru grafu.

Pomocí klíč oddílu a ID v Gremlin může číst všechny vrchol nebo Microsoft edge. Například pro graf s oblasti ("USA") jako klíč oddílu a "Seattle" jako klíč řádku, můžete vyhledat vrchol pomocí následující syntaxe:

```
g.V(['USA', 'Seattle'])
```

Okraj můžete odkazovat pomocí klíč oddílu a klíč řádku.

```
g.E(['USA', 'I5'])
```

Další informace najdete v tématu [pomocí oddílů grafu v Azure Cosmos DB](graph-partitioning.md).


<a name="designing-for-scale"></a>
## <a name="design-for-scale"></a>Návrh pro škálování
Efektivní škálování s Azure Cosmos DB, musíte vybrat vhodným klíčem oddílu, při vytváření vašeho kontejneru. Existují dvě hlavní faktory pro výběr vhodným klíčem oddílu:

* **Dotaz hranic a transakce**. Vaši volbu klíč oddílu by měl vyrovnávat nutnost používat transakce proti požadavku, distribuovat vaší entity napříč více klíčů oddílů zajistit škálovatelné řešení. V jedné extreme stejným klíčem oddílu můžete nastavit pro všechny položky, ale tato možnost může omezit škálovatelnost řešení. V jiných extreme můžete přiřadit oddílu jedinečný klíč pro každou položku. Tato volba je vysoce škálovatelné, ale jeho zabrání pomocí transakce mezi dokumenty prostřednictvím uložených procedur a aktivačních událostí. Klíčem ideální oddílu vám umožní využít efektivní dotazy a má dostatek mohutnost zajistit, že vaše řešení je škálovatelná. 
* **Žádné úložiště a výkon kritických bodů**. Je důležité vybrat vlastnost, která umožňuje zápis být distribuován do různých jedinečných hodnot. Požadavky na stejným klíčem oddílu nesmí být delší než zřízené propustnosti přidělené k oddílu a bude míra limited. Proto je důležité vybrat klíč oddílu, který nevede k "aktivní body" v rámci vaší aplikace. Vzhledem k tomu, že všechna data pro jeden oddíl klíč musí být uložen v rámci oddílu, neměli byste klíče oddílů, které mají velký objem dat pro stejnou hodnotu. 

Podívejme se na několik reálných scénářů a klíče dobrý oddílů pro každou:
* Pokud jste implementace back-end profil uživatel *ID uživatele* je vhodná pro klíč oddílu.
* Pokud ukládáte data IoT, například stav zařízení *ID zařízení* je vhodná pro klíč oddílu.
* Pokud používáte Azure Cosmos DB pro protokolování dat časové řady *hostname* nebo *ID procesu* je vhodná pro klíč oddílu.
* Pokud máte víceklientské architektury, *ID klienta* je vhodná pro klíč oddílu.

V některých případech, jako je IoT a uživatelské profily použití, klíč oddílu může být stejný jako vaše *ID* (klíč dokumentu). V jiné, jako je časové řady data, můžete mít klíč oddílu, který se liší od *ID*.

### <a name="partitioning-and-loggingtime-series-data"></a>Vytváření oddílů a protokolování nebo časových řad dat
Mezi běžné případy použití v Azure Cosmos DB je protokolování a telemetrie. Je důležité vybrat vhodným klíčem oddílu v tomto scénáři, protože možná budete muset obrovské objemy dat pro čtení a zápis. Volba pro klíč oddílu závisí na sazby pro čtení a zápis a typy dotazů, které chcete spustit. Zde jsou některé tipy, jak zvolit vhodným klíčem oddílu:

* Pokud váš případ použití zahrnuje malý počet zápisů, které se nashromáždí přes dlouhou dobu a které potřebujete k dotazování podle rozsahů časová razítka u ostatních filtrů, použijte kumulativní časové razítko. Například je dobrý způsob použití datum jako klíč oddílu. Při tomto postupu se můžete dotazovat přes všechna data pro konkrétního data z jednoho oddílu. 
* Pokud vaše úlohy je zápisu náročné, což je velmi obvyklé v tomto scénáři, použijte klíč oddílu, který není založen na časové razítko. Takto můžete Azure Cosmos DB distribuovat a škálování zápisy rovnoměrně mezi různé oddíly. Sem *hostname*, *ID procesu*, *ID aktivity*, nebo je vhodné použít jinou vlastnost s vysokou kardinalitou. 
* Další možností je hybridní přístup, kde máte více kontejnerů, jednu pro každý den/měsíc, a klíč oddílu je podrobnější vlastnost jako *hostname*. Tento přístup má výhodu, kterou můžete nastavit různé propustnost pro každý kontejner nebo sadu kontejnery na základě potřeb škálování a výkon a časový interval. Kontejner pro aktuální měsíc může například zřizovat s vyšší propustnost, protože slouží čte a zapisuje. Předchozích měsíců může být opatřena nižší propustnost, protože slouží pouze čtení.

### <a name="partitioning-and-multitenancy"></a>Vytváření oddílů a víceklientskou architekturu
Pokud implementujete víceklientské aplikace pomocí Azure Cosmos DB, existují dvě oblíbených návrhů vzít v úvahu: *klíč jeden oddíl každého klienta* a *jednoho kontejneru typu každého klienta*. Zde jsou výhody a nevýhody pro každou:

* **Klíč oddílu jeden na každého klienta**. V tomto modelu jsou umístěna společně v rámci jednoho kontejneru klientů. Dotazy a vložení pro jednoho klienta můžete provedeny s jeden oddíl. Můžete také implementovat logiku transakcí mezi všechny položky, které patří do klienta. Vzhledem k tomu, že více klientů sdílet kontejner, můžete lépe využít úložiště a zřízené propustnosti sdružování prostředků pro všechny klienty v rámci jednoho kontejneru spíše než zřizování pro každého klienta. Nevýhodou je, že nemáte izolaci výkonu každého klienta. Zvýšení propustnosti, která zaručí výkon bude platit pro celý kontejneru s všichni klienti versus cílové zvyšuje pro jednoho klienta.
* **Jeden kontejner každého klienta**. V tomto modelu má každý klient vlastní kontejner a je možné rezervovat propustnost s zaručenou výkonu každého klienta. Tento model je cenově pro víceklientské aplikace s několika klienty.

Můžete také použít hybridní přístup, který společně colocates malé klientů a izoluje větší klientům vlastní kontejnerů.

## <a name="next-steps"></a>Další postup
V tomto článku jsme poskytuje přehled o konceptech a osvědčené postupy pro škálování a vytváření oddílů v Azure Cosmos DB. 

* Další informace o [zřízené propustnosti v Azure Cosmos DB](request-units.md).
* Další informace o [globální distribuce v Azure Cosmos DB](distribute-data-globally.md).



