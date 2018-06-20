---
title: Jak používat službu Azure Table Storage nebo rozhraní Table API služby Azure Cosmos DB z PHP | Microsoft Docs
description: Ukládejte si strukturovaná data v cloudu pomocí služby Azure Table Storage nebo rozhraní Table API služby Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: php
ms.topic: sample
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 19d475c16b672b960b417391b4c3a6efe27f6cd6
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34797936"
---
# <a name="how-to-use-azure-storage-table-service-or-the-azure-cosmos-db-table-api-from-php"></a>Jak používat službu Azure Table Storage nebo rozhraní Table API služby Azure Cosmos DB z PHP
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Přehled
Tato příručka ukazuje, jak provádět běžné scénáře pomocí služby Azure Table Storage a rozhraní Table API služby Azure Cosmos DB. Ukázky jsou napsané v PHP a využívají [klientskou knihovnu služby Azure Table Storage pro PHP][download]. Popsané scénáře zahrnují **vytvoření a odstranění tabulky** a **vkládání, odstraňování a dotazování entit v tabulce**. Další informace o službě Azure Table Storage najdete v části [Další kroky](#next-steps).


## <a name="create-an-azure-service-account"></a>Vytvoření účtu služby Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Vytvoření účtu rozhraní Table API služby Azure Cosmos DB

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-php-application"></a>Vytvoření aplikace PHP

Jediným požadavkem pro vytvoření aplikace PHP umožňující přístup ke službě Table Storage nebo rozhraní Table API služby Azure Cosmos DB je přidání odkazů na třídy v sadě SDK azure-storage-table pro PHP do vašeho kódu. K vytvoření aplikace můžete použít jakékoli vývojové nástroje, včetně Poznámkového bloku.

V této příručce budete používat funkce služby Table Storage nebo Azure Cosmos DB, které je možné volat místně z aplikace PHP nebo v kódu spuštěném v rámci webové role, role pracovního procesu nebo webu Azure.

## <a name="get-the-client-library"></a>Získání klientské knihovny

1. V kořenovém adresáři vašeho projektu vytvořte soubor composer.json a přidejte do něj následující kód:
```json
{
  "require": {
    "microsoft/azure-storage-table": "*"
  }
}
```
2. Do kořenového adresáře stáhněte soubor [composer.phar](http://getcomposer.org/composer.phar). 
3. Otevřete příkazový řádek a spusťte následující příkaz v kořenovém adresáři vašeho projektu:
```
php composer.phar install
```
Případně můžete přejít do úložiště [klientské knihovny služby Azure Table Storage pro PHP](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) na GitHubu a zkopírovat si zdrojový kód.


## <a name="add-required-references"></a>Přidání požadovaných odkazů
Pokud chcete používat službu Table Storage nebo rozhraní API služby Azure Cosmos DB, musíte:

* Přidat odkaz na soubor automatického načítání pomocí příkazu [require_once][require_once].
* Přidat odkazy na všechny třídy, které používáte.

Následující příklad ukazuje vložení souboru automatického načítání a odkazu na třídu **TableRestProxy**.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

V následujících příkladech se vždy zobrazuje příkaz `require_once`, který však odkazuje pouze na třídy nezbytné ke spuštění příkladu.

## <a name="add-a-storage-table-service-connection"></a>Přidání připojení ke službě Table Storage
Pokud chcete vytvořit instanci klienta služby Table Storage, musíte nejprve mít platný připojovací řetězec. Formát připojovacího řetězce služby Table Storage je následující:

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

## <a name="add-an-azure-cosmos-db-connection"></a>Přidání připojení ke službě Azure Cosmos DB
Pokud chcete vytvořit instanci klienta Azure Cosmos DB, musíte nejprve mít platný připojovací řetězec. Formát připojovacího řetězce Azure Cosmos DB je následující:

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

## <a name="add-a-storage-emulator-connection"></a>Přidání připojení k emulátoru úložiště
Získání přístupu k úložišti emulátoru:

```php
UseDevelopmentStorage = true
```

Pokud chcete vytvořit klienta služby Azure Table Storage nebo Azure Cosmos DB, musíte použít třídu **TableRestProxy**. Můžete:

* Do ní předat připojovací řetězec přímo nebo
* použít **CloudConfigurationManager (CCM)** k vyhledání připojovacího řetězce v několika externích zdrojích:
  * Ve výchozím nastavení podporuje jeden externí zdroj – proměnné prostředí.
  * Nové zdroje můžete přidat rozšířením třídy `ConnectionStringSource`.

Ve zde uvedených příkladech se připojovací řetězec předává přímo.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>Vytvoření tabulky
Objekt **TableRestProxy** umožňuje vytvořit tabulku pomocí metody **createTable**. Při vytváření tabulky můžete nastavit časový limit služby Table Storage. (Další informace o časovém limitu služby Table Storage najdete v tématu [Nastavení časových limitů pro operace služby Table Storage][table-service-timeouts].)

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

Další informace o omezeních a názvech tabulek najdete v tématu [Vysvětlení datového modelu služby Table Storage][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky
Pokud chcete do tabulky přidat entitu, vytvořte nový objekt **Entity** a předejte ho do metody **TableRestProxy->insertEntity**. Nezapomeňte, že při vytváření entity musíte zadat `PartitionKey` a `RowKey`. Jedná se o jedinečné identifikátory entity a jsou to hodnoty, které je možné dotazovat mnohem rychleji než ostatní vlastnosti entity. Systém používá `PartitionKey` k automatické distribuci entit tabulky do mnoha uzlů úložiště. Entity se stejnou hodnotou `PartitionKey` se ukládají na stejném uzlu. (Operace s více entitami uloženými na stejném uzlu se provádějí rychleji než s entitami uloženými na různých uzlech.) `RowKey` představuje jedinečné ID entity v rámci oddílu.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

Další informace o typech a vlastnostech služby Table Storage najdete v tématu [Vysvětlení datového modelu služby Table Storage][table-data-model].

Třída **TableRestProxy** nabízí dvě alternativní metody pro vkládání entit: **insertOrMergeEntity** a **insertOrReplaceEntity**. Pokud chcete tyto metody použít, vytvořte nový objekt **Entity** a předejte ho jako parametr do jedné z těchto metod. Obě metody vloží entitu, pokud ještě neexistuje. Pokud entita již existuje, metoda **insertOrMergeEntity** aktualizuje hodnoty vlastností, pokud vlastnosti již existují, a přidá nové vlastnosti, pokud ještě neexistují, zatímco metoda **insertOrReplaceEntity** zcela nahradí existující entitu. Následující příklad ukazuje použití metody **insertOrMergeEntity**. Pokud entita s `PartitionKey` s hodnotou tasksSeattle a `RowKey` s hodnotou 1 ještě neexistuje, vloží se. Pokud se však vložila dříve (jak je znázorněno v předchozím příkladu), vlastnost `DueDate` se aktualizuje a přidá se vlastnost `Status`. Vlastnosti `Description` a `Location` se také aktualizují, ale s použitím hodnot, které je prakticky ponechají beze změny. Pokud by se tyto dvě vlastnosti nepřidaly, jak je znázorněno v příkladu, ale existovaly by v cílové entitě, jejich stávající hodnoty by zůstaly beze změny.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Načtení jedné entity
Metoda **TableRestProxy->getEntity** umožňuje načíst jednu entitu prostřednictvím zadání dotazu na její `PartitionKey` a `RowKey`. V následujícím příkladu se do metody **getEntity** předá klíč oddílu `tasksSeattle` a klíč řádku `1`.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>Načtení všech entit v oddílu
Dotazy na entity se vytvářejí pomocí filtrů (další informace najdete v tématu [Dotazování tabulek a entit][filters]). Pokud chcete načíst všechny entity v oddílu, použijte filtr "PartitionKey eq *název_oddílu*". Následující příklad ukazuje načtení všech entit v oddílu `tasksSeattle` předáním filtru do metody **queryEntities**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Načtení podmnožiny entit v oddílu
Stejným způsobem, jako se použil v předchozím příkladu, je možné načíst jakoukoli podmnožinu entit v oddílu. Podmnožinu načtených entit určuje použitý filtr (další informace najdete v tématu [Dotazování tabulek a entit][filters]). Následující příklad ukazuje použití filtru k načtení všech entit s konkrétní hodnotou `Location` a hodnotou `DueDate` nižší než zadané datum.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>Načtení podmnožiny vlastností entity
Dotaz může načíst podmnožinu vlastností entity. Tato technika, označovaná jako *projekce*, snižuje šířku pásma a může zlepšit výkon dotazů, zejména u velkých entit. Pokud chcete zadat vlastnost, která se má načíst, předejte její název do metody **Query->addSelectField**. Tuto metodu můžete zavolat vícekrát a přidat další vlastnosti. Po spuštění metody **TableRestProxy->queryEntities** budou vrácené entity obsahovat pouze vybrané vlastnosti. (Pokud chcete vrátit podmnožinu entit tabulky, použijte filtr, jak je znázorněno v předchozích dotazech.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>Aktualizace entity
Existující entitu můžete aktualizovat tak, že na ni použijete metody **Entity->setProperty** a **Entity->addProperty** a pak zavoláte metodu **TableRestProxy->updateEntity**. Následující příklad načte entitu, upraví jednu vlastnost, odebere jinou vlastnost a přidá novou vlastnost. Poznámka: Vlastnost můžete odebrat nastavením její hodnoty na **null**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Odstranění entity
Pokud chcete odstranit entitu, předejte název tabulky a hodnoty `PartitionKey` a `RowKey` entity do metody **TableRestProxy->deleteEntity**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Pokud chcete provést kontroly souběžnosti, můžete pomocí metody **DeleteEntityOptions->setEtag** nastavit značku entity, která se má odstranit, a předat objekt **DeleteEntityOptions** jako čtvrtý parametr do metody **deleteEntity**.

## <a name="batch-table-operations"></a>Dávkové operace s tabulkou
Metoda **TableRestProxy->batch** umožňuje v rámci jednoho požadavku spustit více operací. Zde uvedený vzorec zahrnuje přidání operací do objektu **BatchRequest** a následné předání objektu **BatchRequest** do metody **TableRestProxy->batch**. Pokud chcete do objektu **BatchRequest** přidat operaci, můžete několikrát zavolat jakoukoli z následujících metod:

* **addInsertEntity** (přidá operaci insertEntity)
* **addUpdateEntity** (přidá operaci updateEntity)
* **addMergeEntity** (přidá operaci mergeEntity)
* **addInsertOrReplaceEntity** (přidá operaci insertOrReplaceEntity)
* **addInsertOrMergeEntity** (přidá operaci insertOrMergeEntity)
* **addDeleteEntity** (přidá operaci deleteEntity)

Následující příklad ukazuje spuštění operací **insertEntity** a **deleteEntity** v rámci jednoho požadavku. 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Další informace o dávkování operací s tabulkou najdete v tématu [Provádění transakcí skupin entit][entity-group-transactions].

## <a name="delete-a-table"></a>Odstranění tabulky
A konečně, pokud chcete odstranit tabulku, předejte název tabulky do metody **TableRestProxy->deleteTable**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Další kroky
Seznámili jste se se základy služby Azure Table Storage a Azure Cosmos DB. Další informace najdete na následujících odkazech.

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.

* [Centrum pro vývojáře PHP](/develop/php/)

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: http://php.net/require_once
[table-service-timeouts]: https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: https://docs.microsoft.com/rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: https://docs.microsoft.com/rest/api/storageservices/Performing-Entity-Group-Transactions
