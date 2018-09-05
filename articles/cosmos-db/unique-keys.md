---
title: Jedinečné klíče ve službě Azure Cosmos DB | Dokumentace Microsoftu
description: Další informace o použití jedinečné klíče v databázi Azure Cosmos DB.
services: cosmos-db
keywords: omezení UNIQUE key, porušení omezení unique key
author: rafats
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: rafats
ms.openlocfilehash: 5811cb1e08ed5d02038da2a4460ae4b63580833b
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696135"
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Jedinečné klíče ve službě Azure Cosmos DB

Jedinečné klíče umožňují vývojářům umožňuje přidat vrstvu integrity dat do své databáze. Vytvořením zásady jedinečného klíče při vytváření kontejneru, zajistíte jedinečnost jedné nebo více hodnot za [klíč oddílu](partition-data.md). Po vytvoření kontejneru s zásady jedinečného klíče zabraňuje vytváření nové nebo aktualizované položky s hodnotami, které duplicitní hodnoty určené omezení unique key.   

> [!NOTE]
> Jedinečné klíče jsou podporovány nejnovější verze [.NET](sql-api-sdk-dotnet.md) a [.NET Core](sql-api-sdk-dotnet-core.md) sad SQL SDK a [rozhraní MongoDB API](mongodb-feature-support.md#unique-indexes). Rozhraní Table API a rozhraní Gremlin API nepodporují v tuto chvíli jedinečné klíče. 
> 
>

## <a name="use-case"></a>Případ použití

Jako příklad, Podívejme se na jak uživatel databáze přidružený [sociální aplikace](use-cases.md#web-and-mobile-applications) můžou mít užitek z budete mít zásady jedinečného klíče pro e-mailové adresy. Tím, že uživatele e-mailová adresa jedinečný klíč, zkontrolujte každý záznam má jedinečnou e-mailovou adresu a žádné nové záznamy se dají vytvářet pomocí duplicitní e-mailové adresy. 

Pokud mají uživatelé moct vytvářet více záznamů se stejným e-mailové adresy, ale není stejný křestní jméno, příjmení a e-mailovou adresu, můžete přidat dalších cest pro zásady jedinečného klíče. Místo vytváření jedinečné klíče založeného na e-mailovou adresu, tak můžete vytvořit jedinečný klíč, který je kombinací křestní jméno, příjmení a e-mailu. V takovém případě může každá jedinečná kombinace tří cest, takže databáze může obsahovat položky, které mají následující hodnoty cest. Každá z těchto záznamů by úspěšně prošel zpracováním zásady jedinečného klíče.  

**Povolené hodnoty pro jméno, příjmení a e-mailu jedinečný klíč**

|Jméno|Příjmení|E-mailová adresa|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivan|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

Pokud jste se pokusili získat vložit jiný záznam s libovolnou kombinací uvedené v předchozí tabulce, by zobrazí se chyba oznamující, že nebyla splněna omezení unique key. Chyba služby Azure Cosmos DB vrátil je "Prostředek se zadaným id nebo názvem již existuje." nebo "Prostředek se zadaným id, název nebo jedinečný index už existuje." 

## <a name="using-unique-keys"></a>Použití jedinečné klíče

Jedinečné klíče musí být definován při vytvoření kontejneru a jedinečný klíč působí na klíč oddílu. K sestavení v předchozím příkladu, pokud rozdělit podle PSČ, může mít záznamy z tabulky duplicitní v každém oddílu.

Nelze aktualizovat existující kontejner, aby používal jedinečné klíče.

Po vytvoření kontejneru s zásady jedinečného klíče nelze změnit zásady, pokud je znovu vytvořit kontejner. Pokud máte existující data, která byste chtěli implementace jedinečné klíče na vytvořit nový kontejner a pak použít nástroj pro migraci příslušná data pro přesun dat do nového kontejneru. Pro kontejnery SQL, použijte [nástroj pro migraci dat](import-data.md). Pro kontejnery MongoDB, použijte [mongoimport.exe nebo mongorestore.exe](mongodb-migrate.md).

Maximálně 16 hodnotami cestu (například /firstName, /lastName, /address/zipCode atd.) mohou být součástí každého jedinečný klíč. 

Každé zásady jedinečného klíče může mít maximálně 10 jedinečných klíčů omezení nebo kombinace a kombinované cesty pro všechny vlastnosti jedinečný index nesmí být delší než 60 znaků. Proto předchozího příkladu, která používá jméno, příjmení, e-mailová adresa je jenom jedno omezení a používá tři 16 možných cest k dispozici. 

Žádosti o jednotky poplatky za vytváření, aktualizace a odstranění položky jsou mírně vyšší, když zásady jedinečného klíče v kontejneru. 

Zhuštěný jedinečné klíče nejsou podporovány. Pokud jsou hodnoty pro některé jedinečné cesty chybí, jsou považovány za zvláštní hodnota null, který se podílí na omezení jedinečnosti.

## <a name="sql-api-sample"></a>Ukázka rozhraní SQL API

Následující příklad kódu ukazuje, jak vytvořit nový kontejner SQL s dvě omezení jedinečných klíčů. Prvním omezením je jméno, příjmení, e-mailu omezení je popsáno v předchozím příkladu. Druhé omezení je adresa/PSC uživatelů. Následuje ukázkový soubor JSON, který používá cesty v zásady jedinečného klíče v příkladu kódu. 

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

Ukázkový dokument JSON.

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
> Poznámka: jedinečný název klíče je prosím malá a velká písmena. Jak je znázorněno výše příklad, nastaví se pro /address/zipcode jedinečný název. Pokud vaše data budou mít PSČ, pak vloží "null" Jedinečný klíč podle PSČ není roven PSČ. A z důvodu tohoto rozlišování velikosti písmen pro všechny záznamy s PSČ nebudou moci být vložen jako duplicitní "null" bude porušovat omezení unique key.

## <a name="mongodb-api-sample"></a>Ukázka rozhraní API MongoDB

Následující ukázkový příkaz ukazuje, jak vytvořit jedinečný index pro pole firstName, lastName a e-mailu kolekce uživatelů pro rozhraní API pro MongoDB. Tím se zajistí jedinečnost kombinace všech tří polí napříč všemi dokumenty v kolekci. Pro kolekce rozhraní MongoDB API se vytvoří jedinečný index po vytvoření kolekce, ale před naplnění kolekce.

> [!NOTE]
> Formát jedinečné klíče pro účty rozhraní API MongoDB se liší od s účty SQL API, kde není nutné zadat znak zpětného lomítka (/) před název pole. 

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```
## <a name="configure-unique-keys-by-using-azure-portal"></a>Konfigurace jedinečné klíče pomocí webu Azure Portal

V části výše si můžete najdete ukázky kódu, které se zobrazí, jak můžete definovat omezení jedinečných klíčů při vytvoření kolekce pomocí rozhraní SQL API nebo rozhraní MongoDB API. Ale je také možné definovat jedinečné klíče při vytváření kolekce prostřednictvím webového uživatelského rozhraní na webu Azure Portal. 

- Přejděte **Průzkumník dat** ve vašem účtu služby Cosmos DB
- Klikněte na tlačítko **novou kolekci**
- V části jedinečné klíče ** požadované omezení jedinečných klíčů můžete přidat kliknutím **přidat jedinečný klíč**

![Definujte jedinečné klíče v Průzkumníku dat](./media/unique-keys/unique-keys-azure-portal.png)

- Pokud chcete vytvořit jedinečné klíče omezení na cestě lastName, přidáte `/lastName`.
- Pokud chcete vytvořit klíče jedinečnosti pro kombinaci firstName lastName, přidat `/lastName,/firstName`

Po dokončení klikněte na tlačítko **OK** k vytvoření této kolekce.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak vytvořit jedinečné klíče pro položky v databázi. Pokud vytvoříte kontejner poprvé, přečtěte si [dělení dat ve službě Azure Cosmos DB](partition-data.md) jako jedinečné klíče a klíče oddílů závisí na sebe navzájem. 


