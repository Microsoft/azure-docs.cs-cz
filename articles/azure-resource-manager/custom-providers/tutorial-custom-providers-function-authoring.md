---
title: Vytvoření koncového bodu RESTful
description: Tento kurz ukazuje, jak vytvořit koncový bod RESTful pro vlastní zprostředkovatele. Podrobnosti o tom, jak zpracovat požadavky a odpovědi pro podporované metody HTTP RESTful.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7f6c51211ce0572797ade659b9316003502da1f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650020"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>Vytvoření koncového bodu RESTful pro vlastní zprostředkovatele

Vlastní zprostředkovatel je smlouva mezi Azure a koncovýbod. Pomocí vlastních poskytovatelů můžete přizpůsobit pracovní postupy v Azure. Tento kurz ukazuje, jak vytvořit vlastní koncový bod restful zprostředkovatele. Pokud nejste obeznámeni s vlastními poskytovateli Azure, podívejte se [na přehled o vlastních zprostředkovatelích prostředků](overview.md).

> [!NOTE]
> Tento kurz vychází z kurzu [Nastavení funkcí Azure pro vlastní zprostředkovatele Azure](./tutorial-custom-providers-function-setup.md). Některé kroky v tomto kurzu fungují pouze v případě, že aplikace funkce Azure byla nastavena pro práci s vlastními poskytovateli.

## <a name="work-with-custom-actions-and-custom-resources"></a>Práce s vlastními akcemi a vlastními zdroji

V tomto kurzu můžete aktualizovat aplikaci funkce tak, aby fungovala jako koncový bod RESTful pro vlastního poskytovatele. Prostředky a akce v Azure jsou modelovány podle následující základní specifikace RESTful:

- **PUT**: Vytvoření nového zdroje
- **GET (instance)**: Načtení existujícího prostředku
- **ODSTRANIT**: Odebrání existujícího prostředku
- **POST**: Spuštění akce
- **GET (kolekce)**: Seznam všech existujících zdrojů

 V tomto kurzu použijete úložiště Azure Table. Ale každá databáze nebo služba úložiště může fungovat.

## <a name="partition-custom-resources-in-storage"></a>Oddíl vlastní prostředky v úložišti

Vzhledem k tomu, že vytváříte službu RESTful, je třeba uložit vytvořené prostředky. Pro úložiště Azure Table je potřeba generovat klíče oddílů a řádků pro vaše data. Pro vlastní zprostředkovatele by měla být data rozdělena na vlastní zprostředkovatele. Při odeslání příchozí požadavek na vlastního zprostředkovatele, `x-ms-customproviders-requestpath` vlastní zprostředkovatel přidá záhlaví odchozí požadavky do koncového bodu.

Následující příklad ukazuje `x-ms-customproviders-requestpath` záhlaví vlastního prostředku:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Na základě hlavičky `x-ms-customproviders-requestpath` příkladu můžete vytvořit parametry *partitionKey* a *rowKey* pro úložiště, jak je znázorněno v následující tabulce:

Parametr | Šablona | Popis
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | Parametr *partitionKey* určuje způsob rozdělení dat na oddíly. Obvykle jsou data rozdělena na oddíly podle instance vlastního zprostředkovatele.
*řádekKlíč* | `{myResourceType}:{myResourceName}` | Parametr *rowKey* určuje individuální identifikátor dat. Identifikátor je obvykle název prostředku.

Je také nutné vytvořit novou třídu pro modelování vlastního prostředku. V tomto kurzu přidáte do aplikace funkce následující třídu **CustomResource:**

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** je jednoduchá, obecná třída, která přijímá všechna vstupní data. Je založen na **TableEntity**, který se používá k ukládání dat. Třída **CustomResource** zdědí dvě vlastnosti z **TableEntity**: **partitionKey** a **rowKey**.

## <a name="support-custom-provider-restful-methods"></a>Podpora vlastních metod RESTful zprostředkovatele

> [!NOTE]
> Pokud kód nekopírujete přímo z tohoto kurzu, musí být obsah odpovědi `Content-Type` platný `application/json`JSON, který nastaví záhlaví na .

Teď, když jste nastavili dělení dat, vytvořte základní metody CRUD a aktivační metody pro vlastní prostředky a vlastní akce. Vzhledem k tomu, že vlastní zprostředkovatelé fungují jako proxy servery, koncový bod RESTful musí modelovat a zpracovávat požadavek a odpověď. Následující fragmenty kódu ukazují, jak zpracovat základní operace RESTful.

### <a name="trigger-a-custom-action"></a>Spuštění vlastní akce

Pro vlastní zprostředkovatele vlastní akce se aktivuje prostřednictvím požadavků POST. Vlastní akce může volitelně přijmout tělo požadavku, který obsahuje sadu vstupních parametrů. Akce pak vrátí odpověď, která signalizuje výsledek akce a zda byla úspěšná nebo neúspěšná.

Přidejte do aplikace funkce následující metodu **TriggerCustomAction:**

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

Metoda **TriggerCustomAction** přijme příchozí požadavek a jednoduše vrátí odpověď se stavovým kódem.

### <a name="create-a-custom-resource"></a>Vytvoření vlastního prostředku

Pro vlastní zprostředkovatele je vytvořen vlastní prostředek prostřednictvím požadavků PUT. Vlastní zprostředkovatel přijme tělo požadavku JSON, který obsahuje sadu vlastností pro vlastní prostředek. Prostředky v Azure postupujte podle modelu RESTful. Stejnou adresu URL požadavku můžete použít k vytvoření, načtení nebo odstranění prostředku.

Přidejte následující metodu **CreateCustomResource** k vytvoření nových prostředků:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

Metoda **CreateCustomResource** aktualizuje příchozí požadavek tak, aby zahrnoval **id**pole specifická pro Azure , **název**a **typ**. Tato pole jsou vlastnosti nejvyšší úrovně používané službami v rámci Azure. Umožňují vlastnímu poskytovateli spolupracovat s dalšími službami, jako jsou Azure Policy, Šablony Azure Resource Manager a Protokol aktivit Azure.

Vlastnost | Příklad | Popis
---|---|---
**Jméno** | {myCustomResourceName} | Název vlastního prostředku
**Typ** | Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | Obor názvů typu prostředku
**Id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | ID prostředku

Kromě přidání vlastností jste také uložili dokument JSON do úložiště azure table.

### <a name="retrieve-a-custom-resource"></a>Načtení vlastního prostředku

Pro vlastní zprostředkovatele vlastní prostředek se načte prostřednictvím požadavků GET. Vlastní zprostředkovatel *nepřijímá* tělo požadavku JSON. Pro požadavky GET koncový bod `x-ms-customproviders-requestpath` používá záhlaví vrátit již vytvořený prostředek.

Přidejte následující metodu **RetrieveCustomResource** pro načtení existujících prostředků:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

V Azure prostředky postupujte podle modelu RESTful. Adresa URL požadavku, která vytvoří prostředek, také vrátí prostředek, pokud je proveden požadavek GET.

### <a name="remove-a-custom-resource"></a>Odebrání vlastního prostředku

Pro vlastní zprostředkovatele je vlastní prostředek odebrán prostřednictvím požadavků DELETE. Vlastní zprostředkovatel *nepřijímá* tělo požadavku JSON. Pro požadavek DELETE koncový bod `x-ms-customproviders-requestpath` používá záhlaví k odstranění již vytvořeného prostředku.

Chcete-li odebrat existující prostředky, přidejte následující metodu **RemoveCustomResource:**

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

V Azure prostředky postupujte podle modelu RESTful. Adresa URL požadavku, která vytvoří prostředek, také odstraní prostředek, pokud je proveden požadavek DELETE.

### <a name="list-all-custom-resources"></a>Vypsat všechny vlastní zdroje

Pro vlastní zprostředkovatele můžete vytvořit výčet seznamu existujících vlastních prostředků pomocí kolekce get požadavky. Vlastní zprostředkovatel *nepřijímá* tělo požadavku JSON. Pro kolekci požadavků GET koncový bod `x-ms-customproviders-requestpath` používá záhlaví k výčetu již vytvořených prostředků.

Přidejte následující metodu **EnumerateAllCustomResources** pro výčet existujících prostředků:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> RowKey QueryComparisons.GreaterThan a QueryComparisons.LessThan je syntaxe úložiště tabulky Azure k provedení dotazu "startswith" pro řetězce.

Chcete-li zobrazit seznam všech existujících prostředků, vygenerujte dotaz úložiště Azure Table, který zajistí, že prostředky existují pod oddílem vlastního zprostředkovatele. Dotaz pak zkontroluje, zda klíč `{myResourceType}` řádku začíná stejnou hodnotou.

## <a name="integrate-restful-operations"></a>Integrace operací RESTful

Po přidání všech metod RESTful do aplikace funkce aktualizujte hlavní metodu **Run,** která volá funkce pro zpracování různých požadavků REST:

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for a custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
```

Aktualizovaná metoda **Run** nyní zahrnuje vstupní vazbu *tableStorage,* kterou jste přidali pro úložiště Azure Table. První část metody přečte `x-ms-customproviders-requestpath` záhlaví a `Microsoft.Azure.Management.ResourceManager.Fluent` používá knihovnu k analýzě hodnoty jako ID prostředku. Záhlaví `x-ms-customproviders-requestpath` je odesláno vlastním zprostředkovatelem a určuje cestu k příchozímu požadavku.

Pomocí analyzovaného ID prostředku můžete vygenerovat hodnoty **partitionKey** a **rowKey** pro data, která mají být vyhledána nebo ukládat vlastní prostředky.

Po přidání metod a tříd je třeba aktualizovat **pomocí** metod pro aplikaci funkce. Přidejte následující kód do horní části souboru C#:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Pokud se ztratíte v libovolném bodě tohoto kurzu, můžete najít kompletní ukázku kódu ve [vlastním odkazu na koncový bod C# RESTful](./reference-custom-providers-csharp-endpoint.md). Po dokončení aplikace funkce uložte adresu URL aplikace funkce. Může být použit k aktivaci aplikace funkce v pozdějších kurzech.

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili koncový bod RESTful pro práci s koncovým bodem vlastního zprostředkovatele Azure. Chcete-li se dozvědět, jak vytvořit vlastního zprostředkovatele, přejděte do článku [Kurz: Vytvoření vlastního zprostředkovatele](./tutorial-custom-providers-create.md).
