---
title: Správa grafu dvojčat s využitím vztahů
titleSuffix: Azure Digital Twins
description: Podívejte se, jak spravovat graf digitálních vláken pomocí propojení s relacemi.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 62deca7ed1c34bbefed7fb76224db6ec8ab12dae
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147126"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Správa grafu digitálních vláken pomocí vztahů

Srdcem digitálních vláken Azure je [dvojitě graf](concepts-twins-graph.md) , který představuje celé prostředí. Neřízený graf se skládá z jednotlivých digitálních vláken propojených prostřednictvím **vztahů** . 

Jakmile budete mít funkční [instanci digitálních vláken Azure](how-to-set-up-instance-portal.md) a nastavili jste [ověřovací](how-to-authenticate-client.md) kód v klientské aplikaci, můžete použít [**rozhraní API DigitalTwins**](/rest/api/digital-twins/dataplane/twins) k vytváření, úpravám a odstraňování digitálních vláken a jejich vztahů v instanci digitálních vláken Azure. Můžete také použít [rozhraní .NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)nebo rozhraní příkazového [řádku Azure Digital revlákens](how-to-use-cli.md).

Tento článek se zaměřuje na správu vztahů a grafu jako celku. Chcete-li pracovat s jednotlivými digitálními podseznamy, přečtěte si téma [*Postup: Správa digitálních vláken*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]
    
[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Vytvoření relací

Vztahy popisují, jak jsou vzájemně propojeny různé digitální vazby, které tvoří základ pro dvojitou graf.

Vztahy se vytvářejí pomocí `CreateRelationship()` volání. 

Chcete-li vytvořit relaci, je nutné zadat následující:
* Zdrojové ID vlákna ( `srcId` ve vzorovém kódu níže): ID vlákna, kde vztah pochází.
* Cílové ID vlákna ( `targetId` v níže uvedeném příkladu kódu): ID vlákna, do kterého relace dorazí.
* Název vztahu ( `relName` v níže uvedeném příkladu kódu): obecný typ vztahu, například _Contains_ .
* ID vztahu ( `relId` v níže uvedeném příkladu kódu): konkrétní název pro tento vztah, například _Relationship1_ .

ID vztahu musí být jedinečné v rámci daného zdroje vlákna. Nemusí být globálně jedinečný.
Například pro zdvojenou *foo* musí být každé konkrétní ID vztahu jedinečné. Nicméně jiný ovládací prvek s dvojitou *čárkou* může mít odchozí vztah, který odpovídá stejnému ID relace *foo* .

Následující ukázka kódu ukazuje, jak vytvořit relaci v instanci digitálních vláken Azure.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
            
        }
```
V metodě Main teď můžete zavolat `CreateRelationship()` funkci a vytvořit tak relaci, jako je _contains_ tato: 

```csharp
await CreateRelationship(client, srcId, targetId, "contains");
```
Chcete-li vytvořit více relací, můžete opakovat volání stejné metody a předáním různých typů relací do argumentu. 

Další informace o pomocné třídě `BasicRelationship` naleznete v tématu [*How to: use the Azure Digital revlákens API and SDK*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Vytvoření více vztahů mezi dvojitými hodnotami

Vztahy mohou být klasifikovány buď: 

* Odchozí vztahy: vztahy patřící k tomuto zdvojenému bodu směrem ven, aby se připojily k ostatním nevlákenám. `GetRelationshipsAsync()`Metoda slouží k získání odchozích vztahů vlákna.
* Příchozí vztahy: relace patřící k ostatním nevlákenám, které odkazují na tento objekt vláken, aby se vytvořil odkaz "příchozí". `GetIncomingRelationshipsAsync()`Metoda slouží k získání příchozích relací vlákna.

Neexistuje žádné omezení počtu vztahů, které můžete mít mezi dvěma dvojitými vláknami – můžete mít tolik vztahů mezi dvojitými možnostmi, jak budete chtít. 

To znamená, že můžete vyjádřit několik různých typů vztahů mezi dvěma dvojitými vlákna najednou. Například *Dvojitá* a může mít jak *uloženou* *relaci, tak* i vytvářený vztah s *dvojitým B* .

V případě potřeby můžete dokonce vytvořit několik instancí stejného typu relace mezi dvěma dvěma typy vláken. V tomto příkladu může mít *Dvojitá a* dvě různé *uložené* relace s *dvojitým B* , pokud mají relace různá ID vztahů.

## <a name="list-relationships"></a>Výpis relací

Chcete-li získat přístup k seznamu **odchozích** relací pro danou dvojitou hodnotu v grafu, můžete použít `GetRelationships()` metodu, například:

```csharp
await client.GetRelationships()
```

Vrátí `Azure.Pageable<T>` nebo `Azure.AsyncPageable<T>` , v závislosti na tom, zda používáte synchronní nebo asynchronní verzi volání.

Tady je příklad, který načte seznam vztahů:

```csharp
public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

```
Nyní můžete zavolat tuto metodu pro zobrazení odchozích vztahů vláken, jako jsou:

```csharp
await FindOutgoingRelationshipsAsync(client, twin_Id);
```
Načtené relace můžete použít k přechodu na další vlákna v grafu. Provedete to tak, že si přečtete `target` pole z vráceného vztahu a použijete ho jako ID pro vaše další volání `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Najít příchozí relace k digitálnímu vlákna

Digitální vlákna Azure také obsahuje rozhraní API pro vyhledání všech vztahů _ *příchozích* * s daným dvojitým. To je často užitečné pro zpětnou navigaci nebo při odstraňování vlákna.

Předchozí ukázka kódu se zaměřuje na hledání odchozích relací z vlákna. Následující příklad je strukturován podobně, ale místo toho najde *příchozí* relace na vlákna.

Všimněte si, že `IncomingRelationship` volání nevrátí úplný text vztahu.

```csharp
public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }
```

Nyní můžete zavolat tuto metodu, chcete-li zobrazit příchozí vztahy vláken, jako jsou tyto:

```csharp
await FindIncomingRelationshipsAsync(client, twin_Id);
```
### <a name="list-all-twin-properties-and-relationships"></a>Vypsat všechny zdvojené vlastnosti a vztahy

Pomocí výše uvedených metod můžete pro výpis odchozích a příchozích vztahů na vlákna vytvořit metodu, která vytiskne úplné informace, včetně vlastností vlákna a obou typů jejich vztahů. Tady je ukázková metoda, `FetchAndPrintTwinAsync()` která ukazuje, jak to udělat.

```csharp  
private static async Task FetchAndPrintTwinAsync(DigitalTwinsClient client, string twin_Id)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }
```

Tuto funkci teď můžete zavolat v metodě Main takto: 

```csharp
await FetchAndPrintTwinAsync(client, targetId);
```
## <a name="delete-relationships"></a>Odstranit relace

První parametr určuje zdrojovou nevlákennou (zdvojené místo, kde relace vznikla). Druhým parametrem je ID vztahu. Potřebujete jak zdvojené ID, tak ID vztahu, protože identifikátory relací jsou jedinečné pouze v rámci rozsahu vlákna.

```csharp
private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
```

Nyní můžete zavolat tuto metodu a odstranit tak relaci, například:

```csharp
await DeleteRelationship(client, srcId, relId);
```
## <a name="create-a-twin-graph"></a>Vytvoření vlákna s dvojitou křivkou 

Následující fragment kódu spustitelný používá operace vztahu z tohoto článku k vytvoření cyklického grafu z digitálních vláken a vztahů.

### <a name="set-up-the-runnable-sample"></a>Nastavení ukázky spustitelný

Fragment kódu používá [*Room.jsv*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) a [*Floor.jsv*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) definicích modelů z [*kurzu: Prozkoumejte digitální vlákna Azure pomocí ukázkové klientské aplikace*](tutorial-command-line-app.md). Pomocí těchto odkazů můžete přejít přímo na soubory nebo je stáhnout jako součást celého kompletního ukázkového [projektu.](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) 

Než spustíte ukázku, udělejte toto:
1. Stáhněte si soubory modelu, umístěte je do projektu a nahraďte `<path-to>` zástupné symboly v následujícím kódu a sdělte tak programu, kde se mají najít.
2. Zástupný symbol nahraďte `<your-instance-hostname>` názvem hostitele instance digitálního vlákna Azure.
3. Přidejte tyto balíčky do projektu:
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Pokud chcete ukázku spustit přímo, budete také muset nastavit místní přihlašovací údaje. V další části se to provede.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Spuštění ukázky

Po dokončení výše uvedeného postupu můžete přímo spustit následující vzorový kód.

```csharp 
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload models");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            string dtdl1 = File.ReadAllText("<path-to>/Floor.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the models to the service
            await client.CreateModelsAsync(typeList);

            //Create new (Floor) digital twin
            BasicDigitalTwin floorTwin = new BasicDigitalTwin();
            string srcId = "myFloorID";
            floorTwin.Metadata = new DigitalTwinMetadata();
            floorTwin.Metadata.ModelId = "dtmi:example:Floor;1";
            //Floor twins have no properties, so nothing to initialize
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(srcId, floorTwin);
            Console.WriteLine("Twin created successfully");

            //Create second (Room) digital twin
            BasicDigitalTwin roomTwin = new BasicDigitalTwin();
            string targetId = "myRoomID";
            roomTwin.Metadata = new DigitalTwinMetadata();
            roomTwin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            roomTwin.Contents = props;
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(targetId, roomTwin);
            
            //Create relationship between them
            await CreateRelationship(client, srcId, targetId, "contains");
            Console.WriteLine();

            //Print twins and their relationships
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete the relationship
            Console.WriteLine("Deleting the relationship");
            await DeleteRelationship(client, srcId, $"{srcId}-contains->{targetId}");
            Console.WriteLine();

            //Print twins and their relationships again
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        private async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }

        }

        private static async Task FetchAndPrintTwinAsync(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        private static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");
                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
    }
}
```

Tady je výstup konzoly výše uvedeného programu: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Výstup na konzole zobrazuje zdvojené podrobnosti, příchozí a odchozí vztahy vláken." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Dvojitým grafem je koncept vytváření vztahů mezi vlákna. Pokud chcete zobrazit vizuální znázornění s dvojitým grafem, přečtěte si část [_Visualization *](how-to-manage-graph.md#visualization) tohoto článku. 

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Vytvoření vlákna s dvojitou křivkou z tabulky

V praktických případech použití budou zdvojené hierarchie často vytvořeny z dat uložených v jiné databázi nebo v tabulce. Tato část ukazuje, jak lze tabulku analyzovat.

Vezměte v úvahu následující tabulku dat s popisem sady digitálních vláken a relací, které se mají vytvořit.

| ID modelu| Zdvojené ID (musí být jedinečné) | Název vztahu | Cílové ID vlákna | Dvojitá inicializační data |
| --- | --- | --- | --- | --- |
| dtmi: Příklad: Floor; 1 | Floor1 |  obsahuje | Room1 |{"Teplota": 80; "vlhkost": 60}
| dtmi: Příklad: Floor; 1 | Floor0 |  má      | Room0 |{"Teplota": 70; "vlhkost": 30}
| dtmi: Příklad: místnost; 1  | Room1 | 
| dtmi: Příklad: místnost; 1  | Room0 |

Následující kód používá [rozhraní Microsoft Graph API](/graph/overview) ke čtení tabulky a k vytvoření grafu s dynamickými podmnožinami digitálních vláken Azure z výsledků.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string modelId = row[0].GetString();
    string sourceId = row[1].GetString();
    string relName = row[2].GetString();
    string targetId = row[3].GetString();
    string initData = row[4].GetString();
    
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (sourceId != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = sourceId,
            TargetId = targetId,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Contents = initData;
    // Set the type of twin to be created
    twin.Metadata = new DigitalTwinMetadata() { ModelId = modelId };
    
    try
    {
        await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(sourceId, twin);
    }
    catch (RequestFailedException e)
    {
       Console.WriteLine($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            await client.CreateOrReplaceRelationshipAsync(rec.sourceId, Guid.NewGuid().ToString(), rec);
        }
        catch (RequestFailedException e)
        {
            Console.WriteLine($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>Správa vztahů pomocí rozhraní příkazového řádku

Vlákna a jejich vztahy se dají spravovat taky pomocí rozhraní příkazového řádku Azure Digital revlákens CLI. Příkazy najdete v tématu [*Postupy: použití rozhraní příkazového řádku Azure Digital zdvojené*](how-to-use-cli.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si o dotazování na vyzdvojený graf digitálních vláken Azure:
* [*Koncepty: dotazovací jazyk*](concepts-query-language.md)
* [*Postupy: dotazování na nevlákenný graf*](how-to-query-graph.md)