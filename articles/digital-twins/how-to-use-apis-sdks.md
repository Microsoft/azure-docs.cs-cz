---
title: Použití rozhraní API a sad SDK služby Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Podívejte se, jak pracovat s rozhraními API digitálních vláken Azure, včetně přes SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: cac0d8cb8a910b735454c9270060364cab2db5fb
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187234"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Použití rozhraní API a sad SDK služby Azure Digital Twins

Digitální vlákna Azure jsou vybavená **rozhraními API řídicí plochy** a **rozhraním API roviny dat** pro správu vaší instance a jejích prvků. 
* Rozhraní API plochy ovládacího prvku jsou [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) rozhraní API a zahrnují operace správy prostředků, jako je vytváření a odstraňování vaší instance. 
* Rozhraní API roviny dat jsou rozhraní API digitálních vláken Azure, která se používají pro operace správy dat, jako je Správa modelů, vláken a grafu.

Tento článek poskytuje přehled dostupných rozhraní API a metody, jak s nimi interaktivně pracovat. Rozhraní REST API můžete buď použít přímo spolu s jejich přidruženými Swagger (prostřednictvím nástroje jako je [post](how-to-use-postman.md)), nebo prostřednictvím sady SDK.

## <a name="overview-control-plane-apis"></a>Přehled: Řídicí rozhraní API roviny

Rozhraní API plochy ovládacího prvku jsou rozhraní API [ARM](../azure-resource-manager/management/overview.md) , která slouží ke správě vaší instance digitálního vlákna Azure jako celku, takže pokrývá operace, jako je vytváření nebo odstraňování celé instance. Tyto koncové body budete také používat k vytváření a odstraňování koncových bodů.

Nejaktuálnější verze rozhraní API roviny ovládacího prvku je _**2020-10-31**_.

Chcete-li použít rozhraní API plochy ovládacího prvku:
* Rozhraní API můžete volat přímo odkazem na nejnovější Swagger ve [složce Swagger řídicí roviny](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins). Toto úložiště obsahuje také složku příkladů, které ukazují použití.
* V tuto chvíli můžete přistupovat k sadám SDK pro řídicí rozhraní API v...
  - [**.NET (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([odkaz [automaticky generované]](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)) ([zdroj](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/artifact/com.microsoft.azure.digitaltwins.v2020_10_31/azure-mgmt-digitaltwins/1.0.0/jar) ([odkaz [automaticky generovaný]](/java/api/overview/azure/digitaltwins?view=azure-java-stable&preserve-view=true)) ([zdroj](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/mgmt-v2020_10_31))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([zdroj](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([zdroj](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Přejít**](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt/2020-10-31/digitaltwins) ([zdroj](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt/2020-10-31/digitaltwins))

Rozhraní API rovin ovládacích prvků můžete také využít k interakci s digitálními podčinnostmi Azure pomocí [Azure Portal](https://portal.azure.com) a [CLI](how-to-use-cli.md).

## <a name="overview-data-plane-apis"></a>Přehled: rozhraní API roviny dat

Rozhraní API roviny dat představují rozhraní API digitálních vláken Azure, která slouží ke správě prvků v instanci digitálních vláken Azure. Zahrnují operace, jako je vytváření tras, nahrávání modelů, vytváření relací a správu vláken. Můžou být v podstatě rozdělené do následujících kategorií:
* **DigitalTwinModels** – kategorie DigitalTwinModels obsahuje rozhraní API pro správu [modelů](concepts-models.md) v instanci digitálních vláken Azure. Mezi aktivity správy patří nahrávání, ověřování, načítání a odstraňování modelů, které jsou vytvořené v DTDL.
* **DigitalTwins** – kategorie DigitalTwins obsahuje rozhraní API, která vývojářům umožňují vytvářet, upravovat a odstraňovat [digitální vlákna](concepts-twins-graph.md) a jejich vztahy v instanci digitálních vláken Azure.
* **Dotaz** – kategorie dotazu umožňuje vývojářům [Najít sady digitálních vláken ve dvojitých grafech](how-to-query-graph.md) napříč relacemi.
* **Směrování událostí** – kategorie trasy událostí obsahuje rozhraní API pro [směrování dat](concepts-route-events.md)prostřednictvím systému a služeb pro příjem dat.

Nejaktuálnější verze rozhraní API roviny dat je _**2020-10-31**_.

Použití rozhraní API roviny dat:
* Můžete volat rozhraní API přímo, do...
   - odkaz na nejnovější Swagger ve [složce Swagger datové roviny](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Toto úložiště obsahuje také složku příkladů, které ukazují použití. 
   - zobrazení [Referenční dokumentace rozhraní API](/rest/api/azure-digitaltwins/).
* Můžete použít **sadu .NET (C#) SDK**. Chcete-li použít sadu .NET SDK...
   - Balíček můžete zobrazit a přidat ze sady NuGet: [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - Můžete si prohlédnout [referenční dokumentaci k sadě SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).
   - zdroj sady SDK, včetně složky ukázek, najdete na webu GitHub: [Klientská knihovna Azure IoT s Nevlákenou pro .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - Podrobné informace a příklady použití můžete zobrazit tak, že v tomto článku budete pokračovat v části [*.NET (C#) SDK (rovina dat)*](#net-c-sdk-data-plane) .
* Můžete použít **sadu Java SDK**. Použití sady Java SDK...
   - Balíček můžete zobrazit a nainstalovat z Maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - můžete zobrazit [referenční dokumentaci k sadě SDK](/java/api/overview/azure/digitaltwins/client?preserve-view=true&view=azure-java-stable) .
   - zdroj sady SDK najdete na webu GitHub: [Klientská knihovna Azure IoT je nevlákenná pro jazyk Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Můžete použít **sadu JavaScript SDK**. Použití sady JavaScript SDK...
   - balíček si můžete zobrazit a nainstalovat z npm: [Azure Azure Digital nezákladní Klientská knihovna pro JavaScript](https://www.npmjs.com/package/@azure/digital-twins-core).
   - Můžete si prohlédnout [referenční dokumentaci k sadě SDK](/javascript/api/@azure/digital-twins-core/?branch=master&view=azure-node-latest&preserve-view=true).
   - zdroj sady SDK můžete najít na webu GitHub: [Azure Azure Digital nezákladní Klientská knihovna pro JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* Můžete použít **sadu Python SDK**. Chcete-li použít sadu Python SDK...
   - Balíček můžete zobrazit a nainstalovat z PyPi: [Azure Azure Digital Core Client Library for Python](https://pypi.org/project/azure-digitaltwins-core/).
   - Můžete si prohlédnout [referenční dokumentaci k sadě SDK](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true).
   - zdroj sady SDK můžete najít na webu GitHub: [Azure Azure Digital Core Client Library for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Můžete vygenerovat sadu SDK pro jiný jazyk pomocí programu AutoRest. Postupujte podle pokynů v tématu [*Postupy: vytváření vlastních sad SDK pro digitální vlákna Azure pomocí automatického REST*](how-to-create-custom-sdks.md).

Rozhraní API roviny dat můžete také využít k interakci s digitálními podčinnostmi Azure prostřednictvím rozhraní příkazového [řádku](how-to-use-cli.md).

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (rovina dat)

Sada Azure Digital revlákens .NET (C#) SDK je součástí sady Azure SDK for .NET. Je to open source a je založený na rozhraních API roviny dat digitálních vláken Azure.

> [!NOTE]
> Další informace o návrhu sady SDK najdete v tématu Obecné [Principy návrhu pro sadu Azure SDK](https://azure.github.io/azure-sdk/general_introduction.html) a konkrétní [pokyny pro návrh .NET](https://azure.github.io/azure-sdk/dotnet_introduction.html).

Pokud chcete použít sadu SDK, zahrňte do svého projektu balíček NuGet **Azure. DigitalTwins. Core** . Budete také potřebovat nejnovější verzi balíčku **Azure. identity** .

* V aplikaci Visual Studio můžete přidat balíčky pomocí Správce balíčků NuGet (k dispozici prostřednictvím *nástrojů > správce balíčků nuget > spravovat balíčky NuGet pro řešení*). 
* Pomocí nástroje pro příkazový řádek .NET můžete spustit:

    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Podrobný návod k používání rozhraní API v praxi najdete v tématu [*kurz: Code a klientská aplikace*](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>Příklady použití sady .NET SDK

Zde je několik ukázek kódu ilustrující použití sady .NET SDK.

Ověřování proti službě:

```csharp
// Authenticate against the service and create a client
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credential = new DefaultAzureCredential();
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
```

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Nahrajte modely modelů a seznamů:

```csharp
// Upload a model
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
// Read a list of models back from the service
AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
await foreach (DigitalTwinsModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Vytváření a dotazování na vlákna:

```csharp
// Initialize twin metadata
BasicDigitalTwin updateTwinData = new BasicDigitalTwin();

twinData.Id = $"firstTwin";
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.Contents.Add("data", "Hello World!");
try {
    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("firstTwin", updateTwinData);
} catch(RequestFailedException rex) {
    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
}
 
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    // Use JSON deserialization to pretty-print
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    // Or use BasicDigitalTwin for convenient property access
    BasicDigitalTwin btwin = JsonSerializer.Deserialize<BasicDigitalTwin>(twin);
}
```

Projděte si kurz tohoto ukázkového kódu aplikace a podívejte se na [*kurz: vytvoření klientské aplikace*](tutorial-code.md) pro návod. 

Další ukázky můžete najít také v [úložišti GitHub pro sadu .NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples).

#### <a name="serialization-helpers"></a>Pomocníka serializace

Pomocné rutiny serializace jsou pomocné funkce dostupné v sadě SDK pro rychlé vytvoření nebo deserializaci dvojitě dostupných dat pro přístup k základním informacím. Vzhledem k tomu, že základní metody sady SDK vracejí ve výchozím nastavení ve výchozím nastavení hodnoty ve formátu JSON, může být užitečné tyto pomocné třídy použít k dalšímu přerušení dat.

Dostupné pomocné třídy jsou:
* `BasicDigitalTwin`: Představuje základní data digitálního vlákna.
* `BasicRelationship`: Představuje základní data relace.
* `UpdateOperationUtility`: Představuje informace o opravě JSON používané v voláních aktualizace.
* `WriteableProperty`: Představuje metadata vlastnosti.

##### <a name="deserialize-a-digital-twin"></a>Deserializace digitálního vlákna

Vlákna můžete vždy deserializovat pomocí knihovny JSON dle vašeho výběru, například `System.Test.Json` nebo `Newtonsoft.Json` . Pro základní přístup k vytvářené pomocné třídy je tento bit pohodlnější.

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_id);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
```

`BasicDigitalTwin`Pomocná třída také poskytuje přístup k vlastnostem, které jsou definovány na vlákna, prostřednictvím `Dictionary<string, object>` . K vypsání vlastností vlákna můžete použít:

```csharp
Response<BasicDigitalTwin> twin = client.GetDigitalTwin(twin_id);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.Contents.Keys)
{
    if (twin.Contents.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-digital-twin"></a>Vytvoření digitálního vlákna

Pomocí `BasicDigitalTwin` třídy můžete připravit data pro vytvoření zdvojené instance:

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
twin.Contents = props;

client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myNewRoomID", twin);
```

Výše uvedený kód je ekvivalentní následujícímu "ručnímu" variantě:

```csharp
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example:Room;1"}
};
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", 25.0 }
};
client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>("myNewRoomID", twin);
```

##### <a name="deserialize-a-relationship"></a>Deserializace vztahu

Data relace můžete vždy deserializovat podle zvoleného typu. Pro základní přístup k relaci použijte typ `BasicRelationship` .

```csharp
BasicRelationship res = client.GetRelationship<BasicRelationship>(twin_id, rel_id);
Console.WriteLine($"Relationship Name: {rel.Name}");
```

`BasicRelationship`Pomocná třída také poskytuje přístup k vlastnostem, které jsou definovány v relaci, prostřednictvím `IDictionary<string, object>` . K vypsání vlastností můžete použít:

```csharp
BasicRelationship res = client.GetRelationship<BasicRelationship>(twin_id, rel_id);
Console.WriteLine($"Relationship Name: {rel.Name}");
foreach (string prop in rel.Contents.Keys)
{
    if (twin.Contents.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-relationship"></a>Vytvoření relace

Pomocí `BasicRelationship` třídy můžete také připravit data pro vytváření relací na instance s dvojitou platností:

```csharp
BasicRelationship rel = new BasicRelationship();
rel.TargetId = "myTargetTwin";
rel.Name = "contains"; // a relationship with this name must be defined in the model
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("active", true);
rel.Properties = props;
client.CreateOrReplaceRelationshipAsync("mySourceTwin", "rel001", rel);
```

##### <a name="create-a-patch-for-twin-update"></a>Vytvoření opravy pro dvojitou aktualizaci

Aktualizace volání pro vlákna a relace používají strukturu [opravy JSON](http://jsonpatch.com/) . Chcete-li vytvořit seznamy operací opravy JSON, můžete použít, `JsonPatchDocument` jak je uvedeno níže.

```csharp
var updateTwinData = new JsonPatchDocument();
updateTwinData.AppendAddOp("/Temperature", 25.0);
updateTwinData.AppendAddOp("/myComponent/Property", "Hello");
// Un-set a property
updateTwinData.AppendRemoveOp("/Humidity");

client.UpdateDigitalTwin("myTwin", updateTwinData);
```

## <a name="general-apisdk-usage-notes"></a>Obecné poznámky k používání rozhraní API/sady SDK

> [!NOTE]
> Upozorňujeme, že digitální vlákna Azure v současné době nepodporuje **sdílení prostředků mezi zdroji (CORS)**. Další informace o strategiích dopadů a rozlišení najdete v části věnované [*sdílení prostředků mezi zdroji (CORS)*](concepts-security.md#cross-origin-resource-sharing-cors) v tématu *Koncepty: Security for Azure Digital* Solutions.

Následující seznam poskytuje další podrobnosti a obecné pokyny pro používání rozhraní API a sad SDK.

* K přímému volání rozhraní API digitálních vláken Azure můžete použít nástroj pro testování REST HTTP, jako je například post. Další informace o tomto procesu naleznete v tématu [*How to: Make a requests with post*](how-to-use-postman.md).
* Chcete-li použít sadu SDK, vytvořte instanci `DigitalTwinsClient` třídy. Konstruktor vyžaduje přihlašovací údaje, které lze získat pomocí různých metod ověřování v `Azure.Identity` balíčku. Další `Azure.Identity` informace najdete v [dokumentaci k jejímu oboru názvů](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet). 
* `InteractiveBrowserCredential`Při zahájení práce může být užitečné, ale k dispozici je několik dalších možností, včetně přihlašovacích údajů pro [spravovanou identitu](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet), které pravděpodobně použijete k ověření služby [Azure Functions se službou MSI](../app-service/overview-managed-identity.md?tabs=dotnet) proti digitálním vazbám Azure. Další informace o naleznete `InteractiveBrowserCredential` v [dokumentaci třídy](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet).
* Všechna volání rozhraní API služby jsou vystavena jako členské funkce `DigitalTwinsClient` třídy.
* Všechny funkce služeb existují v synchronních a asynchronních verzích.
* Všechny funkce služby vyvolávají výjimku pro libovolný návratový stav 400 nebo vyšší. Ujistěte se, že zabalíte volání do `try` oddílu a zachytíte alespoň `RequestFailedExceptions` . Další informace o tomto typu výjimky najdete [zde](/dotnet/api/azure.requestfailedexception?preserve-view=true&view=azure-dotnet).
* Většina metod služeb vrací `Response<T>` nebo ( `Task<Response<T>>` pro asynchronní volání), kde `T` je třída návratového objektu pro volání služby. [`Response`](/dotnet/api/azure.response-1?preserve-view=true&view=azure-dotnet)Třída zapouzdřuje vrácení služby a prezentuje návratové hodnoty ve svém `Value` poli.  
* Metody služby s stránkovanými výsledky vrátí `Pageable<T>` nebo `AsyncPageable<T>` jako výsledky. Další informace o `Pageable<T>` třídě naleznete [zde](/dotnet/api/azure.pageable-1?preserve-view=true&view=azure-dotnet-preview). Další informace naleznete `AsyncPageable<T>` [zde](/dotnet/api/azure.asyncpageable-1?preserve-view=true&view=azure-dotnet-preview).
* Můžete iterovat přes stránkované výsledky pomocí `await foreach` smyčky. Další informace o tomto procesu najdete [tady](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8).
* Základní sada SDK je `Azure.Core` . Referenční informace o infrastruktuře a typech SDK najdete v [dokumentaci k oboru názvů Azure](/dotnet/api/azure?preserve-view=true&view=azure-dotnet-preview) .

Metody služby vracejí objekty silného typu, pokud je to možné. Vzhledem k tomu, že digitální vlákna Azure je založené na modelech, které uživatel nakonfiguroval za běhu (prostřednictvím DTDL modelů odeslaných do služby), mnoho rozhraní API služby převezme a vrátí data ve formátu JSON.

## <a name="monitor-api-metrics"></a>Monitorovat metriky rozhraní API

Metriky API, jako jsou požadavky, latence a frekvence selhání, se dají zobrazit v [Azure Portal](https://portal.azure.com/). 

Na domovské stránce portálu vyhledejte vaši instanci digitálních vláken Azure, abyste si vyčetli její podrobnosti. Výběrem možnosti **metriky** v nabídce instance digitálních vláken Azure zobrazíte stránku *metriky* .

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Snímek obrazovky zobrazující stránku s metrikami pro digitální vlákna Azure":::

Tady můžete zobrazit metriky pro vaši instanci a vytvořit vlastní zobrazení.

## <a name="next-steps"></a>Další kroky

Přečtěte si téma jak vytvořit přímé požadavky na rozhraní API pomocí metody post:
* [*Postupy: Vytváření požadavků s použitím metody post*](how-to-use-postman.md)

Nebo je vhodné použít sadu .NET SDK vytvořením klientské aplikace pomocí tohoto kurzu:
* [*Kurz: vytvoření kódu klientské aplikace*](tutorial-code.md)