---
title: Správa grafu dvojčat s využitím vztahů
titleSuffix: Azure Digital Twins
description: Podívejte se, jak spravovat graf digitálních vláken pomocí propojení s relacemi.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a0ab8f8ff3f2134c205338dfe8e6f2e887a5a053
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949611"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Správa grafu digitálních vláken pomocí vztahů

Srdcem digitálních vláken Azure je [dvojitě graf](concepts-twins-graph.md) , který představuje celé prostředí. Dvojitý graf se skládá z jednotlivých digitálních vláken propojených prostřednictvím **vztahů**.

Jakmile budete mít funkční [instanci digitálních vláken Azure](how-to-set-up-instance-portal.md) a nastavili jste [ověřovací](how-to-authenticate-client.md) kód v klientské aplikaci, můžete použít [**rozhraní API DigitalTwins**](how-to-use-apis-sdks.md) k vytváření, úpravám a odstraňování digitálních vláken a jejich vztahů v instanci digitálních vláken Azure. Můžete také použít [rozhraní .NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)nebo rozhraní příkazového [řádku Azure Digital revlákens](how-to-use-cli.md).

Tento článek se zaměřuje na správu vztahů a grafu jako celku. Chcete-li pracovat s jednotlivými digitálními podseznamy, přečtěte si téma [*Postup: Správa digitálních vláken*](how-to-manage-twin.md).

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Vytvoření relací

Vztahy popisují, jak jsou vzájemně propojeny různé digitální vazby, které tvoří základ pro dvojitou graf.

Vztahy se vytvářejí pomocí `CreateRelationship` volání. 

Chcete-li vytvořit relaci, je nutné zadat následující:
* Zdrojové ID vlákna (vlákna, kde relace vznikla)
* Cílové ID vlákna (zdvojené, kde relace dorazí)
* Název vztahu
* ID vztahu

ID vztahu musí být jedinečné v rámci daného zdroje vlákna. Nemusí být globálně jedinečný.
Například pro zdvojenou *foo*musí být každé konkrétní ID vztahu jedinečné. Nicméně jiný ovládací prvek s dvojitou *čárkou* může mít odchozí vztah, který odpovídá stejnému ID relace *foo* . 

Následující ukázka kódu ukazuje, jak přidat relaci do instance digitálního vlákna Azure.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Další informace o pomocné třídě `BasicRelationship` naleznete v tématu [*How to: use the Azure Digital revlákens API and SDK*](how-to-use-apis-sdks.md).

### <a name="create-multiple-relationships-between-twins"></a>Vytvoření více vztahů mezi dvojitými hodnotami

Neexistuje žádné omezení počtu vztahů, které můžete mít mezi dvěma dvojitými vláknami – můžete mít tolik vztahů mezi dvojitými možnostmi, jak budete chtít. 

To znamená, že můžete vyjádřit několik různých typů vztahů mezi dvěma dvojitými vlákna najednou. Například *Dvojitá* a může mít jak *uloženou* *relaci, tak* i vytvářený vztah s *dvojitým B*.

V případě potřeby můžete dokonce vytvořit několik instancí stejného typu relace mezi dvěma dvěma typy vláken. V tomto příkladu to znamená, že *zdvojená A* může mít dvě jedinečné *uložené* relace s *dvojitým B*.

## <a name="list-relationships"></a>Výpis relací

Chcete-li získat přístup k seznamu **odchozích** relací přicházejících z daného vlákna v grafu, můžete použít:

```csharp
await client.GetRelationshipsAsync(id);
```

Vrátí `Azure.Pageable<T>` nebo `Azure.AsyncPageable<T>` , v závislosti na tom, zda používáte synchronní nebo asynchronní verzi volání.

Tady je úplný příklad, který načte seznam vztahů:

```csharp
public async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw if an error occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);
        List<BasicRelationship> results = new List<BasicRelationship>();
        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            results.Add(rel);
        }
        return results;
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
        return null;
    }
}
```

Načtené relace můžete použít k přechodu na další vlákna v grafu. Provedete to tak, že si přečtete `target` pole z vráceného vztahu a použijete ho jako ID pro vaše další volání `GetDigitalTwin` . 

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Najít příchozí relace k digitálnímu vlákna

Digitální vlákna Azure také obsahuje rozhraní API pro vyhledání všech **příchozích** vztahů k danému vlákna. To je často užitečné pro zpětnou navigaci nebo při odstraňování vlákna.

Předchozí ukázka kódu se zaměřuje na hledání odchozích relací z vlákna. Následující příklad je strukturován podobně, ale místo toho najde *příchozí* relace na vlákna.

Všimněte si, že `IncomingRelationship` volání nevrátí úplný text vztahu.

```csharp
async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        List<IncomingRelationship> results = new List<IncomingRelationship>();
        await foreach (IncomingRelationship incomingRel in incomingRels)
            results.Add(incomingRel);
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

## <a name="delete-relationships"></a>Odstranit relace

Relace můžete odstranit pomocí `DeleteRelationship(source, relId);` .

První parametr určuje zdrojovou nevlákennou (zdvojené místo, kde relace vznikla). Druhým parametrem je ID vztahu. Potřebujete jak zdvojené ID, tak ID vztahu, protože identifikátory relací jsou jedinečné pouze v rámci rozsahu vlákna.

## <a name="create-a-twin-graph"></a>Vytvoření vlákna s dvojitou křivkou 

Následující fragment kódu používá operace vztahu z tohoto článku k vytvoření cyklického grafu z digitálních vláken a vztahů.

```csharp
static async Task CreateTwins()
{
    // Create twins - see utility functions below 
    await CreateRoom("Room01", 68, 50, false, "");
    await CreateRoom("Room02", 70, 66, true, "EId-00124");
    await CreateFloorOrBuilding("Floor01", makeFloor:true);

    // Create relationships
    await AddRelationship("Floor01", "contains", "Floor-to-Room01", "Room01");
    await AddRelationship("Floor01", "contains", "Floor-to-Room02", "Room02");
}

static async Task<bool> AddRelationship(string source, string relationship, string id, string target)
{
    var relationship = new BasicRelationship
    {
        TargetId = target,
        Name = relationship
    };

    try
    {
        string relId = $"{source}-contains->{target}";
        await client.CreateRelationshipAsync(source, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
        return true;
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
        return false;
    }
}

static async Task<bool> CreateRoom(string id, double temperature, double humidity)
{
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = "dtmi:com:contoso:Room;2";
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("Temperature", temperature);
    props.Add("Humidity", humidity);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin)); 
        return true;       
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}

static async Task<bool> CreateFloorOrBuilding(string id, bool makeFloor=true)
{
    string type = "dtmi:com:contoso:Building;3";
    if (makeFloor==true)
        type = "dtmi:com:contoso:Floor;2";
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = type;
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("AverageTemperature", 0);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));  
        return true;      
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}
```

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Vytvoření vlákna s dvojitou křivkou z tabulky

V praktických případech použití budou zdvojené hierarchie často vytvořeny z dat uložených v jiné databázi nebo v tabulce. Tato část ukazuje, jak lze tabulku analyzovat.

Vezměte v úvahu následující tabulku dat s popisem sady digitálních vláken a relací, které se mají vytvořit.

| Model    | ID | Nadřazený | Název vztahu | Další data |
| --- | --- | --- | --- | --- |
| řízení    | Floor01 | | | … |
| konverzační    | Room10 | Floor01 | obsahuje | … |
| konverzační    | Room11 | Floor01 | obsahuje | … |
| konverzační    | Room12 | Floor01 | obsahuje | … |
| řízení    | Floor02 | | | … |
| konverzační    | Room21 | Floor02 | obsahuje | … |
| konverzační    | Room22 | Floor02 | obsahuje | … |

Následující kód používá [rozhraní Microsoft Graph API](https://docs.microsoft.com/graph/overview) ke čtení tabulky a k vytvoření grafu s dynamickými podmnožinami digitálních vláken Azure z výsledků.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string type = row[0].GetString();
    string id = row[1].GetString();
    string relSource = row[2].GetString();
    string relName = row[3].GetString();
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (relSource != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = relSource,
            TargetId = id,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    switch (type)
    {
        case "room":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Room;2" };
            break;
        case "floor":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Floor;2" };
            break;
        ... handle additional types
    }
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
        Log.Error($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            client.CreateRelationship(rec.SourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Log.Error($"Error {e.Status}: {e.Message}");
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