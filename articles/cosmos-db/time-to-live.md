---
title: Vypršení platnosti dat v Azure Cosmos DB s časem TTL | Dokumentace Microsoftu
description: Díky TTL Microsoft Azure Cosmos DB poskytuje možnost používat dokumenty z systém automaticky vymazány po určitou dobu.
services: cosmos-db
keywords: Hodnota Time to live
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: sngun
ms.openlocfilehash: 49f6d6ee65ffae71cba8c73301355bfe2bdcd1d6
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480552"
---
# <a name="expire-data-in-azure-cosmos-db-collections-automatically-with-time-to-live"></a>Vypršení platnosti dat v kolekcích Azure Cosmos DB automaticky s časem TTL
Aplikace můžete vytvářet a ukládat velké objemy dat. Některé z těchto dat, jako jsou počítače, generovány události data, protokoly a uživatelské relace, které informace jsou užitečné pouze omezené dobu. Jakmile se data změní přebytečné podle potřeb aplikace je bezpečný pro vyprázdnění dat a snížení požadavkům na ukládání aplikace.

"Time to live" nebo TTL Microsoft Azure Cosmos DB umožňuje mít dokumenty automaticky odstraněna z databáze po určitou dobu. Výchozí čas TTL můžete nastavit na úrovni kolekce a přepsat na základě každý dokument. Po nastavení TTL je jako výchozí kolekce nebo na úrovni dokumentu Cosmos DB se automaticky odeberou dokumenty, které existují po uplynutí této doby čas v sekundách, protože poslední změny.

TTL ve službě Azure Cosmos DB využívá posun vůči při poslední změny dokumentu. K tomu použije `_ts` pole, která existuje pro každý dokument. Pole _ts je časové razítko epocha stylu systému unix představující datum a čas. `_ts` Pole se aktualizuje pokaždé, když se upraví dokumentu. 

## <a name="ttl-behavior"></a>Hodnota TTL chování
Hodnota TTL funkce je řízena pomocí vlastnosti TTL ve dvou úrovních – na úrovni kolekce a dokumentu. Hodnoty jsou nastavené během několika sekund a jsou považovány za delta z `_ts` , že byl dokument naposledy změněno.

1. DefaultTTL pro kolekci
   
   * Pokud chybí (nebo nastavený na hodnotu null), dokumenty se neodstraní automaticky.
   * Pokud k dispozici a hodnota je nastavená na "-", 1 = nekonečné – dokumenty nevyprší ve výchozím nastavení
   * Pokud je k dispozici a hodnota je nastavena na nějaké číslo ("n") – dokumenty vyprší "n" sekund po poslední změny
2. Hodnota TTL pro dokumenty: 
   
   * Vlastnost se vztahuje pouze v případě, že je k dispozici pro kolekce nadřazených DefaultTTL.
   * Přepíše hodnotu DefaultTTL pro kolekce nadřazených.

Co nejdříve po vypršení platnosti dokumentu (`ttl`  +  `_ts` < = aktuální čas serveru), dokument je označena jako "neplatná." Žádná operace bude možné na těchto dokumentech po uplynutí této doby a vyloučí z výsledků všechny dotazy provést. Dokumenty v systému se fyzicky odstraní a budou odstraněny na pozadí tj později. To nespotřebovává žádné [jednotek žádosti (ru)](request-units.md) z kolekce rozpočtu.

Výše uvedené logiku je možné zobrazit v matici následující:

|  | DefaultTTL chybí nebo není nastaven na kolekci | DefaultTTL = -1 v kolekci | DefaultTTL = "n" na kolekci |
| --- |:--- |:--- |:--- |
| V dokumentu chybí hodnota TTL |Nic k přepsání na úrovni dokumentu, protože dokument a kolekce nemají žádný koncept TTL. |Platnost skončí žádné dokumenty v této kolekci. |Dokumenty v této kolekci vyprší, když uplyne interval n. |
| Hodnota TTL = -1 pro dokument |Nic k přepsání na úrovni dokumentu od kolekce nedefinuje vlastnost DefaultTTL, můžete přepsat dokument. Hodnota TTL dokumentu je neinterpretované v systému. |Platnost skončí žádné dokumenty v této kolekci. |Dokument s TTL =-1 v této kolekci nikdy nevyprší. Další dokumenty vyprší po uplynutí intervalu "n". |
| Hodnota TTL = n v dokumentu |Nic k přepsání na úrovni dokumentu. Hodnota TTL dokumentu je neinterpretované v systému. |Dokument s TTL = n platnost vyprší po n interval v sekundách. Další dokumenty, které budou dědit interval-1 a nikdy nevyprší. |Dokument s TTL = n platnost vyprší po n interval v sekundách. Další dokumenty, které zdědí "n" intervalu z kolekce. |

## <a name="configuring-ttl"></a>Konfigurace hodnoty TTL
Ve výchozím nastavení je čas TTL zakázané ve výchozím nastavení ve všech kolekcích Cosmos DB a na všechny dokumenty. Hodnota TTL lze nastavit programově, nebo pomocí webu Azure portal. Konfigurace hodnoty TTL z webu Azure portal postupujte následovně:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) a přejděte do svého účtu Azure Cosmos DB.  

2. Přejděte do kolekce, kterou chcete nastavit hodnotu TTL, otevřete **škálování a nastavení** podokně. Uvidíte, že čas TTL je ve výchozím nastavení nastavena na **vypnout**. Můžete změnit na **na (žádná výchozí hodnota)** nebo **na**.

   **vypnout** – dokumenty nejsou automaticky odstraněny.  
   **Zapnuto (žádná výchozí)** – tato možnost nastaví hodnotu TTL "-1" (nekonečné), což znamená, že dokumenty nevyprší ve výchozím nastavení.  
   **na** – platnost dokumentů "n" sekund po poslední změny.  

   ![Nastavit čas TTL](./media/time-to-live/set-ttl-in-portal.png)

## <a name="enabling-ttl"></a>Povolení TTL
Pokud chcete povolit interval TTL, ZÍSKÁ v kolekci nebo dokumenty v kolekci, musíte nastavit vlastnost DefaultTTL kolekce na hodnotu -1 nebo nenulové kladné číslo. Nastavení DefaultTTL-1 znamená, která ve výchozím nastavení se všechny dokumenty v kolekci stále pracovat, ale služby Cosmos DB, měli byste sledovat tuto kolekci pro dokumenty, které mají přepsat toto výchozí nastavení.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive =-1; //never expire by default

    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(databaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });

## <a name="configuring-default-ttl-on-a-collection"></a>Konfigurace výchozí TTL kolekce
Budete moct nakonfigurovat výchozí čas TTL na úrovni kolekce. Pokud chcete nastavit interval TTL, ZÍSKÁ v kolekci, budete muset zadat nenulové kladné číslo určující dobu v sekundách, po vypršení platnosti všech dokumentů v kolekci po poslední úpravy dokumentu časové razítko (`_ts`). Nebo můžete nastavit jako výchozí hodnotu-1, což znamená, že všechny dokumenty vložen do kolekce po neomezenou dobu pracovat ve výchozím nastavení.

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = "orders";
    collectionDefinition.PartitionKey.Paths.Add("/customerId");
    collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24; // expire all documents after 90 days
    
    DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
        "/dbs/salesdb",
        collectionDefinition,
        new RequestOptions { OfferThroughput = 20000 });


## <a name="setting-ttl-on-a-document"></a>Nastavení hodnoty TTL dokumentu
Kromě nastavení výchozí hodnota TTL na kolekci, můžete nastavit konkrétní TTL na úrovni dokumentu. Tím se přepíše výchozí kolekci.

* Pokud chcete nastavit interval TTL, ZÍSKÁ v dokumentu, budete muset zadat nenulové kladné číslo, který určuje dobu v sekundách, po vypršení platnosti dokumentu po poslední úpravy dokumentu časové razítko (`_ts`).
* Pokud dokument neobsahuje žádné hodnoty TTL pole, se uplatní výchozí kolekci.
* Pokud hodnota TTL je zakázaná na úrovni kolekce, pole hodnoty TTL v dokumentu budou ignorovány, dokud hodnota TTL je znovu povolena na kolekci.

Tady je fragment kódu ukazuje, jak nastavit dobu platnosti TTL dokumentu:

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


## <a name="extending-ttl-on-an-existing-document"></a>Rozšíření hodnoty TTL na existující dokument
Hodnota TTL dokumentu můžete resetovat provedením jakékoli operace zápisu na dokument. To uděláte, bude tato hodnota nastavena `_ts` aktuální čas a odpočítávání do vypršení platnosti dokumentu, úmluvu `ttl`, začnou znovu. Pokud chcete provést změnu `ttl` dokumentu, můžete aktualizovat pole, jak vám pomůžou s další nastavitelné pole.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = 60 * 30 * 30; // update time to live
    
    response = await client.ReplaceDocumentAsync(readDocument);

## <a name="removing-ttl-from-a-document"></a>Odebrání hodnoty TTL dokumentu
Pokud byla nastavena hodnotu TTL dokumentu a tohoto dokumentu vyprší už nechcete, pak můžete načíst dokument, odeberte pole interval TTL, ZÍSKÁ a nahradit dokument na serveru. Pokud pole Hodnota TTL je odebrán z dokumentu, použije se výchozí kolekci. K zastavení dokumentu z vypršení platnosti a není odvozena od kolekce pak musíte nastavit hodnotu TTL na hodnotu -1.

    response = await client.ReadDocumentAsync(
        "/dbs/salesdb/colls/orders/docs/SO05"), 
        new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });
    
    Document readDocument = response.Resource;
    readDocument.TimeToLive = null; // inherit the default TTL of the collection
    
    response = await client.ReplaceDocumentAsync(readDocument);

## <a name="disabling-ttl"></a>Zakázání TTL
Zakázat TTL výhradně na kolekci a zastavte sledovací proces na pozadí z hledání dokumenty s vypršenou platností, které by se měla odstranit vlastnost DefaultTTL v kolekci. Odstraňuje se tato vlastnost se liší od nastavení na hodnotu -1. Nastavení na hodnotu-1 znamená, že nové dokumenty přidána do kolekce stále pracovat, ale je možné přepsat na konkrétní dokumenty v kolekci. Odebrání této vlastnosti zcela z kolekce znamená, že, že žádné dokumenty vyprší, i když jsou dokumenty, které jste explicitně přepsat předchozí výchozí.

    DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
    
    // Disable TTL
    collection.DefaultTimeToLive = null;
    
    await client.ReplaceDocumentCollectionAsync(collection);

<a id="ttl-and-index-interaction"></a> 
## <a name="ttl-and-index-interaction"></a>Hodnota TTL a index interakce
Přidání nebo změna nastavení TTL na kolekci změní podkladové indexu. Pokud hodnota TTL se změní z vypnout na On, je přeindexovány kolekce. Při provádění změn zásady indexování při indexování režim je konzistentní vzhledem k aplikacím, uživatelé nebudou Všimněte si, že ke změně indexu. Pokud indexování režim je nastavený opožděné, index je vždy zachytávání a pokud se změní hodnota TTL, index je znovu vytvořit úplně od začátku. Při změně hodnoty TTL a index režim je nastaven na opožděné, dotazy, které provádí během opětovné sestavení indexu nevrátí úplné nebo správné výsledky.

Pokud potřebujete přesně vrácená data, neměňte hodnotu TTL, při indexování režim je nastaven na opožděná. V ideálním případě by měla být zvolena konzistentní index zajistit konzistentní výsledků. 

## <a name="faq"></a>Nejčastější dotazy
**Co TTL stojí mě?**

Se neúčtují žádné další poplatky nastavení hodnotu TTL dokumentu.

**Jak dlouho bude trvat až po interval TTL, ZÍSKÁ odstranění dokumentu?**

Dokumenty jsou platnost vypršela ihned, jakmile hodnota TTL je v provozu a nebudete mít přístup přes CRUD a dotazů rozhraní API. 

**Hodnota TTL dokumentu může mít libovolný dopad na RU poplatky?**

Ne, bude existovat žádný vliv na RU poplatky za odstranění dokumenty s vypršenou platností prostřednictvím TTL ve službě Cosmos DB.

**Funkce TTL pouze nevztahují na všechny dokumenty, nebo může vypršet hodnoty vlastností pro jednotlivý dokument?**

Hodnota TTL se vztahuje na celý dokument. Pokud chcete do vypršení platnosti jen část dokumentu, pak se doporučuje extrahovat část z hlavního dokumentu do samostatného "propojené" dokumentu a pak použít hodnotu TTL pro daný dokument byl extrahován.

**Má funkci TTL žádné zvláštní požadavky indexování?**

Ano. Kolekce musí mít [indexování nastavené zásady](indexing-policies.md) konzistentní nebo Lazy. Pokoušíte se nastavit DefaultTTL na kolekci s indexování nastavenou na None způsobí chybu, protože se pokouší vypnout indexování kódu na kolekci, která má DefaultTTL již nastaven.

## <a name="next-steps"></a>Další postup
Další informace o službě Azure Cosmos DB, najdete v tématu ke službě [ *dokumentaci* ](https://azure.microsoft.com/documentation/services/cosmos-db/) stránky.

