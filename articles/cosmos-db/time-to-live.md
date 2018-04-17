---
title: Vypršení platnosti dat v Azure Cosmos DB s Hodnota time to live | Microsoft Docs
description: Microsoft Azure Cosmos DB s TTL, poskytuje schopnost dokumenty z systém automaticky vymazány po určitou dobu.
services: cosmos-db
documentationcenter: ''
keywords: Hodnota Time to live
author: SnehaGunda
manager: kfile
ms.assetid: 25fcbbda-71f7-414a-bf57-d8671358ca3f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2017
ms.author: sngun
ms.openlocfilehash: 61db8f85e73d2c071bdec0ace60911813fa4f0e8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="expire-data-in-azure-cosmos-db-collections-automatically-with-time-to-live"></a>Vypršení platnosti dat v kolekcích Azure Cosmos DB automaticky s Hodnota time to live
Aplikace můžete vytvářet a ukládat velká množství dat. Některé z těchto dat, jako například počítač generuje data, protokoly a uživatelské relace události, které informace jsou užitečné pouze omezenou dobu. Jakmile se změní na data přebytečných potřebám aplikace je bezpečné mazání tato data a snížit požadavkům na ukládání aplikace.

Microsoft Azure Cosmos DB s "time to live" nebo TTL, poskytuje schopnost dokumenty automaticky odstraněna z databáze po určitou dobu. Výchozí doba TTL můžete nastavit na úrovni kolekce a přepsán pro každý dokument. Jakmile je nastavena hodnota TTL, jako výchozí kolekci nebo na úrovni dokumentu Cosmos DB automaticky odebere dokumenty, které existují po uplynutí této doby čas v sekundách, protože poslední změny.

Hodnota Time to live v Cosmos DB používá posun vůči při poslední změny dokumentu. K tomu použije `_ts` pole, která již existuje u každé dokumentu. Pole _ts je časové razítko epoch stylu systému unix představující datum a čas. `_ts` Pole je aktualizováno pokaždé, když je změněn dokument. 

## <a name="ttl-behavior"></a>Hodnota TTL chování
Hodnota TTL funkce je řízena vlastnostmi TTL na dvou úrovních - úrovni kolekce a na úrovni dokumentu. Hodnoty jsou nastavené v sekundách a jsou považovány za rozdílů z `_ts` posledního dokumentu modifikace.

1. DefaultTTL pro kolekci
   
   * Pokud chybí (nebo nastaveno na hodnotu null), dokumenty nejsou automaticky odstraněny.
   * Pokud je přítomen a hodnota je "-1" = nekonečné – dokumenty nevyprší ve výchozím nastavení
   * Pokud je přítomen a hodnota je některé číslo ("n") – dokumenty vyprší "n" sekund po poslední změny
2. Hodnota TTL pro dokumenty: 
   
   * Vlastnost se vztahuje pouze v případě DefaultTTL je k dispozici pro její nadřazená kolekce.
   * Přepíše hodnotu DefaultTTL pro její nadřazená kolekce.

Jakmile vypršela platnost dokumentu (`ttl`  +  `_ts` < = aktuální čas serveru), dokument je označena jako "prošlé". Žádná operace bude možné na těchto dokumentech po této době a jejich budou vyloučeny z výsledků všechny dotazy provést. Dokumenty jsou fyzicky odstraněn v systému, včetně jsou na pozadí tj později. Není to využívat žádné [jednotky žádosti (ruština)](request-units.md) z rozpočtu kolekce.

Výše uvedené logiku lze zobrazit v matici následující:

|  | DefaultTTL chybí nebo není nastaven na kolekci | DefaultTTL = -1 na kolekci | DefaultTTL = "n" na kolekci |
| --- |:--- |:--- |:--- |
| Hodnota TTL chybí v dokumentu |Nic k přepsání na úrovni dokumentu vzhledem k tomu, že dokument i kolekce nemají žádný koncept TTL. |Vypršení platnosti žádné dokumenty v této kolekci. |Dokumenty v této kolekci vyprší po uplynutí intervalu n. |
| Hodnota TTL = -1 v dokumentu |Nic k přepsání na úrovni dokumentu od kolekce nedefinuje vlastnost DefaultTTL, který můžete přepsat dokumentu. Hodnota TTL na dokument je zrušení interpretovaný v systému. |Vypršení platnosti žádné dokumenty v této kolekci. |Dokument s TTL =-1 v této kolekci nikdy nevyprší. Všechny ostatní dokumenty vyprší po intervalu "n". |
| Hodnota TTL = n dokumentu |Nic k přepsání na úrovni dokumentu. Hodnota TTL na dokument je zrušení interpretovaný v systému. |Dokument s TTL = n vyprší po n interval v sekundách. Další dokumenty budou dědit interval-1 a jeho platnost nikdy nevypršela. |Dokument s TTL = n vyprší po n interval v sekundách. Další dokumenty zdědí "n" interval z kolekce. |

## <a name="configuring-ttl"></a>Konfigurace TTL
Ve výchozím nastavení je hodnota time to live zakázané ve výchozím nastavení ve všech kolekcích Cosmos DB a na všechny dokumenty. Hodnota TTL jde nastavit prostřednictvím kódu programu nebo na portálu Azure v **nastavení** části pro kolekci. 

## <a name="enabling-ttl"></a>Povolení TTL
Chcete-li povolit TTL na kolekci nebo dokumenty v rámci kolekce, nastavte vlastnost DefaultTTL kolekce na -1 nebo nenulovou hodnotou kladné číslo. Nastavení DefaultTTL-1 znamená, který ve výchozím nastavení se všechny dokumenty v kolekci bude navždy za provozu, ale služba Cosmos DB měli sledovat tuto kolekci pro dokumenty, které mají přepsat toto výchozí nastavení.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>Konfigurace výchozí hodnota TTL na kolekci
Budete moci konfigurovat výchozí čas TTL na úrovni kolekce. Pokud chcete nastavit interval TTL, ZÍSKÁ na kolekci, je třeba zadat nenulovou hodnotou kladné číslo určující časový interval v sekundách, po poslední úpravy časové razítko dokumentu vyprší všechny dokumenty v kolekci (`_ts`). Nebo můžete nastavit výchozí hodnoty-1, což znamená, že bude ve výchozím nastavení po neomezenou dobu live všechny dokumenty v vložit do kolekce.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>Nastavení TTL v dokumentu
Kromě nastavení výchozí hodnota TTL na kolekci můžete nastavit konkrétní TTL na úrovni dokumentu. Tím se přepíše výchozí kolekce.

* Pokud chcete nastavit interval TTL, ZÍSKÁ v dokumentu, je třeba zadat nenulové kladné číslo, který určuje časový interval v sekundách, po kterou vyprší dokumentu po poslední úpravy časové razítko dokumentu (`_ts`).
* Pokud dokument neobsahuje žádné pole TTL, bude použita výchozí hodnota kolekce.
* Pokud je hodnota TTL vypnutá na úrovni kolekce, pole TTL v dokumentu budou ignorovány, dokud hodnota TTL je povoleno znovu na kolekci.

Zde je fragment ukazuje, jak nastavit dobu platnosti TTL na dokumentu:

    // Include a property that serializes to "ttl" in JSON
    public class SalesOrder
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        
        [JsonProperty(PropertyName="cid")]
        public string CustomerId { get; set; }
        
        // used to set expiration policy
        [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
        public int? TimeToLive { get; set; }
        
        //...
    }
    
    // Set the value to the expiration in seconds
    SalesOrder salesOrder = new SalesOrder
    {
        Id = "SO05",
        CustomerId = "CO18009186470",
        TimeToLive = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days 
    };


## <a name="extending-ttl-on-an-existing-document"></a>Rozšíření TTL na stávající dokument
Hodnota TTL dokumentu můžete resetovat pomocí tohoto postupu všechny operace zápisu v dokumentu. To bude tato hodnota nastavena `_ts` aktuální čas a začne odpočítávat čas do vypršení platnosti dokumentu, jak pomocí `ttl`, bude spuštěno znovu. Pokud chcete změnit `ttl` dokumentu, můžete aktualizovat pole, jako je tomu s jiné nastavit pole.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="removing-ttl-from-a-document"></a>Odebrání TTL z dokumentu
Pokud hodnotu TTL byla nastavena na dokument a již nechcete vypršení platnosti tohoto dokumentu, pak můžete načtení dokumentu, odeberte pole TTL a nahraďte dokumentů na serveru. Pokud pole TTL je odebrán z dokumentu, použijí se výchozí kolekce. Zastavení dokumentu z vypršení platnosti a není dědit z kolekce bude nutné nastavit hodnotu TTL na hodnotu -1.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(salesOrder);

## <a name="disabling-ttl"></a>Zakázání TTL
Měla by být odstraněna zakázat TTL zcela na kolekci a ukončit proces na pozadí z vyhledávání pro dokumenty s vypršenou platností DefaultTTL vlastnost v kolekci. Odstraněním této vlastnosti se liší od nastavení na hodnotu -1. Nastavení pro nové dokumenty-1 znamená přidat do kolekce bude navždy za provozu, ale je možné přepsat na konkrétní dokumenty v kolekci. Odebrání tato vlastnost zcela z kolekce znamená, že žádné dokumenty vyprší, i když jsou dokumenty, které mají explicitně přepsat předchozí výchozí.

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);

<a id="ttl-and-index-interaction"></a> 
## <a name="ttl-and-index-interaction"></a>Interakce TTL a index.
Přidání nebo změna nastavení TTL na kolekci změní základní index. Pokud hodnota TTL se změní z vypnout na On, je přeindexovány kolekce. Při provádění změn zásady indexování při konzistentní indexování režimu, nebudou uživatelé Všimněte si ke změně indexu. Pokud indexování režimu je nastavena na opožděné index je vždy zachytávání a dojde ke změně hodnota TTL, je index znovu od začátku. Když se změní hodnota TTL a index režim je nastaven na opožděné, dotazy, provádí při opětovném sestavení indexu nevrátí úplné nebo správné výsledky.

Pokud potřebujete přesná data vrácená, neměňte hodnotu TTL, pokud indexování režim je nastaven na opožděné. V ideálním případě je třeba zvolit konzistentní index zajistit konzistentní dotazu výsledky. 

## <a name="faq"></a>Nejčastější dotazy
**Co se TTL náklady mi?**

Není k dispozici bez dalších nákladů na dokument nastavení hodnotu TTL.

**Jak dlouho bude trvat až po hodnotu TTL odstranění dokumentu?**

Dokumenty jsou platnost vypršela ihned, jakmile interval TTL, ZÍSKÁ zapnutý a nebudou přístupné prostřednictvím CRUD nebo dotaz, rozhraní API. 

**Bude TTL na dokument mít žádný vliv na RU poplatky?**

Ne, bude mít žádný vliv na RU poplatky za odstranění dokumenty s vypršenou platností prostřednictvím TTL v Cosmos DB.

**Funkci TTL se vztahují pouze na celý dokumenty, nebo může vypršet hodnoty vlastností pro jednotlivý dokument?**

Hodnota TTL se vztahuje na celý dokument. Pokud chcete vyprší pouze část dokumentu, pak se doporučuje rozbalte část z hlavní dokumentu v samostatných "propojené" dokumentu a pak použít TTL na tomto extrahované dokumentu.

**Má funkci TTL žádné zvláštní požadavky indexování?**

Ano. Kolekce musí mít [indexování sady zásad](indexing-policies.md) konzistentní nebo Lazy. Při pokusu o nastavení DefaultTTL kolekce s indexování nastaven na žádný způsobí chybu, protože se pokouší vypnout indexování na kolekce, která má DefaultTTL, již nastaven.

## <a name="next-steps"></a>Další postup
Další informace o databázi Cosmos Azure, najdete v tématu službu [ *dokumentace* ](https://azure.microsoft.com/documentation/services/cosmos-db/) stránky.

