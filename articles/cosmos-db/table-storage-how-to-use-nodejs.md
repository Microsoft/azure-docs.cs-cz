---
title: Použití služby Azure Table Storage nebo Azure Cosmos DB rozhraní API pro tabulky z Node.js
description: Ukládat strukturovaná data v cloudu pomocí služby Azure Table Storage nebo Azure Cosmos DB rozhraní API pro tabulky z Node.js.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.custom: devx-track-js
ms.openlocfilehash: 2d40b70d49b1934c9dd2d911369245b1b2e4f2ff
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079695"
---
# <a name="how-to-use-azure-table-storage-or-the-azure-cosmos-db-table-api-from-nodejs"></a>Jak používat službu Azure Table Storage nebo rozhraní Table API služby Azure Cosmos DB z Node.js
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

V tomto článku se dozvíte, jak vytvářet tabulky, ukládat data a provádět operace CRUD s daty. Vyberte buď Azure Table service, nebo rozhraní API pro tabulky Azure Cosmos DB. Ukázky jsou napsány v Node.js.

## <a name="create-an-azure-service-account"></a>Vytvoření účtu služby Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Vytvoření účtu služby Azure Storage**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Vytvoření účtu rozhraní Table API služby Azure Cosmos DB**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="configure-your-application-to-access-azure-storage-or-the-azure-cosmos-db-table-api"></a>Konfigurace aplikace pro přístup ke službě Azure Storage nebo rozhraní Table API služby Azure Cosmos DB

Pokud chcete používat službu Azure Storage nebo Azure Cosmos DB, potřebujete sadu SDK služby Azure Storage pro Node.js, která obsahuje sadu knihoven usnadňujících práci a komunikujících se službami REST služby Storage.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalace balíčku pomocí Node Package Manageru (NPM)

1. Použijte rozhraní příkazového řádku, jako je **PowerShell** (Windows), **Terminál** (Mac) nebo **Bash** (Unix), a přejděte do složky, ve které jste vytvořili svou aplikaci.
2. Do příkazového okna zadejte **npm install azure-storage** . Výstup příkazu je podobný následujícímu příkladu.

   ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
   ```

3. Můžete ručně spustit příkaz **ls** a ověřit, že se vytvořila složka **node_modules** . Uvnitř této složky najdete balíček **azure-storage** obsahující knihovny, které potřebujete pro přístup k úložišti.

### <a name="import-the-package"></a>Import balíčku

Na začátek souboru **server.js** ve vaší aplikaci přidejte následující kód:

```javascript
var azure = require('azure-storage');
```

## <a name="add-your-connection-string"></a>Přidat připojovací řetězec

Můžete se buď připojit k účtu služby Azure Storage nebo účtu Azure Cosmos DB rozhraní API pro tabulky. Získejte připojovací řetězec na základě typu účtu, který používáte.

### <a name="add-an-azure-storage-connection"></a>Přidání připojení ke službě Azure Storage

Modul Azure načte informace potřebné pro připojení k účtu služby Azure Storage z proměnných prostředí AZURE_STORAGE_ACCOUNT a AZURE_STORAGE_ACCESS_KEY nebo AZURE_STORAGE_CONNECTION_STRING. Pokud tyto proměnné prostředí nejsou nastaveny, je nutné při volání zadat informace o účtu `TableService` . Například následující kód vytvoří `TableService` objekt:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myaccesskey');
```

### <a name="add-an-azure-cosmos-db-connection"></a>Přidání připojení ke službě Azure Cosmos DB

Pokud chcete přidat Azure Cosmos DB připojení, vytvořte `TableService` objekt a zadejte název svého účtu, primární klíč a koncový bod. Tyto hodnoty můžete zkopírovat z **Nastavení**  >  **připojovací řetězec** v Azure Portal pro účet Cosmos DB. Například:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myprimarykey', 'myendpoint');
```

## <a name="create-a-table"></a>Vytvoření tabulky

Následující kód vytvoří `TableService` objekt a použije ho k vytvoření nové tabulky.

```javascript
var tableSvc = azure.createTableService();
```

Volání metody `createTableIfNotExists` vytvoří novou tabulku se zadaným názvem, pokud ještě neexistuje. Následující příklad vytvoří novou tabulku mytable, pokud ještě neexistuje:

```javascript
tableSvc.createTableIfNotExists('mytable', function(error, result, response){
  if(!error){
    // Table exists or created
  }
});
```

`result.created` má hodnotu `true`, pokud se vytvoří nová tabulka, a hodnotu `false`, pokud tabulka již existuje. `response` obsahuje informace o požadavku.

### <a name="apply-filters"></a>Použití filtrů

Můžete použít volitelné filtrování na operace prováděné pomocí `TableService` . Operace filtrování můžou zahrnovat protokolování, automatické opakování, atd. Filtry jsou objekty, které implementují metodu s podpisem:

```javascript
function handle (requestOptions, next)
```

Po dokončení předzpracování možností požadavku musí metoda zavolat funkci **next** a předat zpětné volání s následujícím podpisem:

```javascript
function (returnObject, finalCallback, next)
```

V tomto zpětném volání a po zpracování `returnObject` (odpověď z požadavku na server) musí zpětné volání vyvolat, `next` Pokud existuje, aby pokračoval v zpracování jiných filtrů, nebo jednoduše vyvolat `finalCallback` jinak pro ukončení vyvolání služby.

K dispozici jsou dva filtry, které implementují logiku opakování, do sady Azure SDK pro Node.js `ExponentialRetryPolicyFilter` a `LinearRetryPolicyFilter` . Následující vytvoří `TableService` objekt, který používá `ExponentialRetryPolicyFilter` :

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var tableSvc = azure.createTableService().withFilter(retryOperations);
```

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky

Pokud chcete přidat entitu, nejprve vytvořte objekt definující vlastnosti entity. Všechny entity musí obsahovat **PartitionKey** a **RowKey** , což jsou jedinečné identifikátory entity.

* **PartitionKey** – Určuje oddíl, ve kterém je entita uložená.
* **RowKey** – Jednoznačně identifikuje entitu v rámci oddílu.

**PartitionKey** i **RowKey** musí být řetězcové hodnoty. Další informace najdete v tématu [Vysvětlení datového modelu služby Table Storage](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

Následuje příklad definice entity. **DueDate** je definován jako typ `Edm.DateTime` . Zadání typu je volitelné. Pokud typ není zadaný, odvodí se.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
};
```

> [!NOTE]
> K dispozici je také `Timestamp` pole pro každý záznam, který je nastaven službou Azure při vložení nebo aktualizaci entity.

Můžete také použít `entityGenerator` k vytvoření entit. Následující příklad vytvoří stejnou entitu úlohy pomocí `entityGenerator` .

```javascript
var entGen = azure.TableUtilities.entityGenerator;
var task = {
  PartitionKey: entGen.String('hometasks'),
  RowKey: entGen.String('1'),
  description: entGen.String('take out the trash'),
  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
};
```

Chcete-li přidat entitu do tabulky, předejte objekt entity `insertEntity` metodě.

```javascript
tableSvc.insertEntity('mytable',task, function (error, result, response) {
  if(!error){
    // Entity inserted
  }
});
```

Pokud bude operace úspěšná, `result` bude obsahovat [značku entity](https://en.wikipedia.org/wiki/HTTP_ETag) vloženého záznamu a `response` bude obsahovat informace o operaci.

Příklad odpovědi:

```javascript
{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }
```

> [!NOTE]
> Ve výchozím nastavení `insertEntity` nevrací vloženou entitu jako součást `response` informací. Pokud s touto entitou plánujete provádět další operace nebo chcete informace uložit do mezipaměti, může být užitečné vrátit informace jako součást `result`. Můžete to provést tak, že povolíte následujícím `echoContent` způsobem:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Aktualizace entity

Existující entitu můžete aktualizovat několika metodami:

* `replaceEntity` – Aktualizuje existující entitu tím, že ji nahradíte.
* `mergeEntity` – Aktualizuje existující entitu tak, že se do existující entity sloučí nové hodnoty vlastností.
* `insertOrReplaceEntity` – Aktualizuje existující entitu tím, že ji nahradíte. Pokud žádná entita neexistuje, vloží se nová entita.
* `insertOrMergeEntity` – Aktualizuje existující entitu tak, že sloučí nové hodnoty vlastností do existující. Pokud žádná entita neexistuje, vloží se nová entita.

Následující příklad ukazuje aktualizaci entity pomocí `replaceEntity` :

```javascript
tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
  if(!error) {
    // Entity updated
  }
});
```

> [!NOTE]
> Ve výchozím nastavení se při aktualizaci entity nekontroluje, jestli se aktualizovaná data dříve neupravila jiným procesem. Zajištění podpory souběžných aktualizací:
>
> 1. Získejte značku entity aktualizovaného objektu. Ta se vrací jako součást `response` pro všechny operace související s entitou a je možné ji načíst prostřednictvím příkazu `response['.metadata'].etag`.
> 2. Při provádění operace aktualizace entity přidejte do nové entity dříve načtené informace o značce entity. Například:
>
>       entity2['.metadata'].etag = currentEtag;
> 3. Proveďte operaci aktualizace. Pokud se od načtení hodnoty značky entity daná entita upravila, například jinou instancí aplikace, vrátí se `error` oznamující, že nebyla splněná podmínka aktualizace zadaná v požadavku.
>
>

V `replaceEntity` `mergeEntity` případě, že pokud entita, která se aktualizuje, neexistuje, operace aktualizace se nezdařila. Proto pokud chcete entitu Uložit bez ohledu na to, zda již existuje, použijte `insertOrReplaceEntity` nebo `insertOrMergeEntity` .

`result` pro úspěšné operace aktualizace obsahuje **značku entity** aktualizované entity.

## <a name="work-with-groups-of-entities"></a>Práce se skupinami entit

Někdy má smysl odeslat více operací společně v dávce, aby se zajistilo jejich atomické zpracování serverem. K tomu použijte `TableBatch` třídu pro vytvoření dávky a pak použijte `executeBatch` metodu `TableService` pro provedení dávkových operací.

 Následující příklad ukazuje odeslání dvou entit v dávce:

```javascript
var task1 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'Take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20)}
};
var task2 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '2'},
  description: {'_':'Wash the dishes'},
  dueDate: {'_':new Date(2015, 6, 20)}
};

var batch = new azure.TableBatch();

batch.insertEntity(task1, {echoContent: true});
batch.insertEntity(task2, {echoContent: true});

tableSvc.executeBatch('mytable', batch, function (error, result, response) {
  if(!error) {
    // Batch completed
  }
});
```

V případě úspěšných dávkových operací obsahuje `result` informace o jednotlivých operacích v dávce.

### <a name="work-with-batched-operations"></a>Práce s dávkovými operacemi

Operace přidané do dávky můžete prozkoumat zobrazením vlastnosti `operations`. K práci s operacemi můžete využít také následující metody:

* **clear** – Vymaže z dávky všechny operace.
* **getOperations** – Získá z dávky operaci.
* **hasOperations** – Vrátí hodnotu true, pokud dávka obsahuje operace.
* **removeOperations** – Odebere operaci.
* **size** – Vrátí počet operací v dávce.

## <a name="retrieve-an-entity-by-key"></a>Načtení entity podle klíče

Pokud chcete vrátit konkrétní entitu na základě hodnot **PartitionKey** a **RowKey** , použijte metodu **retrieveEntity** .

```javascript
tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
  if(!error){
    // result contains the entity
  }
});
```

Po dokončení operace bude `result` obsahovat příslušnou entitu.

## <a name="query-a-set-of-entities"></a>Dotaz na sadu entit

Pokud chcete dotazovat tabulku, pomocí objektu **TableQuery** sestavte výraz dotazu s použitím následujících klauzulí:

* **select** – Pole, která má dotaz vrátit.
* **where** – Klauzule where.

  * **and** – Podmínka `and` where.
  * **or** – Podmínka `or` where.
* **top** – Počet položek, které se mají načíst.

Následující příklad sestaví dotaz, který vrátí prvních pět položek, které jako PartitionKey mají hodnotu hometasks.

```javascript
var query = new azure.TableQuery()
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

Vzhledem k tomu, že se nepoužila klauzule **select** , vrátí se všechna pole. Pokud chcete provést dotaz na tabulku, použijte **queryEntities** . Následující příklad pomocí tohoto dotazu vrátí entity z tabulky mytable.

```javascript
tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
  if(!error) {
    // query was successful
  }
});
```

V případě úspěchu bude `result.entries` obsahovat pole entit, které odpovídají dotazu. Pokud dotaz nedokáže vrátit všechny entity, token `result.continuationToken` bude mít jinou hodnotu než *null* a můžete ho použít jako třetí parametr metody **queryEntities** pro načtení dalších výsledků. V počátečním dotazu jako třetí parametr použijte hodnotu *null* .

### <a name="query-a-subset-of-entity-properties"></a>Dotaz na podmnožinu vlastností entity

Dotaz na tabulku dokáže z entity načíst pouze několik polí.
Díky tomu se snižuje šířka pásma a může se zlepšit výkon dotazů, zejména u velkých entit. Použijte klauzuli **select** a předejte názvy polí, které se mají vrátit. Například následující dotaz vrátí pouze pole **description** a **dueDate** .

```javascript
var query = new azure.TableQuery()
  .select(['description', 'dueDate'])
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

## <a name="delete-an-entity"></a>Odstranění entity

Entitu můžete odstranit pomocí jejího klíče oddílu a řádku. V tomto příkladu objekt **task1** obsahuje hodnoty **RowKey** a **PartitionKey** entity, která se má odstranit. Objekt se pak předá do metody **deleteEntity** .

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'}
};

tableSvc.deleteEntity('mytable', task, function(error, response){
  if(!error) {
    // Entity deleted
  }
});
```

> [!NOTE]
> Při odstraňování položek zvažte použití značek entit, abyste zajistili, že položku neupravil jiný proces. Informace o použití značek entit najdete v části [Aktualizace entity](#update-an-entity).
>
>

## <a name="delete-a-table"></a>Odstranění tabulky

Následující kód odstraní tabulku z účtu úložiště.

```javascript
tableSvc.deleteTable('mytable', function(error, response){
    if(!error){
        // Table deleted
    }
});
```

Pokud si nejste jisti, jestli tabulka existuje, použijte **deleteTableIfExists** .

## <a name="use-continuation-tokens"></a>Použití tokenů pro pokračování

Pokud z tabulek dotazujete velké množství výsledků, hledejte tokeny pro pokračování. Pro váš dotaz může být k dispozici velké množství dat, kterých si nemusíte všimnout, pokud v rámci sestavování nezajistíte rozpoznání, jestli je přítomný token pro pokračování.

Pokud je takový token přítomný, v objektu **results** vraceném během dotazování entit se nastaví vlastnost `continuationToken`. Tuto vlastnost pak můžete při provádění dotazu použít k pohybu mezi oddíly a entitami tabulky.

Při dotazování můžete zadat parametr `continuationToken` mezi instanci objektu dotazu a funkci zpětného volání:

```javascript
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Při zkoumání objektu `continuationToken` si můžete všimnout vlastností, jako jsou `nextPartitionKey`, `nextRowKey` a `targetLocation`, které je možné použít k iteraci výsledky.

Můžete také použít `top` společně s nástrojem `continuationToken` k nastavení velikosti stránky.

## <a name="work-with-shared-access-signatures"></a>Práce se sdílenými přístupovými podpisy

Sdílené přístupové podpisy (SAS) představují bezpečný způsob zajištění podrobného přístupu k tabulkám bez nutnosti zadávat název nebo klíče vašeho účtu služby Storage. SAS se často používá k zajištění omezeného přístupu k datům, jako je například povolení dotazování záznamů pro mobilní aplikaci.

Důvěryhodná aplikace, jako je například cloudová služba, generuje SAS pomocí metody **generateSharedAccessSignature** objektu **TableService** a poskytuje ho nedůvěryhodné nebo částečně důvěryhodné aplikaci, jako je například mobilní aplikace. SAS se generuje pomocí zásady, která popisuje počáteční a koncové datum platnosti SAS a také úroveň přístupu udělenou držiteli SAS.

Následující příklad vygeneruje novou zásadu sdíleného přístupu, která umožní držiteli SAS dotazovat (r) tabulku a jejíž platnost vyprší 100 minut od okamžiku jejího vytvoření.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
};

var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
var host = tableSvc.host;
```

Všimněte si, že musíte zadat také informace o hostiteli, které se vyžadují při pokusu držitele SAS o přístup k tabulce.

Klientská aplikace pak provádí operace s tabulkou pomocí SAS a metody **TableServiceWithSAS** . Následující příklad se připojí k tabulce a provede dotaz. Informace o formátu tableSAS najdete v článku [udělení omezeného přístupu k prostředkům Azure Storage pomocí článku sdílené přístupové podpisy (SAS)](../storage/common/storage-sas-overview.md) .

```javascript
// Note in the following command, host is in the format: `https://<your_storage_account_name>.table.core.windows.net` and the tableSAS is in the format: `sv=2018-03-28&si=saspolicy&tn=mytable&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D`;

var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
var query = azure.TableQuery()
  .where('PartitionKey eq ?', 'hometasks');

sharedTableService.queryEntities(query, null, function(error, result, response) {
  if(!error) {
    // result contains the entities
  }
});
```

Vzhledem k tomu, že se SAS vygeneroval pouze s přístupem k dotazům, při pokusu o vložení, aktualizaci nebo odstranění entit se vrátí chyba.

### <a name="access-control-lists"></a>Seznamy řízení přístupu

K nastavení zásad přístupu pro SAS můžete použít také seznam řízení přístupu (ACL). To je užitečné, pokud chcete umožnit přístup k tabulce několika klientům, ale pro každého klienta chcete zajistit jiné zásady přístupu.

Seznam ACL se implementuje pomocí pole zásad přístupu, z nichž každá zásada má přidružené ID. Následující příklad definuje dvě zásady, jednu pro uživatele user1 a druhou pro uživatele user2:

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Následující příklad získá aktuální seznam ACL pro tabulku **hometasks** a pak pomocí metody **setTableAcl** přidá nové zásady. Tento přístup umožňuje:

```javascript
var extend = require('extend');
tableSvc.getTableAcl('hometasks', function(error, result, response) {
if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Po nastavení seznamu ACL pak můžete pro zásadu vytvořit SAS založený na ID. Následující příklad vytvoří nový SAS pro uživatele user2:

```javascript
tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });
```

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících materiálech.

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
* Úložiště [sady SDK služby Azure Storage pro Node.js](https://github.com/Azure/azure-storage-node) na GitHubu
* [Azure pro vývojáře v Node.js](/azure/developer/javascript/)
* [Vytvoření webové aplikace Node.js v Azure](../app-service/quickstart-nodejs.md)
* [Sestavení a nasazení aplikace Node.js v cloudové službě Azure](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (pomocí Windows PowerShellu)