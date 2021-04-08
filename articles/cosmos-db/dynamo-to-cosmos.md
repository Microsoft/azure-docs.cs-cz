---
title: Migrace aplikace z Amazon DynamoDB na Azure Cosmos DB
description: Přečtěte si, jak migrovat aplikaci .NET z DynamoDB Amazon na Azure Cosmos DB
author: manishmsfte
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/29/2020
ms.author: mansha
ms.openlocfilehash: 9b4b5fca8017a906fa44b02edcf5f0bdcf6166b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334221"
---
# <a name="migrate-your-application-from-amazon-dynamodb-to-azure-cosmos-db"></a>Migrace aplikace z Amazon DynamoDB na Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB je škálovatelná, globálně distribuovaná a plně spravovaná databáze. Poskytuje zaručený přístup k datům s nízkou latencí. Další informace o Azure Cosmos DB najdete v článku [Přehled](introduction.md) . Tento článek popisuje, jak migrovat aplikaci .NET z DynamoDB na Azure Cosmos DB s minimálními změnami kódu.

## <a name="conceptual-differences"></a>Koncepční rozdíly

Níže jsou uvedené klíčové rozdíly mezi Azure Cosmos DB a DynamoDB:

|  DynamoDB | Azure Cosmos DB  |
|---|---|
|Neuvedeno|  databáze |
|Tabulka      |  Kolekce |
|  Položka |  Dokument |
|Atribut|Pole|
|Sekundární index|Sekundární index|
|Primary Key – klíč oddílu|Partition Key (Klíč oddílu)|
|Primary Key – klíč řazení| Nepožadováno |
|Stream|ChangeFeed|
|Zapsat výpočetní jednotku|Jednotka požadavku (flexibilní, dá se použít pro čtení nebo zápisy)|
|Čtení výpočetní jednotky    |Jednotka požadavku (flexibilní, dá se použít pro čtení nebo zápisy)|
|Globální tabulky| Nepožadováno. Při zřizování účtu Azure Cosmos můžete oblast vybrat přímo (později můžete změnit její oblast).|

## <a name="structural-differences"></a>Strukturální rozdíly

Azure Cosmos DB má jednodušší strukturu JSON v porovnání s hodnotou DynamoDB. Následující příklad ukazuje rozdíly

**DynamoDB**:

Následující objekt JSON představuje formát dat v DynamoDB

```json
{
TableName: "Music",
KeySchema: [
{ 
  AttributeName: "Artist",
  KeyType: "HASH", //Partition key
},
{ 
  AttributeName: "SongTitle",
  KeyType: "RANGE" //Sort key
}
],
AttributeDefinitions: [
{ 
  AttributeName: "Artist",
  AttributeType: "S"
},
{ 
  AttributeName: "SongTitle",
  AttributeType: "S"
}
],
ProvisionedThroughput: {
  ReadCapacityUnits: 1,
  WriteCapacityUnits: 1
 }
}
 ```

**Azure Cosmos DB**:

Následující objekt JSON představuje formát dat v Azure Cosmos DB

```json
{
"Artist": "",
"SongTitle": "",
"AlbumTitle": "",
"Year": 9999,
"Price": 0.0,
"Genre": "",
"Tags": ""
}
```

## <a name="migrate-your-data"></a>Migrace dat

K dispozici jsou různé možnosti, jak migrovat data na Azure Cosmos DB. Další informace najdete v tématu [Možnosti migrace místních nebo cloudových dat do Azure Cosmos DB](cosmosdb-migrationchoices.md) článku.

## <a name="migrate-your-code"></a>Migrace kódu

V tomto článku je v oboru migrace kódu aplikace na Azure Cosmos DB, což je zásadní aspekt migrace databáze. Abychom vám pomohli snížit křivku učení, následující části obsahují porovnání souběžného kódu mezi Amazon DynamoDB a ekvivalentním fragmentem kódu Azure Cosmos DB.

Zdrojový kód stáhnete tak, že naklonujte následující úložiště:

```bash
git clone https://github.com/Azure-Samples/DynamoDB-to-CosmosDB
```

### <a name="pre-requisites"></a>Požadavky

-  .NET Framework 4.7.2
- Visual Studio 2019
- Přístup k Azure Cosmos DBmu účtu rozhraní SQL API
- Místní instalace aplikace Amazon DynamoDB
- Java 8
- Spusťte verzi Amazon DynamoDB ke stažení na portu 8000 (můžete změnit a nakonfigurovat kód).

### <a name="set-up-your-code"></a>Nastavení kódu

Do projektu přidejte následující "balíček NuGet":

```bash
Install-Package Microsoft.Azure.Cosmos 
```

### <a name="establish-connection"></a>Navázat připojení

**DynamoDB**:

V Amazon DynamoDB se pro připojení používá následující kód:

```csharp
    AmazonDynamoDBConfig addbConfig = new AmazonDynamoDBConfig() ;
        addbConfig.ServiceURL = "endpoint";
        try { aws_dynamodbclient = new AmazonDynamoDBClient( addbConfig ); }
```

**Azure Cosmos DB**:

Pokud se chcete připojit Azure Cosmos DB, aktualizujte kód na:

```csharp
client_documentDB = new CosmosClient("your connectionstring from the Azure portal");
```

**Optimalizuje připojení v Azure Cosmos DB**

Pomocí Azure Cosmos DB můžete k optimalizaci připojení použít následující možnosti:

* **ConnectionMode** – použijte režim přímého připojení pro připojení k datovým uzlům ve službě Azure Cosmos DB. Použijte režim brány jenom k inicializaci a ukládání logických adres do mezipaměti a k aktualizaci aktualizací. Další podrobnosti najdete v článku [režimy připojení](sql-sdk-connection-modes.md) .

* **ApplicationRegion** – Tato možnost slouží k nastavení upřednostňované geograficky replikované oblasti, která se používá k interakci s Azure Cosmos DB. Další informace najdete v článku o [globální distribuci](distribute-data-globally.md) .

* **ConsistencyLevel** – Tato možnost slouží k přepsání výchozí úrovně konzistence. Další informace najdete v článku o [úrovních konzistence](consistency-levels.md) .

* **BulkExecutionMode** – Tato možnost slouží ke spouštění hromadných operací nastavením vlastnosti *AllowBulkExecution* na hodnotu true. Další informace najdete v článku o [hromadném importu](tutorial-sql-api-dotnet-bulk-import.md) .

   ```csharp
   client_cosmosDB = new CosmosClient(" Your connection string ",new CosmosClientOptions()
   { 
    ConnectionMode=ConnectionMode.Direct,
    ApplicationRegion=Regions.EastUS2,
    ConsistencyLevel=ConsistencyLevel.Session,
    AllowBulkExecution=true  
   });
   ```

### <a name="provision-the-container"></a>Zřízení kontejneru

**DynamoDB**:

K ukládání dat do Amazon DynamoDB je třeba nejprve vytvořit tabulku. V tomto procesu definujete schéma, typ klíče a atributy, jak je znázorněno v následujícím kódu:

```csharp
// movies_key_schema
public static List<KeySchemaElement> movies_key_schema
  = new List<KeySchemaElement>
{
  new KeySchemaElement
  {
    AttributeName = partition_key_name,
    KeyType = "HASH"
  },
  new KeySchemaElement
  {
    AttributeName = sort_key_name,
    KeyType = "RANGE"
  }
};

// key names for the Movies table
public const string partition_key_name = "year";
public const string sort_key_name      = "title";
  public const int readUnits=1, writeUnits=1; 

    // movie_items_attributes
    public static List<AttributeDefinition> movie_items_attributes
  = new List<AttributeDefinition>
{
  new AttributeDefinition
  {
    AttributeName = partition_key_name,
    AttributeType = "N"
  },
  new AttributeDefinition
  {
    AttributeName = sort_key_name,
    AttributeType = "S"
  }

CreateTableRequest  request;
CreateTableResponse response;

// Build the 'CreateTableRequest' structure for the new table
request = new CreateTableRequest
{
  TableName             = table_name,
  AttributeDefinitions  = table_attributes,
  KeySchema             = table_key_schema,
  // Provisioned-throughput settings are always required,
  // although the local test version of DynamoDB ignores them.
  ProvisionedThroughput = new ProvisionedThroughput( readUnits, writeUnits );
};
```

**Azure Cosmos DB**:

V Amazon DynamoDB musíte zřídit výpočetní jednotky pro čtení & zapisovat výpočetní jednotky. V Azure Cosmos DB zadáte propustnost jako [jednotky žádosti (ru/s)](request-units.md), které lze použít pro všechny operace dynamicky. Data jsou uspořádaná jako databáze > kontejner--> položka. Propustnost můžete určit na úrovni databáze nebo na úrovni kolekce nebo obojího.

Vytvoření databáze:

```csharp
await client_cosmosDB.CreateDatabaseIfNotExistsAsync(movies_table_name);
```

Vytvoření kontejneru:

```csharp
await cosmosDatabase.CreateContainerIfNotExistsAsync(new ContainerProperties() { PartitionKeyPath = "/" + partitionKey, Id = new_collection_name }, provisionedThroughput);
```

### <a name="load-the-data"></a>Načtení dat

**DynamoDB**:

Následující kód ukazuje, jak načíst data v Amazon DynamoDB. MoviesArray sestává ze seznamu dokumentů JSON a pak je potřeba iterovat a načítat dokument JSON do Amazon DynamoDB:

```csharp
int n = moviesArray.Count;
for( int i = 0, j = 99; i < n; i++ )
    {
  try
  {
    string itemJson = moviesArray[i].ToString();
    Document doc = Document.FromJson(itemJson);
    Task putItem = moviesTable.PutItemAsync(doc);
    if( i >= j )
    {
      j++;
      Console.Write( "{0,5:#,##0}, ", j );
      if( j % 1000 == 0 )
        Console.Write( "\n " );
      j += 99;
    }
    await putItem;
```

**Azure Cosmos DB**:

V Azure Cosmos DB můžete pro Stream a zápis napsat `moviesContainer.CreateItemStreamAsync()` . V této ukázce se ale kód JSON deserializace do typu *MovieModel* , aby se ukázala funkce přetypování typů. Tento kód je vícevláknový, který bude používat distribuovanou architekturu Azure Cosmos DB a zrychlit načítání:

```csharp
List<Task> concurrentTasks = new List<Task>();
for (int i = 0, j = 99; i < n; i++)
{
  try
  {
      MovieModel doc= JsonConvert.DeserializeObject<MovieModel>(moviesArray[i].ToString());
      doc.Id = Guid.NewGuid().ToString();
      concurrentTasks.Add(moviesContainer.CreateItemAsync(doc,new PartitionKey(doc.Year)));
      {
          j++;
          Console.Write("{0,5:#,##0}, ", j);
          if (j % 1000 == 0)
              Console.Write("\n               ");
          j += 99;
      }
      
  }
  catch (Exception ex)
  {
      Console.WriteLine("\n     ERROR: Could not write the movie record #{0:#,##0}, because:\n       {1}",
                          i, ex.Message);
      operationFailed = true;
      break;
  }
}
await Task.WhenAll(concurrentTasks);
```

### <a name="create-a-document"></a>Vytvoření dokumentu

**DynamoDB**:

Zápis nového dokumentu v Amazon DynamoDB není typově bezpečný, v následujícím příkladu se jako typ dokumentu používá zástupného prvku NewItem:

```csharp
Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
await writeNew;
```

**Azure Cosmos DB**:

Azure Cosmos DB poskytuje bezpečnost typů prostřednictvím datového modelu. Používáme datový model s názvem MovieModel:

```csharp
public class MovieModel
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("title")]
    public string Title{ get; set; }
    [JsonProperty("year")]
    public int Year { get; set; }
    public MovieModel(string title, int year)
    {
        this.Title = title;
        this.Year = year;
    }
    public MovieModel()
    {

    }
    [JsonProperty("info")]
    public   MovieInfo MovieInfo { get; set; }

    internal string PrintInfo()
    {
        if(this.MovieInfo!=null)
        return            string.Format("\nMovie with title:{1}\n Year: {2}, Actors: {3}\n Directors:{4}\n Rating:{5}\n", this.Id, this.Title, this.Year, String.Join(",",this.MovieInfo.Actors), this.MovieInfo, this.MovieInfo.Rating);
        else
            return string.Format("\nMovie with  title:{0}\n Year: {1}\n",  this.Title, this.Year);
    }
}
```

V Azure Cosmos DB zástupného prvku NewItem bude MovieModel:

```csharp
 MovieModel movieModel = new MovieModel()
            {
                Id = Guid.NewGuid().ToString(),
                Title = "The Big New Movie",
                Year = 2018,
                MovieInfo = new MovieInfo() { Plot = "Nothing happens at all.", Rating = 0 }
            };
    var writeNew= moviesContainer.CreateItemAsync(movieModel, new Microsoft.Azure.Cosmos.PartitionKey(movieModel.Year));
    await writeNew;
```

### <a name="read-a-document"></a>Čtení dokumentu

**DynamoDB**:

Pro čtení v Amazon DynamoDB je nutné definovat primitivní typy:

```csharp
// Create Primitives for the HASH and RANGE portions of the primary key
Primitive hash = new Primitive(year.ToString(), true);
Primitive range = new Primitive(title, false);

  Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
  movie_record = await readMovie;
```

**Azure Cosmos DB**:

U Azure Cosmos DB je však dotaz přirozené (LINQ):

```csharp
IQueryable<MovieModel> movieQuery = moviesContainer.GetItemLinqQueryable<MovieModel>(true)
                        .Where(f => f.Year == year && f.Title == title);
// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
    foreach (MovieModel movie in movieQuery)
    {
      movie_record_cosmosdb = movie;
    }
```

Kolekce Documents v předchozím příkladu bude:

- bezpečný typ
- Zadejte možnost přirozeného dotazu.

### <a name="update-an-item"></a>Aktualizace položky

**DynamoDB**: Pokud chcete aktualizovat položku v Amazon DynamoDB:

```csharp
updateResponse = await client.UpdateItemAsync( updateRequest );
````

**Azure Cosmos DB**:

V Azure Cosmos DB bude aktualizace považována za operaci Upsert operace vložit dokument, pokud neexistuje:

```csharp
await moviesContainer.UpsertItemAsync<MovieModel>(updatedMovieModel);
```

### <a name="delete-a-document"></a>Odstranění dokumentu

**DynamoDB**:

Pokud chcete odstranit položku v Amazon DynamoDB, musíte se znovu zařadit do primitivních hodnot:

```csharp
Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;
      
  Task<Document> delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
```

**Azure Cosmos DB**:

V Azure Cosmos DB můžeme dokument získat a asynchronně ho odstranit:

```csharp
var result= ReadingMovieItem_async_List_CosmosDB("select * from c where c.info.rating>7 AND c.year=2018 AND c.title='The Big New Movie'");
while (result.HasMoreResults)
{
  var resultModel = await result.ReadNextAsync();
  foreach (var movie in resultModel.ToList<MovieModel>())
  {
    await moviesContainer.DeleteItemAsync<MovieModel>(movie.Id, new PartitionKey(movie.Year));
  }
  }
```

### <a name="query-documents"></a>Dotazování na dokumenty

**DynamoDB**:

V Amazon DynamoDB jsou pro dotazování na data požadovány funkce rozhraní API:

```csharp
QueryOperationConfig config = new QueryOperationConfig( );
  config.Filter = new QueryFilter( );
  config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
  config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
  config.AttributesToGet = new List<string> { "year", "title", "info" };
  config.Select = SelectValues.SpecificAttributes;
  search = moviesTable.Query( config ); 
```

**Azure Cosmos DB**:

V Azure Cosmos DB můžete vydávat projekce a filtrovat v jednoduchém dotazu SQL:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.Year, c.Title, c.info from c where Year=1998 AND (CONTAINS(Title,'B') OR CONTAINS(Title,'Hzz'))");
```

V případě operací rozsahu, například "mezi", je třeba provést kontrolu v Amazon DynamoDB:

```csharp
ScanRequest sRequest = new ScanRequest
{
  TableName = "Movies",
  ExpressionAttributeNames = new Dictionary<string, string>
  {
    { "#yr", "year" }
  },
  ExpressionAttributeValues = new Dictionary<string, AttributeValue>
  {
      { ":y_a", new AttributeValue { N = "1960" } },
      { ":y_z", new AttributeValue { N = "1969" } },
  },
  FilterExpression = "#yr between :y_a and :y_z",
  ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
};

ClientScanning_async( sRequest ).Wait( );
```

V Azure Cosmos DB můžete použít dotaz SQL a jednořádkový příkaz:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.title, c.info.actors[0], c.info.directors,c.info.running_time_secs from c where BETWEEN year 1960 AND 1969");
```

### <a name="delete-a-container"></a>Odstranění kontejneru

**DynamoDB**:

Pokud chcete odstranit tabulku v Amazon DynamoDB, můžete zadat:

```csharp
client.DeleteTableAsync( tableName );
```

**Azure Cosmos DB**:

Pokud chcete kolekci v Azure Cosmos DB odstranit, můžete zadat:

```csharp
await moviesContainer.DeleteContainerAsync();
```
Pak databázi odstraňte i v případě, že potřebujete:

```csharp
await cosmosDatabase.DeleteAsync();
```

Jak vidíte, Azure Cosmos DB podporuje přirozené dotazy (SQL), operace jsou asynchronní a mnohem jednodušší. Svůj složitý kód můžete snadno migrovat na Azure Cosmos DB, což po migraci bude jednodušší.

### <a name="next-steps"></a>Další kroky

- Seznamte se s [optimalizací výkonu](performance-tips.md).
- Další informace o [optimalizaci čtení a zápisu](key-value-store-cost.md)
- Další informace o [monitorování v Cosmos DB](monitor-cosmos-db.md)

