---
title: Funkce protokolu HTTP v Durable Functions-Azure Functions
description: Přečtěte si o integrovaných funkcích protokolu HTTP v rozšíření Durable Functions pro Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 64d40de50f21811a56318971de1836abc8fbf8c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027257"
---
# <a name="http-features"></a>Funkce protokolu HTTP

Durable Functions má několik funkcí, které usnadňují začlenění trvalé Orchestrace a entit do pracovních postupů HTTP. Tento článek obsahuje podrobné informace o některých těchto funkcích.

## <a name="exposing-http-apis"></a>Odhalení rozhraní API HTTP

Orchestrace a entity lze vyvolat a spravovat pomocí požadavků HTTP. Rozšíření Durable Functions zpřístupňuje Vestavěná rozhraní API protokolu HTTP. Poskytuje také rozhraní API pro interakci s orchestrací a entitami v rámci funkcí aktivovaných protokolem HTTP.

### <a name="built-in-http-apis"></a>Integrovaná rozhraní API HTTP

Rozšíření Durable Functions automaticky přidá sadu rozhraní API HTTP do hostitele Azure Functions. Pomocí těchto rozhraní API můžete pracovat s orchestrací a entitami a spravovat je bez nutnosti psaní kódu.

Podporují se následující integrovaná rozhraní API HTTP.

* [Spustit novou orchestraci](durable-functions-http-api.md#start-orchestration)
* [Instance orchestrace dotazu](durable-functions-http-api.md#get-instance-status)
* [Ukončení instance Orchestration](durable-functions-http-api.md#terminate-instance)
* [Odeslání externí události do orchestrace](durable-functions-http-api.md#raise-event)
* [Vymazat historii orchestrace](durable-functions-http-api.md#purge-single-instance-history)
* [Odeslání události operace do entity](durable-functions-http-api.md#signal-entity)
* [Získání stavu entity](durable-functions-http-api.md#get-entity)
* [Dotazování seznamu entit](durable-functions-http-api.md#list-entities)

Úplný popis všech vestavěných rozhraní API HTTP zveřejněných pomocí rozšíření Durable Functions najdete v článku věnovaném [rozhraním API protokolu HTTP](durable-functions-http-api.md) .

### <a name="http-api-url-discovery"></a>Zjišťování adresy URL rozhraní API HTTP

[Vazba klienta Orchestration](durable-functions-bindings.md#orchestration-client) zpřístupňuje rozhraní API, která mohou generovat praktické datové části odpovědi HTTP. Například může vytvořit odpověď obsahující odkazy na rozhraní API pro správu pro určitou instanci orchestrace. Následující příklady ukazují funkci triggeru HTTP, která ukazuje, jak používat toto rozhraní API pro novou instanci orchestrace:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

# <a name="python"></a>[Python](#tab/python)

**__init__. py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    function_name = req.route_params['functionName']
    event_data = req.get_body()

    instance_id = await client.start_new(function_name, instance_id, event_data)
    
    logging.info(f"Started orchestration with ID = '{instance_id}'.")
    return client.create_check_status_response(req, instance_id)
```

**function.json**

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}",
      "methods": [
        "post",
        "get"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

---

Spuštění funkce Orchestrator pomocí funkcí triggeru protokolu HTTP, které jsou uvedené výše, se dá provést pomocí libovolného klienta HTTP. Následující příkaz oblé spustí funkci Orchestrator s názvem `DoWork` :

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Dále je příklad reakce na orchestraci, která má `abc123` jako své ID. Některé podrobnosti byly pro přehlednost odebrány.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

V předchozím příkladu každé pole, které končí, `Uri` odpovídá integrovanému HTTP API. Pomocí těchto rozhraní API můžete spravovat cílovou instanci Orchestration.

> [!NOTE]
> Formát adres URL Webhooku závisí na tom, kterou verzi Azure Functions hostitele používáte. Předchozí příklad je pro hostitele Azure Functions 2,0.

Popis všech vestavěných rozhraní API HTTP najdete v [referenčních informacích k rozhraní HTTP API](durable-functions-http-api.md).

### <a name="async-operation-tracking"></a>Sledování asynchronních operací

Výše uvedená odpověď protokolu HTTP je navržena tak, aby pomáhala implementaci dlouhotrvajících asynchronních rozhraní API HTTP pomocí Durable Functions. Tento model se někdy označuje jako *uživatelský vzor cyklického dotazování*. Tok klienta/serveru funguje takto:

1. Klient vydá požadavek HTTP na spuštění dlouhotrvajícího procesu, jako je funkce Orchestrator.
1. Cílový aktivační procedura HTTP vrátí odpověď HTTP 202 s hlavičkou umístění, která má hodnotu "statusQueryGetUri".
1. Klient se dotazuje adresy URL v hlavičce umístění. Klient stále zobrazuje odpovědi HTTP 202 s hlavičkou umístění.
1. Když je instance dokončena nebo se nezdařila, koncový bod v hlavičce umístění vrátí HTTP 200.

Tento protokol umožňuje koordinaci dlouhotrvajících procesů s externími klienty nebo službami, které se mohou dotazovat na koncový bod HTTP a následovat po hlavičce umístění. Implementace klienta i serveru tohoto vzoru jsou integrované do Durable Functions rozhraní API HTTP.

> [!NOTE]
> Ve výchozím nastavení všechny akce založené na protokolu HTTP, které poskytuje [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) , podporují standardní vzorek asynchronní operace. Tato možnost umožňuje vložit dlouhodobě spuštěnou trvalou funkci jako součást pracovního postupu Logic Apps. Další podrobnosti o Logic Apps podpoře asynchronních vzorů HTTP najdete v dokumentaci k [akcím pracovního postupu Azure Logic Apps a triggerům](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> Interakce s orchestrací se dají provádět z libovolného typu funkce, nikoli jenom funkcí aktivovaných protokolem HTTP.

Další informace o tom, jak spravovat Orchestrace a entity pomocí klientských rozhraní API, najdete v článku věnovaném [správě instancí](durable-functions-instance-management.md).

## <a name="consuming-http-apis"></a>Využívání rozhraní API HTTP

Jak je popsáno v tématu [omezení kódu funkce Orchestrator](durable-functions-code-constraints.md), funkce Orchestrator nemohou provádět přímé operace vstupu a výstupu. Místo toho obvykle volají [funkce aktivity](durable-functions-types-features-overview.md#activity-functions) , které provádějí vstupně-výstupní operace.

Počínaje Durable Functions 2,0 mohou orchestrace nativně spotřebovávat rozhraní API HTTP pomocí [aktivační vazby pro orchestraci](durable-functions-bindings.md#orchestration-trigger).

Následující příklad kódu ukazuje funkci Orchestrator, která provádí odchozí požadavek HTTP:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = yield context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    response = yield context.call_http('GET', url)
    
    if response["statusCode"] >= 400:
        # handling of error codes goes here

main = df.Orchestrator.create(orchestrator_function)
```

---

Pomocí akce zavolat HTTP můžete v rámci svých funkcí Orchestrator provádět následující akce:

* Volání rozhraní API HTTP přímo z funkcí orchestrace s některými omezeními, která jsou uvedena později.
* Automaticky podporuje vzory cyklického dotazování na stav HTTP 202 na straně klienta.
* Pomocí [spravovaných identit Azure](../../active-directory/managed-identities-azure-resources/overview.md) můžete autorizovat volání http do jiných koncových bodů Azure.

Schopnost využívat rozhraní API HTTP přímo z funkcí nástroje Orchestrator je určena jako pohodlí pro určitou sadu běžných scénářů. Všechny tyto funkce můžete implementovat sami pomocí funkcí aktivity. V mnoha případech vám funkce aktivit můžou získat větší flexibilitu.

### <a name="http-202-handling"></a>Zpracování HTTP 202

Rozhraní API volání HTTP může automaticky implementovat stranu klienta se vzorem příjemce cyklického dotazování. Pokud volané rozhraní API vrátí odpověď HTTP 202 s hlavičkou umístění, funkce Orchestrator automaticky provede dotaz na umístění prostředku, dokud neobdrží odpověď jinou než 202. Tato odpověď bude odpověď vrácena do kódu funkce nástroje Orchestrator.

> [!NOTE]
> Funkce Orchestrator také nativně podporuje model příjemce dotazování na straně serveru, jak je popsáno v tématu [sledování asynchronních operací](#async-operation-tracking). Tato podpora znamená, že orchestrace v jedné aplikaci Function App můžou snadno koordinovat funkce Orchestrator v jiných aplikacích funkcí. To je podobné konceptu [dílčí orchestrace](durable-functions-sub-orchestrations.md) , ale podporuje komunikaci mezi aplikacemi. Tato podpora je užitečná zejména pro vývoj aplikací se stylem mikroslužeb.

### <a name="managed-identities"></a>Spravované identity

Durable Functions nativně podporuje volání rozhraní API, která přijímají tokeny Azure Active Directory (Azure AD) pro autorizaci. Tato podpora získá tyto tokeny pomocí [spravovaných identit Azure](../../active-directory/managed-identities-azure-resources/overview.md) .

Následující kód je příkladem funkce rozhraní .NET Orchestrator. Funkce provádí ověřená volání k restartování virtuálního počítače pomocí [REST API virtuálních počítačů](/rest/api/compute/virtualmachines)s Azure Resource Manager.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net/.default
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net/.default"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net/.default");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    subscription_id = "mySubId"
    resource_group = "myRg"
    vm_name = "myVM"
    api_version = "2019-03-01"
    token_source = df.ManagedIdentityTokenSource("https://management.core.windows.net/.default")

    # get a list of the Azure subscriptions that I have access to
    restart_response = yield context.call_http("POST", 
        f"https://management.azure.com/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}/restart?api-version={api_version}",
        None,
        None,
        token_source)
    return restart_response

main = df.Orchestrator.create(orchestrator_function)
```

---

V předchozím příkladu `tokenSource` je parametr nakonfigurovaný tak, aby získal tokeny Azure AD pro [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Tokeny jsou identifikovány identifikátorem URI prostředku `https://management.core.windows.net/.default` . Příklad předpokládá, že aktuální aplikace Function App běží buď místně, nebo byla nasazena jako aplikace Function App se spravovanou identitou. Předpokládá se, že místní identita nebo spravovaná identita má oprávnění ke správě virtuálních počítačů v zadané skupině prostředků `myRG` .

Nakonfigurovaný zdroj tokenu za běhu automaticky vrátí přístupový token OAuth 2,0. Zdroj pak přidá token jako nosný token do autorizační hlavičky odchozí žádosti. Tento model je vylepšením ručního přidávání autorizačních hlaviček do požadavků HTTP z následujících důvodů:

* Aktualizace tokenu se zpracovává automaticky. Nemusíte se starat o tokeny, jejichž platnost vypršela.
* Tokeny nejsou nikdy uloženy v trvalém stavu orchestrace.
* Pro správu získání tokenu nemusíte psát žádný kód.

V [předkompilované ukázce C# RestartVMs](https://github.com/Azure/azure-functions-durable-extension/blob/dev/samples/precompiled/RestartVMs.cs)můžete najít Úplnější příklad.

Spravované identity nejsou omezené na správu prostředků Azure. Spravované identity můžete použít pro přístup k jakémukoli rozhraní API, které přijímá tokeny nosiče Azure AD, včetně služeb Azure od Microsoftu a webových aplikací od partnerů. Webová aplikace partnera může být dokonce i jiná aplikace Function App. Seznam služeb Azure od Microsoftu, kteří podporují ověřování pomocí Azure AD, najdete v tématu [služby Azure, které podporují ověřování Azure AD](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Omezení

Integrovanou podporu pro volání rozhraní API HTTP je výhodná funkce. Není vhodný pro všechny scénáře.

Požadavky HTTP odesílané funkcemi Orchestrator a jejich odpovědi jsou serializované a trvalé jako zprávy ve frontě. Toto chování ve frontě zajišťuje, aby volání HTTP byla [spolehlivá a bezpečná pro opětovné přehrání orchestrace](durable-functions-orchestrations.md#reliability). Chování služby Řízení front má ale taky omezení:

* Každý požadavek HTTP zahrnuje další latenci v porovnání s nativním klientem HTTP.
* Velké zprávy žádosti nebo odpovědi, které se nemohou vejít do zprávy fronty, můžou významně snížit výkon orchestrace. Režie při snižování zátěže datových částí do úložiště objektů BLOB může způsobit potenciální snížení výkonu.
* Datové části streamování, bloků dat a binárních souborů se nepodporují.
* Možnost přizpůsobení chování klienta HTTP je omezená.

Pokud některá z těchto omezení by mohla ovlivnit váš případ použití, zvažte místo toho použití funkcí aktivity a klientských knihoven HTTP specifických pro jazyk k provedení odchozích volání HTTP.

> [!NOTE]
> Pokud jste vývojářem .NET, můžete se setkat s tím, proč tato funkce používá typy **DurableHttpRequest** a **DurableHttpResponse** namísto vestavěných typů rozhraní .NET **zprávy HttpRequestMessage** a **HttpResponseMessage** .
>
> Tato volba návrhu je úmyslné. Hlavním důvodem je, že vlastní typy vám pomůžou zajistit, aby uživatelé nemuseli vytvářet nesprávné předpoklady o podporovaném chování interního klienta HTTP. Typy specifické pro Durable Functions také umožňují zjednodušit návrh rozhraní API. Můžou taky snáze zpřístupnit speciální funkce, jako je [spravovaná integrace identit](#managed-identities) a [uživatelský vzor cyklického dotazování](#http-202-handling). 

### <a name="extensibility-net-only"></a>Rozšiřitelnost (jenom .NET)

Přizpůsobení chování interního klienta HTTP orchestrace je možné pomocí [vkládání závislostí Azure Functions .NET](../functions-dotnet-dependency-injection.md). Tato možnost může být užitečná pro provádění malých změn chování. Může to být užitečné také při testování jednotek klienta HTTP vložením objektů kresby.

Následující příklad ukazuje použití injektáže závislosti k zakázání ověřování certifikátu TLS/SSL pro funkce nástroje Orchestrator, které volají externí koncové body HTTP.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o trvalých entitách](durable-functions-entities.md)
