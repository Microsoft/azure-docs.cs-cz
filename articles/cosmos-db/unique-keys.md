---
title: Jedinečné klíče v Azure Cosmos DB | Microsoft Docs
description: Další informace o použití jedinečné klíče v databázi Azure Cosmos DB.
services: cosmos-db
keywords: jedinečné klíče omezení, porušení omezení jedinečnosti klíče
author: rafats
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: rafats
ms.openlocfilehash: d12109efbb157b1e0c15b1a4c0d005fa98c44858
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261096"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Jedinečné klíče v Azure Cosmos DB

Jedinečné klíče poskytne vývojářům možnost přidávat vrstvu integritu dat do své databáze. Vytvořením jedinečné klíče zásady při vytvoření kontejneru, zajistíte jedinečnost jednu nebo více hodnot za [klíč oddílu](partition-data.md). Po vytvoření kontejneru zásadám jedinečné klíče brání vytvoření všechny nové nebo aktualizované položky s hodnotami, které duplicitní hodnoty určeného jedinečné omezení klíče.   

> [!NOTE]
> Podporuje jedinečné klíče nejnovější verze [.NET](sql-api-sdk-dotnet.md) a [.NET Core](sql-api-sdk-dotnet-core.md) SQL sady SDK a [MongoDB API](mongodb-feature-support.md#unique-indexes). Rozhraní API pro tabulky a rozhraní Graph API nepodporují v tuto chvíli jedinečné klíče. 
> 
>

## <a name="use-case"></a>Případ použití

Jako příklad podíváme, jak se uživatel databáze přidružený [sociálních aplikace](use-cases.md#web-and-mobile-applications) přínos s jedinečné klíče zásady na e-mailové adresy. Tím, že uživatele e-mailová adresa jedinečný klíč, zkontrolujte každý záznam má jedinečnou e-mailovou adresu a možné vytvářet žádné nové záznamy s duplicitní e-mailové adresy. 

Pokud mají uživatelé mohli vytvářet více záznamů se stejnou e-mailová adresa, ale není stejné křestní jméno, příjmení a e-mailovou adresu, můžete přidat další cesty k jedinečné klíče zásady. Místo vytvoření jedinečné klíče založeného na e-mailovou adresu, takže můžete vytvořit jedinečný klíč, který je kombinací křestní jméno, příjmení a e-mailu. V takovém případě je povoleno každou jedinečnou kombinaci tři cesty, takže databáze může obsahovat položky, které mají následující hodnoty cestu. Všechny tyto záznamy by úspěšně prošel zpracováním jedinečné klíče zásady.  

**Povolené hodnoty pro jedinečný klíč firstName, lastName a e-mailu**

|Jméno|Příjmení|E-mailová adresa|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivan|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

Pokud jste se pokusili vložit jiný záznam s jakoukoli kombinací uvedené v předchozí tabulce, by se zobrazit chyba oznamující, že nebyla splněna jedinečné omezení klíče. Chyba, kterou vrátil Azure Cosmos DB je "Prostředek se zadaným id nebo názvem již existuje." nebo "Prostředek se zadaným id, jedinečný index nebo název již existuje." 

## <a name="using-unique-keys"></a>Pomocí jedinečné klíče

Jedinečné klíče musí být definován, když se kontejner vytvoří, a jedinečný klíč je vymezen na klíč oddílu. Pokud chcete pracovat s předchozího příkladu, pokud oddílu podle PSČ, můžete mít záznamy z tabulky duplicitní v každém oddílu.

Nelze aktualizovat existující kontejner používat jedinečné klíče.

Po vytvoření kontejneru zásadám jedinečné klíče nelze změnit zásady, pokud je znovu vytvořit kontejner. Pokud máte existující data, která chcete implementovat jedinečné klíče na, vytvořte nový kontejner a potom pomocí nástroje Migrace příslušná data pro přesun dat do nového kontejneru. Pro kontejnery SQL, použijte [nástroj pro migraci dat](import-data.md). Pro MongoDB kontejnery, použijte [mongoimport.exe nebo mongorestore.exe](mongodb-migrate.md).

Maximálně 16 hodnotami cestu (například /firstName, /lastName, /address/zipCode atd.) můžou být součástí každého jedinečný klíč. 

Každá zásada jedinečné klíče může mít maximálně 10 jedinečné omezení klíčů nebo kombinace a kombinované cesty pro všechny vlastnosti jedinečný index nesmí být delší než 60 znaků. Proto nejprve název předchozího příkladu, který používá, příjmení, e-mailová adresa není právě jedno omezení a používá tři 16 možných cest k dispozici. 

Žádosti o jednotky poplatky za vytváření, aktualizaci, odstranění položky jsou mírně zvýší, když je jedinečné klíče zásady v kontejneru. 

Zhuštěný jedinečné klíče nejsou podporovány. Pokud chybí hodnoty pro některé jedinečné cesty, jsou považovány za zvláštní hodnotu null, která je součástí omezení jedinečnosti.

## <a name="sql-api-sample"></a>Ukázka rozhraní SQL API

Následující příklad kódu ukazuje, jak vytvořit nový kontejner SQL s omezeními dva jedinečné klíče. První omezení je firstName, lastName, e-mailu omezení, které jsou popsané v předchozím příkladu. Druhý omezení je adresa/zipCode uživatelé. Ukázkový soubor JSON, který používá cesty v této zásadě jedinečné klíče následuje příklad kódu. 

```csharp
// Create a collection with two separate UniqueKeys, one compound key for /firstName, /lastName,
// and /email, and another for /address/zipCode.
private static async Task CreateCollectionIfNotExistsAsync(string dataBase, string collection)
{
    try
    {
        await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(dataBase, collection));
    }
    catch (DocumentClientException e)
    {
        if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            DocumentCollection myCollection = new DocumentCollection();
            myCollection.Id = collection;
            myCollection.PartitionKey.Paths.Add("/pk");
            myCollection.UniqueKeyPolicy = new UniqueKeyPolicy
            {
                UniqueKeys =
                new Collection<UniqueKey>
                {
                    new UniqueKey { Paths = new Collection<string> { "/firstName" , "/lastName" , "/email" }}
                    new UniqueKey { Paths = new Collection<string> { "/address/zipcode" } },
          }
            };
            await client.CreateDocumentCollectionAsync(
                UriFactory.CreateDatabaseUri(dataBase),
                myCollection,
                new RequestOptions { OfferThroughput = 2500 });
        }
        else
        {
            throw;
        }
    }
```

Ukázka dokumentu JSON.

```json
{
    "id": "1",
    "pk": "1234",
    "firstName": "Gaby",
    "lastName": "Duperre",
    "email": "gaby@contoso.com",
    "address": 
        {            
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zipcode": 98012
        }
    
}
```
> [!NOTE]
> Poznámka: jedinečný název klíče je prosím velká a malá písmena. Jak je znázorněno v výše příklad, je pro /address/zipcode nastaven jedinečný název. Pokud vaše data ZipCode, pak vloží "null" v jedinečný klíč jako zipcode není roven ZipCode. A z důvodu tohoto rozlišování všechny záznamy s ZipCode nebude možné vložit jako duplicitní "null" nesplňují omezení jedinečné klíče.

## <a name="mongodb-api-sample"></a>Ukázka rozhraní API MongoDB

Následující ukázkový příkaz ukazuje, jak vytvořit jedinečný index na jméno, příjmení a e-mailu pole kolekce uživatelů pro rozhraní API MongoDB. Tím se zajistí jedinečnosti pro kombinaci všechna tři pole na všech dokumentech v kolekci. Pro rozhraní API MongoDB kolekce se vytvoří jedinečný index po vytvoření kolekce, ale před naplnění kolekce.

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```
## <a name="configure-unique-keys-by-using-azure-portal"></a>Konfigurace jedinečné klíče pomocí portálu Azure

V části výše můžete najdete ukázek kódu, které vám ukáže, jak můžete definovat jedinečné omezení klíčů při kolekce je vytvořený pomocí rozhraní API pro SQL nebo MongoDB API. Ale je také možné definovat jedinečné klíče při vytváření kolekce prostřednictvím webového uživatelského rozhraní na portálu Azure. 

- Přejděte na **Průzkumníku dat** ve vašem účtu Cosmos DB
- Klikněte na tlačítko **nové kolekce**
- V části jedinečné klíče ** kliknutím můžete přidat požadované jedinečné omezení klíčů **přidat jedinečný klíč.**

![Zadejte jedinečné klíče v Průzkumníku dat](./media/unique-keys/unique-keys-azure-portal.png)

- Pokud chcete vytvořit jedinečné klíče omezení na cestě lastName, přidáte `/lastName`.
- Pokud chcete vytvořit jedinečné omezení klíče pro kombinaci firstName lastName, přidáte `/lastName,/firstName`

Po dokončení klikněte na tlačítko **OK** k vytvoření této kolekce.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak vytvořit jedinečné klíče pro položky v databázi. Pokud chcete vytvořit kontejner poprvé, přečtěte si [segmentace dat v Azure Cosmos DB](partition-data.md) jako jedinečné klíče a klíče oddílů závisí na sobě navzájem. 


