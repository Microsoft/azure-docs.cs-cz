---
title: Migrace z CouchBase do rozhraní SQL API Azure Cosmos DB
description: Podrobné pokyny pro migraci z CouchBase do rozhraní SQL API Azure Cosmos DB
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210941"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Migrace z CouchBase do rozhraní SQL API Azure Cosmos DB

Azure Cosmos DB je škálovatelná, globálně distribuovaná, plně spravovaná databáze. Poskytuje zaručený přístup k vašim datům s nízkou latencí. Další informace o Azure Cosmos DB najdete v článku [přehled.](introduction.md) Tento článek obsahuje pokyny k migraci aplikací jazyka Java, které jsou připojené k Couchbase k účtu SQL API v Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Rozdíly v nomenklatuře

Níže jsou uvedeny klíčové funkce, které fungují odlišně v Azure Cosmos DB ve srovnání s Couchbase:

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Couchbase server| Účet       |
|Kbelík           | Databáze      |
|Kbelík           | Kontejner/kolekce |
|Dokument JSON    | Položka / dokument |

## <a name="key-differences"></a>Klíčové rozdíly

* Azure Cosmos DB má pole "ID" v rámci dokumentu vzhledem k tomu, Couchbase má ID jako součást bloku. Pole "ID" je jedinečné v celém oddílu.

* Azure Cosmos DB škáluje pomocí dělení nebo horizontálního dělení techniky. Což znamená, že rozdělí data do více střepů/oddílů. Tyto oddíly a oddíly jsou vytvořeny na základě vlastnosti klíče oddílu, které poskytujete. Můžete vybrat klíč oddílu pro optimalizaci čtení i psát operace nebo čtení / zápis optimalizované příliš. Další informace naleznete v článku [dělení.](./partition-data.md)

* V Azure Cosmos DB není nutné pro hierarchii nejvyšší úrovně k označení kolekce, protože název kolekce již existuje. Tato funkce umožňuje strukturu JSON mnohem jednodušší. Následuje příklad, který ukazuje rozdíly v datovém modelu mezi Couchbase a Azure Cosmos DB:

   **Couchbase**: ID dokumentu = "99FF4444"

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

   **Azure Cosmos DB**: Odkazovat "ID" v dokumentu, jak je znázorněno níže

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
         
## <a name="java-sdk-support"></a>Podpora pro Java SDK

Azure Cosmos DB má následující sady SDK pro podporu různých architektur Java:

* Asynchronní sada SDK
* Sada SDK pro jarní spouštění

Následující části popisují, kdy použít každou z těchto sad SDK. Vezměme si příklad, kde máme tři typy úloh:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase jako úložiště dokumentů & vlastní dotazy založené na datech pružiny

Pokud je úloha, kterou migrujete, založena na sdk na jarním spuštění, můžete použít následující kroky:

1. Přidání nadřazeného objektu do souboru POM.xml:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Přidejte vlastnosti do souboru POM.xml:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Přidejte závislosti do souboru POM.xml:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Přidejte vlastnosti aplikace pod prostředky a zadejte následující. Nezapomeňte nahradit parametry adresy URL, klíče a názvu databáze:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Definujte název kolekce v modelu. Můžete také zadat další poznámky. Například ID, klíč oddílu k jejich explicitnímu označení:

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

Pro operace CRUD jsou následující fragmenty kódu:

### <a name="insert-and-update-operations"></a>Vložení a aktualizace operací

Kde *_repo* je objektem repozitáře a *doc* je objekt třídy POJO. Můžete použít `.save` k vložení nebo upsert (pokud dokument se zadaným ID nalezeno). Následující fragment kódu ukazuje, jak vložit nebo aktualizovat objekt dokumentu:

```_repo.save(doc);```

### <a name="delete-operation"></a>Odstranit operaci

Zvažte následující fragment kódu, kde objekt doc bude mít ID a klíč oddílu povinné najít a odstranit objekt:

```_repo.delete(doc);```

### <a name="read-operation"></a>Operace čtení

Dokument můžete číst s nebo bez zadání klíče oddílu. Pokud nezadáte klíč oddílu, pak je považován za dotaz mezi oddíly. Zvažte následující ukázky kódu, první provede operaci pomocí ID a pole klíče oddílu. Druhý příklad používá pravidelné pole & bez zadání pole klíče oddílu.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

To je ono, teď můžete použít svou aplikaci s Azure Cosmos DB. Kompletní ukázka kódu pro příklad popsaný v tomto dokumentu je k dispozici v [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub úložiště.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase jako úložiště dokumentů & pomocí dotazů N1QL

N1QL dotazy je způsob, jak definovat dotazy v Couchbase.

|Dotaz N1QL | Dotaz Na Azure CosmosDB|
|-------------------|-------------------|
|VYBERTE`TravelDocument`META( .id `TravelDocument`AS id, .* ODKUD `TravelDocument` `_type` = "com.xx.xx.xx.xxx.xxx.xxx.xxxx " a země = 'Indie' a any m v Vízech USS m.type == 'Multi-Entry' a m.Country IN ['Indie', Bhútán'] OBJEDNÁVKA ` Validity` DESC LIMIT 25 OFFSET 0   | SELECT c.id,c FROM c JOIN m in c.country='India' KDE c._type = " com.xx.xx.xx.xxx.xxx.xxxx" a c.země = 'Indie' a m.type = 'Multi-Entry' a m.Country IN ('Indie', 'Bhútán') OBJEDNÁVKA C.Validity DESC OFFSET 0 LIMIT 25 |

V dotazech N1QL si můžete všimnout následujících změn:

* Není nutné používat klíčové slovo META nebo odkazovat na dokument první úrovně. Místo toho můžete vytvořit vlastní odkaz na kontejner. V tomto příkladu jsme to považovali za "c" (může to být cokoliv). Tento odkaz se používá jako předpona pro všechna pole první úrovně. Fr například c.id, c.country atd.

* Namísto "ANY" nyní můžete udělat spojit na vnořený dokument a odkazovat se na něj s vyhrazeným alias, jako je "m". Jakmile vytvoříte alias pro vnořený dokument, musíte použít alias. Například m.Country.

* Posloupnost POSUN se liší v dotazu Azure Cosmos DB, nejprve je třeba zadat POSUN pak LIMIT. Doporučujese nepoužívat spring data sdk, pokud používáte maximální vlastní definované dotazy, protože může mít zbytečné režie na straně klienta při předávání dotazu do Azure Cosmos DB. Místo toho máme přímou asynchronní Java SDK, která může být v tomto případě využita velmi efektivně.

### <a name="read-operation"></a>Operace čtení

Asynchronní java sdsadí použijte následující kroky:

1. Nakonfigurujte následující závislost do souboru POM.xml:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Vytvořte objekt připojení pro Azure Cosmos DB pomocí metody, `ConnectionBuilder` jak je znázorněno v následujícím příkladu. Ujistěte se, že jste vložili tuto deklaraci do včely tak, že následující kód by měl být proveden pouze jednou:

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

1. Chcete-li spustit dotaz, je třeba spustit následující fragment kódu:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Nyní, s pomocí výše uvedené metody můžete předat více dotazů a spustit bez jakýchkoli potíží. V případě, že máte požadavek na spuštění jednoho velkého dotazu, který lze rozdělit na více dotazů, zkuste místo předchozího fragmentu kódu následující fragment kódu:

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

S předchozím kódem můžete spouštět dotazy paralelně a zvýšit distribuované spuštění optimalizovat. Dále můžete spustit operace vložení a aktualizace:

### <a name="insert-operation"></a>Operace vložení

Chcete-li dokument vložit, spusťte následující kód:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Pak se přihlaste k odběru Mono jako:

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

### <a name="upsert-operation"></a>Upsert provoz

Operace Upsert vyžaduje zadání dokumentu, který je třeba aktualizovat. Chcete-li načíst celý dokument, můžete použít úryvek uvedený v operaci čtení nadpisu a upravit požadovaná pole. Následující fragment kódu upserts dokumentu:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Pak se přihlaste k odběru mono. Viz mono odběr fragment v operaci vložení.

### <a name="delete-operation"></a>Operace odstranění

Následující fragment výstřižku provede odstranění operace:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Pak se přihlaste k odběru mono, odkazovat mono odběr fragment v operaci vložení. Kompletní ukázka kódu je k dispozici v [couchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub úložiště.

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase jako pár klíč/hodnota

Jedná se o jednoduchý typ úlohy, ve kterém můžete provádět vyhledávání namísto dotazů. Pro páry klíčů a hodnot použijte následující kroky:

1. Zvažte možnost mít "/ID" jako primární klíč, což zajistí, že můžete provádět vyhledávací operaci přímo v konkrétním oddílu. Vytvořte kolekci a zadejte "/ID" jako klíč oddílu.

1. Indexování zcela vypněte. Vzhledem k tomu, že provedete vyhledávací operace, neexistuje žádný bod provádění režie indexování. Pokud chcete indexování vypnout, přihlaste se k webu Azure Portal, přejděte na účet Azure Cosmos DB. Otevřete **Průzkumníka dat**, vyberte **databázi** a **kontejner**. Otevřete kartu **Změnit & nastavení** a vyberte **zásady indexování**. V současné době indexování zásady vypadá takto:
    
   ```json
   {
       "indexingMode": "consistent",
       "includedPaths": 
       [
           {
            "path": "/*",
            "indexes": 
             [
                {
                  "kind": "Range",
                  "dataType": "Number"
                },
                {
                  "kind": "Range",
                  "dataType": "String"
                },
                {
                   "kind": "Spatial",
                   "dataType": "Point"
                }
             ]
          }
       ],
       "excludedPaths": 
       [
         {
             "path": "/path/to/single/excluded/property/?"
         },
         {
             "path": "/path/to/root/of/multiple/excluded/properties/*"
         }
      ]
   }
   ````

   Nahraďte výše uvedené zásady indexování následujícími zásadami:

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. K vytvoření objektu připojení použijte následující fragment kódu. Objekt připojení (chcete-li být umístěny nebo @Bean aby byly statické):

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

Chcete-li položku přečíst, použijte následující úryvek:

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

### <a name="insert-operation"></a>Operace vložení

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

### <a name="upsert-operation"></a>Upsert provoz

Chcete-li aktualizovat hodnotu položky, podívejte se na fragment kódu níže:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Pak se přihlaste k odběru mono, odkazovat mono odběr fragment v operaci vložení.

### <a name="delete-operation"></a>Operace odstranění

K provedení operace odstranění použijte následující úryvek:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Pak se přihlaste k odběru mono, odkazovat mono odběr fragment v operaci vložení. Kompletní ukázka kódu je k dispozici v [couchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) úložiště GitHub.

## <a name="data-migration"></a>Migrace dat

Existují dva způsoby migrace dat.

* **Použití Azure Data Factory:** Toto je nejdoporučovanější metoda migrace dat. Nakonfigurujte zdroj jako Couchbase a jímky jako Azure Cosmos DB SQL API, najdete v článku konektor Azure [Cosmos DB Data Factory pro](../data-factory/connector-azure-cosmos-db.md) podrobné kroky.

* **Použijte nástroj pro import dat Azure Cosmos DB:** Tato možnost se doporučuje migrovat pomocí virtuálních stránek s menším množstvím dat. Podrobné kroky najdete v článku [Import dat.](./import-data.md)

## <a name="next-steps"></a>Další kroky

* Pokud jde o testování výkonu, najdete v článku [Výkon a škálování testování s Azure Cosmos DB](./performance-testing.md) článku.
* Pokud chcete kód optimalizovat, přečtěte si článek [Tipy pro výkon pro Azure Cosmos DB.](./performance-tips-async-java.md)
* Prozkoumejte java asynchronní v3 SDK, [úložiště GitHub reference SDK.](https://github.com/Azure/azure-cosmosdb-java/tree/v3)
