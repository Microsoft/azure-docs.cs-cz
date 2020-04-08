---
title: Funkce HTTP v trvanlivých funkcích – funkce Azure
description: Seznamte se s integrovanými funkcemi PROTOKOLU HTTP v rozšíření Durable Functions pro funkce Azure.
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: fece1155d2f707f11dda9f3896bd8a08deff1557
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802379"
---
# <a name="http-features"></a>Funkce protokolu HTTP

Trvalé funkce má několik funkcí, které usnadňují začlenění trvanlivé orchestrations a entity do pracovních postupů HTTP. Tento článek jde do podrobností o některé z těchto funkcí.

## <a name="exposing-http-apis"></a>Vystavení http api

Orchestrations a entity lze vyvolat a spravovat pomocí požadavků HTTP. Rozšíření trvalé funkce zpřístupňuje integrované http api. Poskytuje také api pro interakci s orchestrations a entity z v rámci funkce spouštěné HTTP.

### <a name="built-in-http-apis"></a>Integrovaná http api

Rozšíření Trvalé funkce automaticky přidá sadu http api do hostitele Azure Functions. S těmito api můžete pracovat s a spravovat orchestrations a entity bez psaní kódu.

Jsou podporována následující vestavěná http api.

* [Spustit novou orchestraci](durable-functions-http-api.md#start-orchestration)
* [Instance orchestrace dotazu](durable-functions-http-api.md#get-instance-status)
* [Ukončit instanci orchestrace](durable-functions-http-api.md#terminate-instance)
* [Odeslání externí události do orchestrace](durable-functions-http-api.md#raise-event)
* [Historie orchestrace vymazání](durable-functions-http-api.md#purge-single-instance-history)
* [Odeslání události operace entitě](durable-functions-http-api.md#signal-entity)
* [Získání stavu entity](durable-functions-http-api.md#get-entity)
* [Dotaz na seznam entit](durable-functions-http-api.md#list-entities)

Úplný popis všech integrovaných předdefinovaných protokolů API HTTP vystavených rozšířením Durable Functions naleznete v [článku http api.](durable-functions-http-api.md)

### <a name="http-api-url-discovery"></a>Zjišťování adres URL rozhraní HTTP API

Vazba [klienta orchestrace](durable-functions-bindings.md#orchestration-client) zveřejňuje api, která mohou generovat pohodlné datové části odpovědi HTTP. Může například vytvořit odpověď obsahující odkazy na správu API pro konkrétní instanci orchestrace. Následující příklady ukazují funkci aktivační události HTTP, která ukazuje, jak používat toto rozhraní API pro novou instanci orchestrace:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

---

Spuštění funkce orchestrator pomocí dříve uvedených funkcí aktivační ch odsátcích HTTP lze provést pomocí libovolného klienta HTTP. Následující příkaz cURL spustí funkci orchestratoru s názvem `DoWork`:

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Další je příklad odpovědi pro orchestraci, která má `abc123` jako jeho ID. Některé podrobnosti byly odstraněny pro přehlednost.

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

V předchozím příkladu každé pole `Uri` končící na odpovídá integrované rozhraní HTTP API. Tato api můžete použít ke správě instance orchestrace cíle.

> [!NOTE]
> Formát adres URL webhooku závisí na tom, kterou verzi hostitele Azure Functions používáte. Předchozí příklad je pro hostitele Azure Functions 2.0.

Popis všech předdefinovaných rozhraní HTTP API naleznete v [odkazu http rozhraní API](durable-functions-http-api.md).

### <a name="async-operation-tracking"></a>Sledování asynchronní operace

Odpověď HTTP již bylo uvedeno výše je navržen tak, aby pomoci implementovat dlouhotrvající asynchronní api HTTP s trvalé funkce. Tento vzor se někdy označuje jako *vzor dotazování spotřebitele*. Tok klienta/serveru funguje takto:

1. Klient vydá požadavek HTTP pro spuštění dlouhotrvajícího procesu, jako je funkce orchestrator.
1. Cílová aktivační událost HTTP vrátí odpověď HTTP 202 s hlavičkou Location, která má hodnotu "statusQueryGetUri".
1. Klient zaosívá adresu URL v záhlaví Umístění. Klient nadále vidí odpovědi HTTP 202 s hlavičkou Umístění.
1. Když instance dokončí nebo selže, koncový bod v hlavičce umístění vrátí HTTP 200.

Tento protokol umožňuje koordinaci dlouhotrvajících procesů s externími klienty nebo službami, které mohou dotazovat koncový bod HTTP a sledovat hlavičku Umístění. Klient a server implementace tohoto vzoru jsou integrovány do trvalé funkce HTTP API.

> [!NOTE]
> Ve výchozím nastavení podporují všechny akce založené na protokolu HTTP poskytované [aplikacemi Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) standardní vzor asynchronní operace. Tato funkce umožňuje vložit dlouhotrvající trvanlivé funkce jako součást pracovního postupu Logic Apps. Další podrobnosti o podpoře logic apps pro asynchronní vzory HTTP najdete v [akcích pracovního postupu Azure Logic Apps a v dokumentaci k aktivačním událostem](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> Interakce s orchestracemi lze provádět z libovolného typu funkce, nikoli pouze z funkcí spouštěných protokolem HTTP.

Další informace o tom, jak spravovat orchestrations a entity pomocí klientských api, naleznete v [článku správa instance](durable-functions-instance-management.md).

## <a name="consuming-http-apis"></a>Náročné http api

Jak je popsáno v [omezení kódu funkce orchestrator](durable-functions-code-constraints.md), funkce orchestrator nelze provést vstupně-do přímo. Místo toho obvykle volají [funkce aktivity,](durable-functions-types-features-overview.md#activity-functions) které vstupně-v. operace.

Počínaje trvanlivé funkce 2.0 orchestrations můžete nativně využívat http api pomocí [orchestrace aktivační vazby](durable-functions-bindings.md#orchestration-trigger).

Následující ukázkový kód ukazuje funkci orchestrator, která provádí odchozí požadavek HTTP:

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
    const response = context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

Pomocí akce "volání HTTP" můžete ve funkcích orchestrátoru provést následující akce:

* Volání HTTP API přímo z funkcí orchestrace, s některými omezeními, které jsou uvedeny později.
* Automaticky podporuje vzory dotazování stavu HTTP 202 na straně klienta.
* Pomocí [spravovaných identit Azure](../../active-directory/managed-identities-azure-resources/overview.md) můžete provádět autorizovaná volání HTTP do jiných koncových bodů Azure.

Možnost využívat http api přímo z funkcí orchestrator je určena jako pohodlí pro určitou sadu běžných scénářů. Všechny tyto funkce můžete implementovat sami pomocí funkcí aktivity. V mnoha případech funkce aktivity může poskytnout větší flexibilitu.

### <a name="http-202-handling"></a>Manipulace s HTTP 202

Rozhraní API "volání HTTP" můžete automaticky implementovat straně klienta dotazování spotřebitele vzor. Pokud volané rozhraní API vrátí odpověď HTTP 202 s hlavičkou Umístění, funkce orchestrator automaticky zveí na prostředek umístění, dokud neobdrží odpověď jinou než 202. Tato odpověď bude odpověď vrácena kódu funkce orchestrator.

> [!NOTE]
> Funkce Orchestrator také nativně podporují vzor příjemce dotazování na straně serveru, jak je popsáno v [asynchronní operaci sledování](#async-operation-tracking). Tato podpora znamená, že orchestrace v jedné aplikaci funkce můžete snadno koordinovat funkce orchestrator v jiných aplikacích funkce. To je podobné konceptu [podorchestrace,](durable-functions-sub-orchestrations.md) ale s podporou komunikace mezi aplikacemi. Tato podpora je zvláště užitečná pro vývoj aplikací ve stylu mikroslužeb.

### <a name="managed-identities"></a>Spravované identity

Trvalé funkce nativně podporuje volání rozhraní API, které přijímají tokeny Azure Active Directory (Azure AD) pro autorizaci. Tato podpora používá [azure spravované identity](../../active-directory/managed-identities-azure-resources/overview.md) k získání těchto tokenů.

Následující kód je příkladem funkce orchestrátoru .NET. Funkce umožňuje ověřená volání restartování virtuálního počítače pomocí [virtuálních počítačů](https://docs.microsoft.com/rest/api/compute/virtualmachines)Azure Resource Manager REST API .

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
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
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
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net");

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

---

V předchozím příkladu `tokenSource` je parametr nakonfigurovaný pro získání tokenů Azure AD pro [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Tokeny jsou identifikovány identifikátorem URI `https://management.core.windows.net`prostředku . Příklad předpokládá, že aktuální aplikace funkce je spuštěna místně nebo byla nasazena jako funkční aplikace se spravovanou identitou. Předpokládá se, že místní identita nebo spravovaná identita má `myRG`oprávnění ke správě virtuálních připojení v zadané skupině prostředků .

Za běhu nakonfigurovaný zdroj tokenu automaticky vrátí přístupový token OAuth 2.0. Zdroj pak přidá token jako nosný token do hlavičky autorizace odchozího požadavku. Tento model je vylepšení oproti ručnímu přidání hlavičky autorizace na požadavky HTTP z následujících důvodů:

* Aktualizace tokenu je zpracována automaticky. Nemusíte se starat o vypršení platnosti tokenů.
* Tokeny jsou nikdy uloženy ve stavu trvalé orchestrace.
* Nemusíte psát žádný kód pro správu získávání tokenu.

Úplnější příklad najdete v [předkompilované ukázce restartvmů jazyka C#](https://github.com/Azure/azure-functions-durable-extension/blob/dev/samples/precompiled/RestartVMs.cs).

Spravované identity nejsou omezeny na správu prostředků Azure. Spravované identity můžete použít k přístupu k libovolnému rozhraní API, které přijímá tokeny nosiče Azure AD, včetně služeb Azure od Microsoftu a webových aplikací od partnerů. Webová aplikace partnera může být dokonce další funkční aplikací. Seznam služeb Azure od Microsoftu, které podporují ověřování pomocí Azure AD, najdete v [tématu služby Azure AD, které podporují ověřování Azure AD](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Omezení

Integrovaná podpora pro volání http api je funkce pohodlí. Není to vhodné pro všechny scénáře.

Požadavky HTTP odeslané funkcemi orchestratoru a jejich odpovědi jsou serializovány a trvalé jako zprávy fronty. Toto chování fronty zajišťuje, že volání HTTP jsou [spolehlivá a bezpečná pro opětovné přehrání orchestrace](durable-functions-orchestrations.md#reliability). Chování řazení do fronty má však také omezení:

* Každý požadavek HTTP zahrnuje další latenci ve srovnání s nativním klientem HTTP.
* Velké požadavky nebo odpovědi zprávy, které se nevejdou do zprávy fronty může výrazně snížit výkon orchestrace. Režie snižování zátěže datové části zpráv do úložiště objektů blob může způsobit potenciální snížení výkonu.
* Streamování, bloky dat a binární datové části nejsou podporovány.
* Možnost přizpůsobit chování klienta HTTP je omezená.

Pokud některá z těchto omezení může ovlivnit váš případ použití, zvažte místo toho pomocí funkce aktivity a knihovny klientů HTTP specifické pro jazyk, aby odchozí volání HTTP.

> [!NOTE]
> Pokud jste vývojář rozhraní .NET, můžete se divit, proč tato funkce používá **typy DurableHttpRequest** a **DurableHttpResponse** místo vestavěných typů **HttpRequestMessage** a **HttpResponseMessage** .
>
> Tato volba návrhu je záměrná. Hlavním důvodem je, že vlastní typy pomáhají zajistit, aby uživatelé nevytvářeli nesprávné předpoklady o podporovaném chování interního klienta HTTP. Typy specifické pro trvanlivé funkce také umožňují zjednodušit návrh rozhraní API. Mohou také snadněji zpřístupnit speciální funkce, jako [je integrace spravované identity](#managed-identities) a [vzor dotazování spotřebitele](#http-202-handling). 

### <a name="extensibility-net-only"></a>Rozšiřitelnost (pouze rozhraní.NET)

Přizpůsobení chování interního klienta HTTP orchestrace je možné pomocí [vkládání závislostí Azure Functions .NET](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection). Tato schopnost může být užitečná pro provádění malých změn chování. Může být také užitečné pro testování částí klienta HTTP vložením mock objekty.

Následující příklad ukazuje použití vkládání závislostí zakázat ověřování certifikátu TLS/SSL pro funkce orchestrátoru, které volají externí koncové body HTTP.

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
> [Informace o odolných entitách](durable-functions-entities.md)
