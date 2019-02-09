---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: ff7ba04271c150018f2c55b62e40542a686608cf
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55905260"
---
## <a name="create-client"></a>Vytvoření připojení klienta
Vytvořte připojení klienta tak, že vytvoříte objekt `WindowsAzure.MobileServiceClient`.  Nahraďte `appUrl` adresou URL vaší mobilní aplikace.

```javascript
var client = WindowsAzure.MobileServiceClient(appUrl);
```

## <a name="table-reference"></a>Práce s tabulkami
Pro přístup k datům a jejich aktualizaci vytvořte odkaz na back-endovou tabulku. Nahraďte `tableName` názvem vaší tabulky.

```javascript
var table = client.getTable(tableName);
```

Jakmile budete mít odkaz na tabulku, můžete s ní dále pracovat:

* [Dotazování na tabulku](#querying)
  * [Filtrování dat](#table-filter)
  * [Procházení dat po stránkách](#table-paging)
  * [Řazení dat](#sorting-data)
* [Vkládání dat](#inserting)
* [Úprava dat](#modifying)
* [Odstranění dat](#deleting)

### <a name="querying"></a>Jak: Dotazování odkazu na tabulku
Jakmile budete mít odkaz na tabulku, můžete jej použít k dotazování na data na serveru.  Dotazy se sestavují v jazyce podobném jazyku LINQ.
Pokud chcete vrátit všechna data z tabulky, použijte následující kód:

```javascript
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

S výsledky se zavolá funkce success.  Nepoužívejte ve funkci success smyčku `for (var i in results)` – ta provádí iterace nad informacemi zahrnutými ve výsledcích při použití dalších funkcí dotazování (například `.includeTotalCount()`).

Další informace o syntaxi dotazu najdete v [Dokumentaci k objektu dotazu].

#### <a name="table-filter"></a>Filtrování dat na serveru
Můžete použít klauzuli `where` na odkaz na tabulku:

```javascript
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Můžete také použít funkci, která filtruje objekt.  V takovém případě se právě filtrovanému objektu přiřadí proměnná `this`.  Následující kód je funkčně srovnatelný s předchozím příkladem:

```javascript
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

#### <a name="table-paging"></a>Procházení dat po stránkách
Použijte metody `take()` a `skip()`.  Pokud například chcete rozdělit tabulku na záznamy po stovkách řádků:

```javascript
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

Metoda `.includeTotalCount()` slouží k přidání pole totalCount do objektu výsledků.  Pole totalCount obsahuje celkový počet záznamů, které by se vrátily, kdyby nebylo použité stránkování.

Následně můžete pomocí proměnné pages a několika tlačítek uživatelského rozhraní zobrazit seznam stránek. K načtení nových záznamů pro jednotlivé stránky použijte metodu `loadPage()`.  Pro rychlý přístup k již načteným záznamům implementujte ukládání do mezipaměti.

#### <a name="sorting-data"></a>Jak: Vrácení seřazených dat
Použijte metody dotazu `.orderBy()` nebo `.orderByDescending()`:

```javascript
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Další informace o objektu dotazu najdete v [Dokumentaci k objektu dotazu].

### <a name="inserting"></a>Jak: Vložení dat
Vytvořte objekt JavaScriptu s vhodným datem a asynchronně zavolejte metodu `table.insert()`:

```javascript
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Po úspěšném vložení se vrátí vložená položka i s dalšími poli požadovanými pro operace synchronizace.  Aktualizujte tyto informace ve vlastní mezipaměti pro pozdější aktualizace.

Sada Node.js Server SDK ve funkci Azure Mobile Apps podporuje dynamické schéma pro účely vývoje.  Dynamické schéma umožňuje přidávat do tabulky sloupce tak, že je zadáte v operaci insert nebo update.  Před nasazením aplikace do ostrého provozu doporučujeme dynamické schéma vypnout.

### <a name="modifying"></a>Jak: Úprava dat
Podobně jako u metody `.insert()` byste měli vytvořit objekt aktualizace a pak zavolat metodu `.update()`.  Objekt aktualizace musí obsahovat ID záznamu, který se má aktualizovat – ID získáte při čtení záznamu nebo zavoláním metody `.insert()`.

```javascript
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

### <a name="deleting"></a>Jak: Odstranění dat
Pokud chcete odstranit záznam, zavolejte metodu `.del()`.  V odkazu na objekt předejte ID:

```javascript
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```
