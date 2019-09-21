---
title: Funkce protokolu HTTP v Durable Functions-Azure Functions
description: Přečtěte si o integrovaných funkcích protokolu HTTP v rozšíření Durable Functions pro Azure Functions.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 5a9143643b1a1cabb32903933dbd68d665d0424f
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71171125"
---
# <a name="http-features"></a>Funkce protokolu HTTP

Durable Functions má několik funkcí, které usnadňují začlenění trvalé Orchestrace a entit do pracovních postupů HTTP. Tento článek obsahuje podrobné informace o některých těchto funkcích.

## <a name="exposing-http-apis"></a>Odhalení rozhraní API HTTP

Orchestrace a entity lze vyvolat a spravovat pomocí požadavků HTTP. Rozšíření Durable Functions zpřístupňuje Vestavěná rozhraní HTTP API a poskytuje rozhraní API pro interakci s orchestrací a entitami v rámci funkcí aktivovaných protokolem HTTP.

### <a name="built-in-http-apis"></a>Integrovaná rozhraní API HTTP

Rozšíření Durable Functions automaticky přidá sadu rozhraní API HTTP do hostitele Azure Functions. Tato rozhraní API umožňují pracovat s orchestrací a entitami a spravovat je bez psaní kódu.

Podporují se následující integrovaná rozhraní API HTTP.

* [Spustit novou orchestraci](durable-functions-http-api.md#start-orchestration)
* [Instance orchestrace dotazu](durable-functions-http-api.md#get-instance-status)
* [Ukončení instance Orchestration](durable-functions-http-api.md#terminate-instance)
* [Odeslání externí události do orchestrace](durable-functions-http-api.md#raise-event)
* [Vymazat historii orchestrace](durable-functions-http-api.md#purge-single-instance-history)
* [Odeslání události operace do entity](durable-functions-http-api.md#signal-entity)
* [Dotaz na stav entity](durable-functions-http-api.md#query-entity)

Úplný popis všech vestavěných rozhraní API HTTP zveřejněných pomocí rozšíření Durable Functions najdete v článku věnovaném [rozhraním API protokolu HTTP](durable-functions-http-api.md) .

### <a name="http-api-url-discovery"></a>Zjišťování adresy URL rozhraní API HTTP

[Vazba klienta Orchestration](durable-functions-bindings.md#orchestration-client) zpřístupňuje rozhraní API, která lze použít ke generování praktické datové části odpovědi HTTP. Například může vytvořit odpověď obsahující odkazy na rozhraní API pro správu pro určitou instanci orchestrace. Zde je příklad funkce triggeru HTTP, která ukazuje, jak používat toto rozhraní API pro novou instanci orchestrace:

#### <a name="precompiled-c"></a>PředkompilovanéC#

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

#### <a name="c-script"></a>C#Pravidel

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

#### <a name="functionjson"></a>Function.json

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

Spuštění funkce Orchestrator pomocí výše uvedených funkcí triggeru HTTP se dá provést pomocí libovolného klienta HTTP. Následující příkaz složené spustí funkci Orchestrator s názvem `DoWork`.

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Tady je příklad reakce na orchestraci s `abc123` jako jeho ID (některé podrobnosti jsou odebrané pro přehlednost):

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

`~Uri` Každé pole v předchozím příkladu odpovídá integrovaným rozhraním API protokolu HTTP. Tato rozhraní API je možné použít ke správě cílové instance orchestrace.

> [!NOTE]
> Formát adres URL Webhooku se může lišit v závislosti na tom, kterou verzi Azure Functions hostitele používáte. Výše uvedený příklad je určen pro hostitele Azure Functions 2,0.

Popis všech vestavěných rozhraní API HTTP najdete v referenční dokumentaci k [rozhraní HTTP API](durable-functions-http-api.md) .

### <a name="async-operation-tracking"></a>Sledování asynchronních operací

Výše uvedená odpověď protokolu HTTP je navržena tak, aby pomáhala implementaci dlouhotrvajících asynchronních rozhraní API HTTP pomocí Durable Functions. Tento model se někdy označuje jako *uživatelský vzor cyklického dotazování*. Tok klienta/serveru funguje takto:

1. Klient vydá požadavek HTTP na spuštění dlouhotrvajícího procesu, jako je například funkce Orchestrator.
2. Cílový aktivační procedura http vrátí odpověď HTTP 202 s `Location` hlavičkou `statusQueryGetUri` s hodnotou.
3. Klient dotazuje adresu URL v `Location` hlavičce. Nadále zobrazuje odpovědi HTTP 202 s `Location` hlavičkou.
4. Když se instance dokončí (nebo se nezdařila), `Location` koncový bod v hlavičce vrátí HTTP 200.

Tento protokol umožňuje koordinovat dlouhotrvající procesy s externími klienty nebo službami, které podporují cyklické dotazování koncového bodu http a `Location` následují za hlavičkou. Implementace klienta i serveru tohoto vzoru jsou integrované do Durable Functions rozhraní API HTTP.

> [!NOTE]
> Ve výchozím nastavení všechny akce založené na protokolu HTTP, které poskytuje [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) , podporují standardní vzorek asynchronní operace. Tato možnost umožňuje vložit dlouhodobě spuštěnou trvalou funkci jako součást pracovního postupu Logic Apps. Další podrobnosti o podpoře Logic Apps pro asynchronní vzory HTTP najdete v [dokumentaci k akcím pracovního postupu Azure Logic Apps a triggerům](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> Interakce s orchestrací se dá provádět z libovolného typu funkce, nikoli jenom funkcí aktivovaných protokolem HTTP.

Další informace o tom, jak spravovat Orchestrace a entity pomocí klientských rozhraní API, najdete v článku věnovaném [správě instancí](durable-functions-instance-management.md) .

## <a name="consuming-http-apis"></a>Využívání rozhraní API HTTP

Funkce nástroje Orchestrator nejsou povoleny přímo v/v, jak je popsáno v tématu [omezení kódu funkce nástroje Orchestrator](durable-functions-code-constraints.md). Místo toho nástroj Orchestrator Functions obvykle volá [funkce aktivity](durable-functions-types-features-overview.md#activity-functions) , které provádějí vstupně-výstupní operace.

Počínaje Durable Functions 2,0 jsou orchestrace schopná nativně spotřebovávat rozhraní HTTP API pomocí [aktivační vazby triggeru Orchestration](durable-functions-bindings.md#orchestration-trigger).

> [!NOTE]
> Možnost volat koncové body HTTP přímo z funkcí nástroje Orchestrator není zatím k dispozici v jazyce JavaScript.

Následující příklad kódu ukazuje funkci Orchestrator C# , která vytváří odchozí požadavek HTTP pomocí `CallHttpAsync` rozhraní .NET API:

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

Akce volání HTTP vám umožní ve svých funkcích Orchestrator následující věci:

* Volejte rozhraní API HTTP přímo z funkcí orchestrace (s některými omezeními uvedenými později).
* Automatické podpora způsobů cyklického dotazování na stav HTTP 202 na straně klienta
* Pomocí [spravovaných identit Azure](../../active-directory/managed-identities-azure-resources/overview.md) můžete autorizovat volání http do jiných koncových bodů Azure.

Schopnost využívat rozhraní API HTTP přímo z funkcí nástroje Orchestrator je určena jako pohodlí pro určitou sadu běžných scénářů. Všechny tyto funkce je možné implementovat sami pomocí funkcí aktivity. V mnoha případech vám funkce aktivit můžou získat větší flexibilitu.

### <a name="http-202-handling"></a>Zpracování HTTP 202

Rozhraní API volání HTTP může automaticky implementovat stranu klienta se *vzorem příjemce cyklického dotazování*. Pokud volané rozhraní API vrátí odpověď HTTP 202 s `Location` hlavičkou, funkce Orchestrator automaticky provede dotaz na `Location` prostředek, dokud se nevrátí odpověď, která není 202. Odpověď, která není 202, bude odpověď vrácená do kódu funkce nástroje Orchestrator.

> [!NOTE]
> Funkce Orchestrator také nativně podporuje *model příjemce dotazování*na straně serveru, jak je popsáno v tématu [sledování asynchronních operací](#async-operation-tracking). To znamená, že orchestrace v jedné aplikaci Function App můžou snadno koordinovat funkce Orchestrator v jiných aplikacích funkcí. To je podobné konceptu [dílčí orchestrace](durable-functions-sub-orchestrations.md) , ale podporuje komunikaci mezi aplikacemi. To je zvlášť užitečné pro vývoj aplikací se stylem mikroslužeb.

### <a name="managed-identities"></a>Spravované identity

Durable Functions nativně podporuje volání rozhraní API, která přijímají tokeny Azure AD pro autorizaci. Tato podpora získá tyto tokeny pomocí [spravovaných identit Azure](../../active-directory/managed-identities-azure-resources/overview.md) .

Následující kód je příkladem funkce .NET Orchestrator, která umožňuje ověřená volání k restartování virtuálního počítače pomocí Azure Resource Manager [Virtual Machines REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines).

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    
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

V předchozím příkladu je `tokenSource` parametr nakonfigurovaný tak, aby získal tokeny Azure AD pro [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) (identifikovaný identifikátorem URI `https://management.core.windows.net`prostředku). V příkladu se předpokládá, že aktuální aplikace Function App je buď spuštěná místně, nebo byla nasazena jako aplikace Azure Functions se spravovanou identitou. Předpokládá se, `myRG`že místní identita nebo spravovaná identita má oprávnění ke správě virtuálních počítačů v zadané skupině prostředků.

Nakonfigurovaný zdroj tokenu za běhu automaticky vrátí přístupový token OAuth 2,0 a přidá ho jako nosný token do `Authorization` hlavičky odchozího požadavku. Tento model je vylepšením ručního přidávání autorizačních hlaviček do požadavků HTTP z těchto důvodů:

* Aktualizace tokenu se zpracovává automaticky. Nemusíte se starat o tokeny, jejichž platnost vypršela.
* Tokeny nejsou nikdy uloženy v trvalém stavu orchestrace.
* Nemusíte psát žádný kód, abyste mohli řešit získání tokenu.

Úplnější příklad najdete v [předkompilovaných C# ukázkách "RestartVMs"](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs).

Spravované identity nejsou omezené na správu prostředků Azure. Spravované identity lze použít pro přístup k jakémukoli rozhraní API, které přijímá tokeny nosiče Azure AD, včetně služeb Azure First stran nebo webových aplikací třetích stran. Webová aplikace třetí strany by mohla být i jiná aplikace Function App. Seznam služeb Azure First stran, které podporují ověřování pomocí Azure AD, najdete v tématu [služby Azure, které podporují ověřování Azure AD](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Omezení

Integrovaná podpora pro volání rozhraní API HTTP je výhodná a není vhodná pro všechny scénáře. Požadavky HTTP odesílané funkcemi Orchestrator a jejich odpovědi jsou serializovaná a trvalá jako zprávy ve frontě. Toto chování ve frontě zajišťuje, aby volání HTTP byla [spolehlivá a bezpečná pro opětovné přehrání orchestrace](durable-functions-orchestrations.md#reliability). Toto chování fronty ale také znamená, že:

* Každý požadavek HTTP zahrnuje další latenci v porovnání s nativním klientem HTTP.
* Velké zprávy žádosti nebo odpovědi, které se nemohou vejít do zprávy fronty, můžou významně snížit výkon orchestrace. Potenciální snížení výkonu je kvůli režii při snižování zátěže datových částí zpráv do úložiště objektů BLOB.
* Datové části streamování, bloků dat a binárních souborů se nepodporují.
* Možnost přizpůsobení chování klienta HTTP je omezená.

Pokud některá z těchto omezení můžou mít vliv na váš případ použití, zvažte místo toho použití funkcí aktivity a klientských knihoven HTTP specifických pro jazyk k provedení odchozích volání HTTP.

> [!NOTE]
> Pokud jste vývojářem .NET, možná vás zajímá, proč tato funkce používá `DurableHttpRequest` a `DurableHttpResponse` typy místo vestavěných rozhraní .NET `HttpRequestMessage` a `HttpResponseMessage`. Tato volba návrhu byla záměrné. Hlavním důvodem je, že vlastní typy vám pomůžou zajistit, aby uživatelé nemuseli vytvářet nesprávné předpoklady o podporovaném chování interního klienta HTTP. Typy s trvalým nastavením také umožňují zjednodušit návrh rozhraní API a snadněji vysvětlovat speciální funkce, jako je [spravovaná integrace identit](#managed-identities) a [uživatelský vzor cyklického dotazování](#http-202-handling).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o trvalých entitách](durable-functions-entities.md)
