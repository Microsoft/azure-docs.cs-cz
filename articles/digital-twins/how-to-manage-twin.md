---
title: Správa digitálního dvojčete
titleSuffix: Azure Digital Twins
description: Podívejte se, jak načíst, aktualizovat a odstranit jednotlivé vlákna a relace.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 9acea128313fc440834626dd3a99b117e4b25a8f
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84735893"
---
# <a name="manage-digital-twins"></a>Správa digitálních vláken

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Entity ve vašem prostředí jsou reprezentovány pomocí [digitálních vláken](concepts-twins-graph.md). Správa digitálních vláken může zahrnovat vytváření, úpravy a odebírání. K provedení těchto operací můžete použít [**rozhraní API DigitalTwins**](how-to-use-apis-sdks.md), [sadu .NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)nebo rozhraní příkazového [řádku Azure Digital vlákens](how-to-use-cli.md).

Tento článek se zaměřuje na správu digitálních vláken; Chcete-li pracovat se vztahy a s [dvojitým grafem](concepts-twins-graph.md) jako celku, přečtěte si téma [How to: manage a Graph with Relationships](how-to-manage-graph.md).

> [!TIP]
> Všechny funkce sady SDK přicházejí v synchronních a asynchronních verzích.

## <a name="create-a-digital-twin"></a>Vytvoření digitálního vlákna

Chcete-li vytvořit dvojitou hodnotu, použijte `CreateDigitalTwin` metodu na klientovi služby, například:

```csharp
await client.CreateDigitalTwinAsync("myNewTwinID", initData);
```

K vytvoření digitálního vlákna musíte zadat:
* Požadované ID digitálního vlákna
* [Model](concepts-models.md) , který chcete použít 

Volitelně můžete zadat počáteční hodnoty pro všechny vlastnosti digitálního vlákna. 

> [!TIP]
> Po načtení vlákna s GetDigitalTwin jsou vráceny pouze vlastnosti, které byly nastaveny alespoň jednou.  

Hodnoty modelů a počátečních vlastností jsou k dispozici prostřednictvím `initData` parametru, což je řetězec JSON obsahující relevantní data.

### <a name="initialize-properties"></a>Inicializovat vlastnosti

Rozhraní API pro vytvoření vlákna přijímá objekt, který lze serializovat na platný popis JSON vlastností. V tématu [Koncepty: digitální vlákna a Dvojitá graf](concepts-twins-graph.md) pro Popis formátu JSON pro dvojitou hodnotu.

Můžete vytvořit objekt parametru buď ručně, nebo pomocí poskytnuté pomocné třídy. Tady je příklad každé z nich.

#### <a name="create-twins-using-manually-created-data"></a>Vytváření dvojitých vláken pomocí ručně vytvořených dat

```csharp
// Define the model type for the twin to be created
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:com:contoso:Room;1" }
};
// Initialize the twin properties
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", temperature},
    { "Humidity", humidity},
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

#### <a name="create-twins-with-the-helper-class"></a>Vytvoření vláken s podpůrnou třídou

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

## <a name="get-data-for-a-digital-twin"></a>Získání dat z digitálního vlákna

Můžete získat přístup k plným datům libovolného digitálního vlákna voláním:

```csharp
object result = await client.GetDigitalTwin(id);
```

Toto volání vrátí jako řetězec JSON dvojitá data. 

Chcete-li načíst více vláken pomocí jediného volání rozhraní API, přečtěte si příklady rozhraní API pro dotazování v tématu [Postupy: vytvoření dotazu na nevlákenný graf](how-to-query-graph.md).

Vezměte v úvahu následující model (napsaný v [digitálním DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL), který definuje *měsíc*:

```json
{
    "@id": " dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "contents": [
        {
            "@type": "Property",
            "name": "radius",
            "schema": "double",
            "writable": true
        },
        {
            "@type": "Property",
            "name": "mass",
            "schema": "double",
            "writable": true
        }
    ]
}
```

Výsledek volání `object result = await client.DigitalTwins.GetByIdAsync("my-moon");` na vlákna typu *měsíc*může vypadat takto:

```json
{
  "$dtId": "myMoon-001",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:com:contoso:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

Definované vlastnosti digitálního vlákna jsou vráceny jako vlastnosti nejvyšší úrovně u digitálního vlákna. Metadata nebo systémové informace, které nejsou součástí definice DTDL, se vrátí s `$` předponou. Mezi vlastnosti metadat patří:
* ID digitálního vlákna v této instanci digitálních vláken Azure, jako je `$dtId` .
* Další vlastnosti v `$metadata` oddílu. To zahrnuje:
    - DTMI modelu digitálního vlákna.
    - Stav synchronizace pro každou zapisovatelnou vlastnost. To je nejužitečnější pro zařízení, kde je možné, že služba a zařízení mají Rozbíhající se stavy (například když je zařízení offline). V současné době se tato vlastnost vztahuje pouze na fyzická zařízení připojená k IoT Hub. S daty v části metadata je možné pochopit úplný stav vlastnosti a také poslední změněná časová razítka. Další informace o stavu synchronizace najdete v [tomto IoT Hub kurzu](../iot-hub/tutorial-device-twins.md) synchronizace stavu zařízení.
    - Metadata specifická pro službu, například z IoT Hub nebo z digitálních vláken Azure. 

Můžete analyzovat vrácený formát JSON pro vlákna pomocí knihovny analýzy JSON dle vašeho výběru, například `System.Text.Json` .

Můžete také použít třídu pomocníka serializace `BasicDigitalTwin` , která je součástí sady SDK, což vrátí základní a vlastnosti ve formě předem analyzovaných formulářů. Tady je příklad:

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

Další informace o pomocných třídách serializace najdete v tématu [Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure](how-to-use-apis-sdks.md).

## <a name="update-a-digital-twin"></a>Aktualizace digitálního vlákna

Chcete-li aktualizovat vlastnosti digitálního vlákna, zapište informace, které chcete nahradit ve formátu [opravy JSON](http://jsonpatch.com/) . Tímto způsobem můžete nahradit více vlastností najednou. Pak předáte dokument opravy JSON do `Update` metody:

`await client.UpdateDigitalTwin(id, patch);`.

Tady je příklad kódu opravy JSON. Tento dokument nahrazuje hodnoty *mass* vlastností Replace a *poloměru* digitálního vlákna, na které se aplikuje.

```json
[
  {
    "op": "replace",
    "path": "/mass",
    "value": 0.0799
  },
  {
    "op": "replace",
    "path": "/radius",
    "value": 0.800
  }
]
```

Můžete ručně vytvořit opravy nebo pomocí pomocné třídy serializace v [sadě SDK](how-to-use-apis-sdks.md). Tady je příklad každé z nich.

#### <a name="create-patches-manually"></a>Ruční vytvoření oprav
```csharp
List<object> twinData = new List<object>();
twinData.Add(new Dictionary<string, object>() {
    { "op", "add"},
    { "path", "/Temperature"},
    { "value", 25.0}
});

await client.UpdateDigitalTwinAsync(twinId, JsonConvert.SerializeObject(twinData));
```

#### <a name="create-patches-using-the-helper-class"></a>Vytváření oprav pomocí pomocné třídy

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
```

### <a name="update-properties-in-digital-twin-components"></a>Aktualizovat vlastnosti v digitálních nevlákenných komponentách

Odvolání, že model může obsahovat komponenty a umožňuje, aby byl vytvořen z dalších modelů. 

Chcete-li opravit vlastnosti v komponentách digitálního vlákna, budete používat syntaxi cesty v rámci opravy JSON:

```json
[
  {
    "op": "replace",
    "path": "/mycomponentname/mass",
    "value": 0.0799
  }
]
```

### <a name="update-a-digital-twins-model"></a>Aktualizace modelu digitálního vlákna

`Update`Funkci lze také použít k migraci digitálního vlákna na jiný model. 

Zvažte například následující dokument opravy JSON, který nahrazuje pole metadat digitálního vlákna `$model` :

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:contoso:foo;1"
  }
]
```

Tato operace proběhne úspěšně pouze v případě, že digitální práce, která je upravována opravou, bude v souladu s novým modelem. 

Uvažujte následující příklad:
1. Představte si digitální dvojitou hodnotu s modelem *foo_old*. *foo_old* definuje požadovanou *hmotnost*vlastnosti.
2. Nový model *foo_new* definuje hmotnostní vlastnost a přidává novou *teplotu*požadované vlastnosti.
3. Po opravě musí mít digitální vlákna současně vlastnost pro hmotnost i teplotu. 

Oprava pro tuto situaci musí aktualizovat model i vlastnost teploty vlákna, například takto:

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:com:contoso:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

## <a name="delete-a-digital-twin"></a>Odstranění digitálního vlákna

Můžete odstranit vlákna pomocí `DeleteDigitalTwin(ID)` . Můžete však odstranit pouze dvojitou hodnotu, pokud nemá žádné další relace. Nejprve musíte odstranit všechny relace. 

Zde je příklad kódu pro:

```csharp
static async Task DeleteTwin(string id)
{
    await FindAndDeleteOutgoingRelationshipsAsync(id);
    await FindAndDeleteIncomingRelationshipsAsync(id);
    try
    {
        await client.DeleteDigitalTwin(id);
    } catch (RequestFailedException exc)
    {
        Console.WriteLine($"*** Error:{exc.Error}/{exc.Message}");
    }
}

public async Task FindAndDeleteOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);

        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipssAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

### <a name="delete-all-digital-twins"></a>Odstranit všechny digitální vlákna

Příklad, jak odstranit všechny vlákna najednou, si můžete stáhnout ukázkovou aplikaci používanou v tomto [kurzu: Prozkoumejte základy s ukázkovou klientskou aplikací](tutorial-command-line-app.md). Soubor *CommandLoop.cs* to dělá ve `CommandDeleteAllTwins` funkci.

## <a name="manage-twins-with-cli"></a>Správa dvojitých vláken pomocí rozhraní příkazového řádku

Vlákna je také možné spravovat pomocí rozhraní příkazového řádku Azure Digital revlákens CLI. Příkazy najdete v tématu [Postupy: použití rozhraní příkazového řádku Azure Digital zdvojené](how-to-use-cli.md).

## <a name="next-steps"></a>Další kroky

Podívejte se, jak vytvořit a spravovat vztahy mezi vašimi digitálními dvojitými hodnotami:
* [Postupy: Správa zdvojeného grafu s relacemi](how-to-manage-graph.md)