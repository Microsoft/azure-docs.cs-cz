---
title: Správa digitálních dvojčat
titleSuffix: Azure Digital Twins
description: Podívejte se, jak načíst, aktualizovat a odstranit jednotlivé vlákna a relace.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3f9064c25581523167918b84a2d0027747e32bd9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91282370"
---
# <a name="manage-digital-twins"></a>Správa digitálních dvojčat

Entity ve vašem prostředí jsou reprezentovány pomocí [digitálních vláken](concepts-twins-graph.md). Správa digitálních vláken může zahrnovat vytváření, úpravy a odebírání. K provedení těchto operací můžete použít [**rozhraní API DigitalTwins**](how-to-use-apis-sdks.md), [sadu .NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)nebo rozhraní příkazového [řádku Azure Digital vlákens](how-to-use-cli.md).

Tento článek se zaměřuje na správu digitálních vláken; Chcete-li pracovat se vztahy a s [dvojitým grafem](concepts-twins-graph.md) v podobě celku, přečtěte si téma [*Postup: Správa nefunkčního grafu s relacemi*](how-to-manage-graph.md).

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

Hodnoty modelů a počátečních vlastností jsou k dispozici prostřednictvím `initData` parametru, což je řetězec JSON obsahující relevantní data. Další informace o strukturování tohoto objektu získáte, když budete pokračovat k další části.

> [!TIP]
> Po vytvoření nebo aktualizaci vlákna může být latence až 10 sekund, než se změny projeví v [dotazech](how-to-query-graph.md). `GetDigitalTwin`Rozhraní API (popsané [dále v tomto článku) v](#get-data-for-a-digital-twin)této prodlevě nefunguje, proto použijte volání rozhraní API namísto dotazování, abyste viděli nově vytvořená vlákna, pokud potřebujete okamžitou reakci. 

### <a name="initialize-model-and-properties"></a>Inicializace modelu a vlastností

Rozhraní API pro vytvoření vlákna přijímá objekt, který je serializován do platného popisu JSON vlastností. V tématu [*Koncepty: digitální vlákna a Dvojitá graf*](concepts-twins-graph.md) pro Popis formátu JSON pro dvojitou hodnotu. 

Nejprve vytvoříte datový objekt, který bude představovat vlákna a data vlastností. Pak můžete použít `JsonSerializer` k předání serializované verze do volání rozhraní API pro `initdata` parametr.

Můžete vytvořit objekt parametru buď ručně, nebo pomocí poskytnuté pomocné třídy. Tady je příklad každé z nich.

#### <a name="create-twins-using-manually-created-data"></a>Vytváření dvojitých vláken pomocí ručně vytvořených dat

Bez použití vlastních pomocných tříd můžete reprezentovat vlastnosti vlákna v `Dictionary<string, object>` , kde `string` je název vlastnosti a `object` je objekt představující vlastnost a její hodnotu.

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

Pomocná třída `BasicDigitalTwin` umožňuje ukládat pole vlastností do "" vlákna "objektu více přímo. Přesto můžete chtít sestavit seznam vlastností pomocí `Dictionary<string, object>` , který lze následně přidat k dodanému objektu jako `CustomProperties` přímo.

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

>[!NOTE]
> `BasicDigitalTwin` objekty jsou dodávány s `Id` polem. Toto pole můžete nechat prázdné, ale pokud přidáte hodnotu ID, musí se shodovat s parametrem ID předaným `CreateDigitalTwin` volání. Pro výše uvedený příklad by to vypadalo takto:
>
>```csharp
>twin.Id = "myNewRoomID";
>```

## <a name="get-data-for-a-digital-twin"></a>Získání dat z digitálního vlákna

Můžete získat přístup k plným datům libovolného digitálního vlákna voláním:

```csharp
object result = await client.GetDigitalTwin(id);
```

Toto volání vrátí jako řetězec JSON dvojitá data. 

Po načtení vlákna se vrátí pouze vlastnosti, které byly nastaveny alespoň jednou `GetDigitalTwin` .

>[!TIP]
>`displayName`Pro objekt, který je pro vlákna, je součástí metadat modelu, takže se při získávání dat pro dvojitou instanci nebude zobrazovat. Chcete-li zobrazit tuto hodnotu, můžete [ji načíst z modelu](how-to-manage-model.md#retrieve-models).

Chcete-li načíst více vláken pomocí jediného volání rozhraní API, přečtěte si příklady rozhraní API pro dotazování v tématu [*Postupy: vytvoření dotazu na nevlákenný graf*](how-to-query-graph.md).

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
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
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
* `$etag`, standardní pole HTTP přiřazené webovým serverem
* Další vlastnosti v `$metadata` oddílu. Tady jsou některé z nich:
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

Další informace o pomocných třídách serializace najdete v tématu [*Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure*](how-to-use-apis-sdks.md).

## <a name="update-a-digital-twin"></a>Aktualizace digitálního vlákna

Chcete-li aktualizovat vlastnosti digitálního vlákna, zapište informace, které chcete nahradit ve formátu [opravy JSON](http://jsonpatch.com/) . Tímto způsobem můžete nahradit více vlastností najednou. Pak předáte dokument opravy JSON do `Update` metody:

```csharp
await client.UpdateDigitalTwin(id, patch);
```

Volání opravy může aktualizovat libovolný počet vlastností u jediného vlákna, jak byste chtěli (dokonce i u všech). Pokud potřebujete aktualizovat vlastnosti v rámci více vláken, budete potřebovat samostatné volání aktualizace pro každý z vláken.

> [!TIP]
> Po vytvoření nebo aktualizaci vlákna může být latence až 10 sekund, než se změny projeví v [dotazech](how-to-query-graph.md). `GetDigitalTwin`Rozhraní API (popsané [dříve v tomto článku) v](#get-data-for-a-digital-twin)této prodlevě nefunguje, proto použijte volání rozhraní API namísto dotazování, abyste viděli nově aktualizovaná vlákna, pokud potřebujete okamžitou reakci. 

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

### <a name="handle-conflicting-update-calls"></a>Zpracování konfliktních volání aktualizace

Digitální vlákna Azure zajišťuje, že se všechny příchozí žádosti zpracovávají jednou po druhém. To znamená, že i v případě, že se více funkcí pokusí aktualizovat stejnou vlastnost na vlákna ve stejnou dobu, není **nutné** psát explicitní kód uzamykání pro zpracování konfliktu.

Toto chování je na základě vlákna. 

Představte si například scénář, ve kterém tato tři volání dorazí ve stejnou dobu: 
*   Zapsat vlastnost A v *Twin1*
*   Zapsat vlastnost B v *Twin1*
*   Zapsat vlastnost A v *Twin2*

Dvě volání, která mění *Twin1* , se spustí jednou po druhém a při každé změně se vygenerují zprávy o změně. Volání úpravy *Twin2* může být spuštěno souběžně bez konfliktu, jakmile bude dodáno.

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

Příklad, jak odstranit všechny vlákna najednou, si můžete stáhnout ukázkovou aplikaci používanou v tomto [*kurzu: Prozkoumejte základy s ukázkovou klientskou aplikací*](tutorial-command-line-app.md). Soubor *CommandLoop.cs* to dělá ve `CommandDeleteAllTwins` funkci.

## <a name="manage-twins-with-cli"></a>Správa dvojitých vláken pomocí rozhraní příkazového řádku

Vlákna je také možné spravovat pomocí rozhraní příkazového řádku Azure Digital revlákens CLI. Příkazy najdete v tématu [*Postupy: použití rozhraní příkazového řádku Azure Digital zdvojené*](how-to-use-cli.md).

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="view-all-digital-twins"></a>Zobrazit všechny digitální vlákna

Chcete-li zobrazit všechny digitální vlákna ve vaší instanci, použijte [dotaz](how-to-query-graph.md). Dotaz můžete spustit pomocí [rozhraní API pro dotazy](how-to-use-apis-sdks.md) nebo [příkazů CLI](how-to-use-cli.md).

Zde je text základního dotazu, který vrátí seznam všech digitálních vláken v instanci:

```sql
SELECT *
FROM DIGITALTWINS
``` 

## <a name="next-steps"></a>Další kroky

Podívejte se, jak vytvořit a spravovat vztahy mezi vašimi digitálními dvojitými hodnotami:
* [*Postupy: Správa dvojitých grafů s relacemi*](how-to-manage-graph.md)