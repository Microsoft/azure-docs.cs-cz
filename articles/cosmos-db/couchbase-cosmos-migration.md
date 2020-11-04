---
title: Migrace z CouchBase do Azure Cosmos DB SQL API
description: Podrobné pokyny pro migraci z CouchBase do Azure Cosmos DB SQL API
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.custom: devx-track-java
ms.openlocfilehash: 73d6fe0233eccea9ebf1d82beb509c56fb45f4da
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339506"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Migrace z CouchBase do Azure Cosmos DB SQL API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB je škálovatelná, globálně distribuovaná a plně spravovaná databáze. Poskytuje zaručený přístup k datům s nízkou latencí. Další informace o Azure Cosmos DB najdete v článku [Přehled](introduction.md) . Tento článek poskytuje pokyny k migraci aplikací Java, které jsou připojené k Couchbase, na účet rozhraní SQL API v Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Rozdíly v nomenklatuře

Níže jsou uvedené klíčové funkce, které v Azure Cosmos DB ve srovnání s Couchbase fungují jinak:

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Server Couchbase| Účet       |
|Blocích           | databáze      |
|Blocích           | Kontejner/kolekce |
|Dokument JSON    | Položka/dokument |

## <a name="key-differences"></a>Klíčové rozdíly

* Azure Cosmos DB má v dokumentu pole "ID", zatímco Couchbase má ID jako součást intervalu. Pole ID je v rámci oddílu jedinečné.

* Azure Cosmos DB škáluje pomocí techniky dělení nebo horizontálního dělení. To znamená, že data rozdělí do několika horizontálních oddílů/oddílů. Tyto oddíly/horizontálních oddílů se vytvářejí na základě vlastnosti klíče oddílu, kterou zadáte. Můžete vybrat klíč oddílu pro optimalizaci čtení, i když jsou taky optimalizované operace zápisu nebo čtení/zápis. Další informace najdete v článku [dělení](./partitioning-overview.md) .

* V Azure Cosmos DB není nutné, aby hierarchie nejvyšší úrovně naznamenala kolekci, protože název kolekce již existuje. Tato funkce zpřístupňuje strukturu JSON mnohem jednodušší. Následuje příklad, který ukazuje rozdíly v datovém modelu mezi Couchbase a Azure Cosmos DB:

   **Couchbase** : Document ID = "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB** : v dokumentu uveďte "ID", jak je znázorněno níže.

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Podpora sady Java SDK

Azure Cosmos DB má následující sady SDK pro podporu různých platforem Java:

* Asynchronní sada SDK
* Sada jarních spouštění sady SDK

Následující části popisují, kdy použít jednotlivé sady SDK. Vezměte v úvahu příklad, kde máme tři typy úloh:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase jako úložiště dokumentů & vlastní dotazy na základě dat založených na jaře

Pokud je zatížení, které migrujete, založeno na sadě SDK založených na jarním spuštění, můžete použít následující postup:

1. Přidat nadřazenou položku do souboru POM.xml:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Přidat vlastnosti do souboru POM.xml:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Přidat závislosti do souboru POM.xml:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Do části prostředky přidejte vlastnosti aplikace a určete následující nastavení. Nezapomeňte nahradit parametry adresa URL, klíč a název databáze:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Definujte název kolekce v modelu. Můžete také zadat další poznámky. Například ID, klíč oddílu k jejich označení explicitně:

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

Níže jsou uvedené fragmenty kódu pro operace CRUD:

### <a name="insert-and-update-operations"></a>Operace INSERT a Update

Kde *_repo* je objektem úložiště a *doc* , je objekt třídy Pojo. Můžete použít `.save` k vložení nebo Upsert (Pokud se našel dokument se ZADANÝM ID). Následující fragment kódu ukazuje, jak vložit nebo aktualizovat objekt doc:

```_repo.save(doc);```

### <a name="delete-operation"></a>Odstranit operaci

Vezměte v úvahu následující fragment kódu, kde objekt doc bude mít ID a klíč oddílu povinné pro vyhledání a odstranění objektu:

```_repo.delete(doc);```

### <a name="read-operation"></a>Operace čtení

Dokument si můžete přečíst s nebo bez zadání klíče oddílu. Pokud klíč oddílu nezadáte, považuje se za dotaz na více oddílů. Vezměte v úvahu následující ukázky kódu. první z nich provede operaci pomocí pole ID a klíč oddílu. Druhý příklad používá regulární pole & bez zadání pole klíč oddílu.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

To je to, teď můžete aplikaci používat s Azure Cosmos DB. Kompletní ukázka kódu pro příklad popsaný v tomto dokumentu je k dispozici v úložišti GitHub [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) .

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase jako úložiště dokumentů & pomocí dotazů N1QL

Dotazy N1QL slouží jako způsob, jak definovat dotazy v Couchbase.

|Dotaz N1QL | Dotaz na Azure CosmosDB|
|-------------------|-------------------|
|Vyberte META ( `TravelDocument` ). ID jako ID, `TravelDocument` . * z `TravelDocument` Where `_type` = "com. xx. xx. xx. xxx. xxx. xxxx" a Country = ' Indie ' a jakékoli m v vízech splňuje požadavky m. Type = = ' Multi-Entry ' a m. Country v [' Indie ', Bhútán '] ORDER by ` Validity` limit 25 offset 0   | Vyberte c. ID, c z c JOINa m v c. Country = ' Indie ', kde c._type = "com. xx. xx. xx. xxx. xxx. xxxx" a c. Country = ' Indie ' a m. Type = ' Multi-Entry ' a m. Country IN (' Indie ', ' Bhútán ') ORDER BY c. |

Ve svých dotazech N1QL si můžete všimnout následujících změn:

* Nemusíte používat klíčové slovo META ani odkazovat na dokument první úrovně. Místo toho můžete vytvořit vlastní odkaz na kontejner. V tomto příkladu jsme považovat za "c" (může to být cokoli). Tento odkaz slouží jako předpona pro všechna pole první úrovně. Fr příklad, c.id, c. Country atd.

* Místo "ANY" teď můžete provést spojení s poddokumentem a odkazovat ho na vyhrazený alias, například "m". Jakmile vytvoříte alias pro vnořený dokument, je nutné použít alias. Například m. Country.

* Pořadí POSUNu se v Azure Cosmos DB dotazu liší, nejdříve je třeba zadat posun a omezení. Pokud používáte maximální vlastní definované dotazy, doporučuje se použít sadu jarních dat SDK, protože na straně klienta může být zbytečné režie při předávání dotazu do Azure Cosmos DB. Místo toho máme přímo asynchronní sadu Java SDK, která se v tomto případě dá využít mnohem efektivně.

### <a name="read-operation"></a>Operace čtení

Použijte asynchronní sadu Java SDK s následujícími kroky:

1. Nakonfigurujte následující závislost na soubor POM.xml:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Vytvořte objekt připojení pro Azure Cosmos DB pomocí `ConnectionBuilder` metody, jak je znázorněno v následujícím příkladu. Ujistěte se, že jste tuto deklaraci vložili do fazole tak, aby následující kód byl proveden pouze jednou:

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();   
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. Chcete-li spustit dotaz, je nutné spustit následující fragment kódu:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Nyní můžete pomocí výše uvedené metody předat více dotazů a provést bez jakýchkoli problémů. V případě, že máte požadavek na provedení jednoho rozsáhlého dotazu, který může být rozdělen do více dotazů, pak vyzkoušejte následující fragment kódu místo předchozí:

```java
for(SqlQuerySpec query:queries)
{
    objFlux= container.queryItems(query, fo);
    objFlux .publishOn(Schedulers.elastic())
            .subscribe(feedResponse->
                {
                    if(feedResponse.results().size()>0)
                    {
                        _docs.addAll(feedResponse.results());
                    }
                
                },
                Throwable::printStackTrace,latch::countDown);
    lstFlux.add(objFlux);
}
                        
        Flux.merge(lstFlux);
        latch.await();
}
```

Pomocí předchozího kódu můžete spouštět dotazy paralelně a zvýšit tak distribuovaná spuštění pro optimalizaci. Dále můžete spustit i operace vložení a aktualizace:

### <a name="insert-operation"></a>Vložit operaci

Chcete-li vložit dokument, spusťte následující kód:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Pak se přihlaste k odběru mono jako:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();              
```

### <a name="upsert-operation"></a>Operace Upsert

Operace Upsert vyžaduje, abyste zadali dokument, který je potřeba aktualizovat. Chcete-li načíst úplný dokument, můžete použít fragment uvedený v části operace čtení nadpisu a pak upravit požadovaná pole (y). Následující fragment kódu upsertuje dokument:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Pak se přihlaste k odběru mono. Přečtěte si fragment předplatného mono v operaci vložení.

### <a name="delete-operation"></a>Odstranit operaci

Následující fragment kódu provede operaci odstranění:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Pak se přihlaste k odběru mono, v operaci vložení použijte fragment předplatného mono. Kompletní ukázka kódu je k dispozici v úložišti GitHub [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) .

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase jako pár klíč/hodnota

Toto je jednoduchý typ úlohy, ve které můžete vyhledávat místo dotazů. Pro páry klíč/hodnota použijte následující postup:

1. Zvažte možnost "/ID" jako primární klíč, což zajistí, že můžete provádět operace vyhledávání přímo v konkrétním oddílu. Vytvořte kolekci a jako klíč oddílu zadejte "/ID".

1. Úplné vypínání indexu. Vzhledem k tomu, že budete provádět operace vyhledávání, neexistuje žádný bod pro zaznamenání zátěže. Pokud chcete indexování vypnout, přihlaste se Azure Portal, přejít Azure Cosmos DB účet. Otevřete **Průzkumník dat** vyberte svou **databázi** a **kontejner**. Otevřete kartu **nastavení & škálování** a vyberte  **zásadu indexování**. V současné době indexování zásad vypadá takto:
    
   ```json
   {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ]
    }
   ````

   Nahraďte výše uvedené zásady indexování následujícími zásadami:

   ```json
   {
    "indexingMode": "none",
    "automatic": false,
    "includedPaths": [],
    "excludedPaths": []
    }
   ```

1. Pomocí následujícího fragmentu kódu vytvořte objekt připojení. Objekt připojení (který se má umístit do @Bean statického umístění nebo ho označit jako statický):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

Nyní můžete provádět operace CRUD následujícím způsobem:

### <a name="read-operation"></a>Operace čtení

Chcete-li číst položku, použijte následující fragment kódu:

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
        
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.item()!=null)
            {
                doc= resourceResponse.properties().toObject(UserModel.class);
            }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>Vložit operaci

Chcete-li vložit položku, můžete provést následující kód:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Pak se přihlaste k odběru mono jako:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Operace Upsert

Chcete-li aktualizovat hodnotu položky, přečtěte si následující fragment kódu:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Pak se přihlaste k odběru mono, v operaci vložení použijte fragment předplatného mono.

### <a name="delete-operation"></a>Odstranit operaci

Pomocí následujícího fragmentu kódu spusťte operaci odstranění:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Pak se přihlaste k odběru mono, v operaci vložení použijte fragment předplatného mono. Kompletní ukázka kódu je k dispozici v úložišti GitHub [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) .

## <a name="data-migration"></a>Migrace dat

Existují dva způsoby, jak migrovat data.

* **Použít Azure Data Factory:** Toto je nejpoužívanější způsob migrace dat. Konfigurace zdroje jako Couchbase a jímky jako Azure Cosmos DB SQL API, podrobné pokyny najdete v článku o [konektoru služby Azure Cosmos DB Data Factory](../data-factory/connector-azure-cosmos-db.md) .

* **Použijte nástroj pro import dat Azure Cosmos DB:** Tato možnost se doporučuje migrovat pomocí virtuálních počítačů s méně objemy dat. Podrobný postup najdete v článku pro [Import dat](./import-data.md) .

## <a name="next-steps"></a>Další kroky

* Chcete-li provést testování výkonu, přečtěte si článek [testování výkonu a škálování pomocí Azure Cosmos DB](./performance-testing.md) článku.
* Chcete-li optimalizovat kód, přečtěte si téma [tipy ke zvýšení výkonu pro Azure Cosmos DB](./performance-tips-async-java.md) článek.
* Prozkoumejte sadu Java Async V3 SDK, Referenční dokumentace k [sadě SDK](https://github.com/Azure/azure-cosmosdb-java/tree/v3) .
