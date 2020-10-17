---
title: Vývoj & konfigurace Azure Functions App – signál Azure
description: Podrobnosti o tom, jak vyvíjet a konfigurovat aplikace bez serveru v reálném čase pomocí Azure Functions a služby Azure Signal
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: acb85a04b8a1ca491058702510079a36b93fc657
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151040"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Vývoj a konfigurace služby Azure Functions s využitím služby Azure SignalR Service

Aplikace Azure Functions můžou využít [vazby služby Azure signaler](../azure-functions/functions-bindings-signalr-service.md) k přidávání možností v reálném čase. Klientské aplikace používají klientské sady SDK dostupné v několika jazycích pro připojení ke službě signalizace Azure a příjem zpráv v reálném čase.

Tento článek popisuje koncepty pro vývoj a konfiguraci aplikace služby Azure Functions, která je integrovaná se službou signalizace.

## <a name="signalr-service-configuration"></a>Konfigurace služby Signal

Služba signalizace Azure se dá nakonfigurovat v různých režimech. Při použití s Azure Functions musí být služba nakonfigurovaná v režimu bez *serveru* .

V Azure Portal vyhledejte stránku *Nastavení* prostředku služby signaler. Nastavte *režim služby* na bez *serveru*.

![Režim služby Signal](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Vývoj s využitím Azure Functions

Bezserverová aplikace v reálném čase vytvořená a s využitím Azure Functions a Azure SignalR Service zpravidla vyžaduje dvě funkce Azure Functions:

* Funkci „negotiate“, kterou klient volá k získání adresy URL koncového bodu a platného přístupového tokenu služby SignalR Service
* Jedna nebo více funkcí zpracovávajících zprávy ze služby signalizace a odesílání zpráv nebo Správa členství ve skupinách

### <a name="negotiate-function"></a>funkce Negotiate

Klientská aplikace vyžaduje pro připojení ke službě Azure Signal Service platný přístupový token. Přístupový token může být pro dané ID uživatele anonymní nebo ověřený. Aplikace služby signalizace bez serveru vyžadují koncový bod HTTP s názvem "Negotiate", aby získal token a jiné informace o připojení, jako je například adresa URL koncového bodu služby signalizace.

Pomocí funkce Azure aktivované protokolem HTTP a vstupní vazbou *SignalRConnectionInfo* vygenerujte objekt informace o připojení. Funkce musí mít trasu HTTP, která končí na `/negotiate` .

U [modelu založeného na třídách](#class-based-model) v jazyce C# není potřeba vstupní vazba *SignalRConnectionInfo* a může přidat vlastní deklarace, které jsou mnohem jednodušší. Viz [dohadování prostředí v modelu založeném na třídě](#negotiate-experience-in-class-based-model)

Další informace o tom, jak vytvořit funkci Negotiate, najdete v [referenčních odkazech na vstupní vazby *SignalRConnectionInfo* ](../azure-functions/functions-bindings-signalr-service-input.md).

Další informace o tom, jak vytvořit ověřený token, najdete v tématu [použití ověřování App Service](#using-app-service-authentication).

### <a name="handle-messages-sent-from-signalr-service"></a>Zpracování zpráv odeslaných ze služby Signal

Použijte vazbu *triggeru signálu* ke zpracování zpráv odeslaných ze služby signalizace. Můžete se aktivovat, když klienti odesílají zprávy nebo se klienti připojí nebo odpojí.

Další informace najdete v odkazu na [ *triggery triggeru signálu* ](../azure-functions/functions-bindings-signalr-service-trigger.md).

Také je nutné nakonfigurovat koncový bod funkce jako nadřazený, aby služba aktivovala funkci, kde se nachází zpráva od klienta. Další informace o tom, jak nakonfigurovat nadřazený datový proud, najdete v tomto [dokumentu](concept-upstream.md).

### <a name="sending-messages-and-managing-group-membership"></a>Odesílání zpráv a Správa členství ve skupinách

Pomocí výstupní vazby *signálu* můžete odesílat zprávy klientům připojeným ke službě Azure Signal. Zprávy můžete vysílat všem klientům nebo je můžete odeslat do podmnožiny klientů, které jsou ověřeny pomocí konkrétního ID uživatele nebo byly přidány do konkrétní skupiny.

Uživatele lze přidat do jedné nebo více skupin. Výstupní vazbu *signálu* můžete také použít k přidání nebo odebrání uživatelů do nebo ze skupin.

Další informace najdete v odkazu na [výstupní vazbu *signálu* ](../azure-functions/functions-bindings-signalr-service-output.md).

### <a name="signalr-hubs"></a>Rozbočovače signálu

Návěstí má koncept "centra". Každé připojení klienta a každá zpráva odeslaná z Azure Functions je vymezena na konkrétní centrum. Můžete použít centra jako způsob, jak oddělit vaše připojení a zprávy do logických oborů názvů.

## <a name="class-based-model"></a>Model založený na třídě

Model založený na třídě je vyhrazen pro C#. Model založený na třídě může mít konzistentní prostředí pro programování na straně serveru signalizace. Má následující funkce.

* Méně práce s konfigurací: název třídy se používá jako `HubName` , název metody se používá jako `Event` a `Category` automaticky se určuje podle názvu metody.
* Automatická vazba parametrů: `ParameterNames` není ani atribut `[SignalRParameter]` není potřeba. Parametry jsou automaticky vázány na argumenty metody Azure Function v daném pořadí.
* Pohodlné prostředí pro výstup a vyjednávání.

Následující kódy demonstrují tyto funkce:

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("negotiate")]
    public SignalRConnectionInfo Negotiate([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req)
    {
        return Negotiate(req.Headers["x-ms-signalr-user-id"], GetClaims(req.Headers["Authorization"]));
    }

    [FunctionName(nameof(OnConnected))]
    public async Task OnConnected([SignalRTrigger]InvocationContext invocationContext, ILogger logger)
    {
        await Clients.All.SendAsync(NewConnectionTarget, new NewConnection(invocationContext.ConnectionId));
        logger.LogInformation($"{invocationContext.ConnectionId} has connected");
    }

    [FunctionName(nameof(Broadcast))]
    public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        await Clients.All.SendAsync(NewMessageTarget, new NewMessage(invocationContext, message));
        logger.LogInformation($"{invocationContext.ConnectionId} broadcast {message}");
    }

    [FunctionName(nameof(OnDisconnected))]
    public void OnDisconnected([SignalRTrigger]InvocationContext invocationContext)
    {
    }
}
```

Všechny funkce, které chtějí využít model založený na třídách, musí být metodou třídy, která dědí z **ServerlessHub**. Název třídy `SignalRTestHub` v ukázce je název centra.

### <a name="define-hub-method"></a>Definovat metodu centra

Všechny metody centra **musí** mít argument `InvocationContext` dekorované podle `[SignalRTrigger]` atributů a používat konstruktor bez parametrů. Pak se **název metody** považuje za **událost**parametru.

Ve výchozím nastavení, `category=messages` s výjimkou názvu metody, je jedním z následujících názvů:

* **Připojeno**: zpracovává se jako `category=connections, event=connected`
* **Odpojeno**: zpracovává se jako `category=connections, event=disconnected`

### <a name="parameter-binding-experience"></a>Prostředí vazeb parametrů

V modelu založeném na třídě `[SignalRParameter]` není nutné, protože všechny argumenty jsou označeny jako `[SignalRParameter]` výchozí, s výjimkou jedné z následujících situací:

* Argument je upraven atributem vazby.
* Typ argumentu je `ILogger` nebo `CancellationToken`
* Argument je upraven podle atributu. `[SignalRIgnore]`

### <a name="negotiate-experience-in-class-based-model"></a>Vyjednávat prostředí v modelu založeném na třídě

Místo použití vstupní vazby signálů `[SignalR]` může být vyjednávání v modelu založeném na třídě flexibilnější. Základní třída `ServerlessHub` obsahuje metodu.

```cs
SignalRConnectionInfo Negotiate(string userId = null, IList<Claim> claims = null, TimeSpan? lifeTime = null)
```

Tato funkce uživatel přizpůsobuje `userId` nebo `claims` během provádění funkce.

## <a name="use-signalrfilterattribute"></a>Použití `SignalRFilterAttribute`

Uživatel může dědit a implementovat abstraktní třídu `SignalRFilterAttribute` . Pokud jsou výjimky vyvolány v `FilterAsync` , `403 Forbidden` budou odeslány zpět klientům.

Následující příklad ukazuje, jak implementovat filtr zákazníka, který umožňuje `admin` vyvolání pouze `broadcast` .

```cs
[AttributeUsage(AttributeTargets.Method, AllowMultiple = true, Inherited = true)]
internal class FunctionAuthorizeAttribute: SignalRFilterAttribute
{
    private const string AdminKey = "admin";

    public override Task FilterAsync(InvocationContext invocationContext, CancellationToken cancellationToken)
    {
        if (invocationContext.Claims.TryGetValue(AdminKey, out var value) &&
            bool.TryParse(value, out var isAdmin) &&
            isAdmin)
        {
            return Task.CompletedTask;
        }

        throw new Exception($"{invocationContext.ConnectionId} doesn't have admin role");
    }
}
```

Využijte atribut k autorizaci funkce.

```cs
[FunctionAuthorize]
[FunctionName(nameof(Broadcast))]
public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
{
}
```

## <a name="client-development"></a>Vývoj klientů

Klientské aplikace pro signalizaci mohou využívat sadu SDK pro klienty signalizace v jednom z několika jazyků ke snadnému připojení a příjmu zpráv ze služby Azure Signal.

### <a name="configuring-a-client-connection"></a>Konfigurace připojení klienta

Aby bylo možné se připojit ke službě Signaler, klient musí dokončit úspěšné vyjednávání připojení, které se skládá z těchto kroků:

1. Pokud chcete získat platné informace o připojení, vytvořte žádost na koncový bod *Negotiate* http popsanou výše.
1. Připojení ke službě Signaler pomocí adresy URL koncového bodu služby a přístupového tokenu získaného z koncového bodu *Negotiate*

Klientské sady SDK pro Signal už obsahují logiku potřebnou k provedení metody handshake pro vyjednávání. Předejte adresu URL koncového bodu Negotiate, mínus `negotiate` segment, do sady SDK `HubConnectionBuilder` . Tady je příklad v JavaScriptu:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Podle konvence sada SDK automaticky připojí `/negotiate` k adrese URL a použije ji k zahájení vyjednávání.

> [!NOTE]
> Pokud v prohlížeči používáte JavaScript nebo TypeScript SDK, musíte ve svém Function App [Povolit sdílení prostředků mezi zdroji (CORS)](#enabling-cors) .

Další informace o tom, jak používat klientskou sadu SDK pro signalizaci, najdete v dokumentaci k vašemu jazyku:

* [.NET Standard](/aspnet/core/signalr/dotnet-client)
* [JavaScript](/aspnet/core/signalr/javascript-client)
* [Java](/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Posílání zpráv od klienta ke službě

Pokud máte pro svůj prostředek signalizace nakonfigurované [nadřazené](concept-upstream.md) služby, můžete odesílat zprávy od klienta k vašemu Azure Functions pomocí libovolného klienta signalizace. Tady je příklad v JavaScriptu:

```javascript
connection.send('method1', 'arg1', 'arg2');
```

## <a name="azure-functions-configuration"></a>Konfigurace Azure Functions

Aplikace Azure Functions, které se integrují se službou Azure Signal Service, se dají nasadit jako jakákoli Typická aplikace Function App, jako je například [průběžné nasazování](../azure-functions/functions-continuous-deployment.md), [nasazování zip](../azure-functions/deployment-zip-push.md)a [spouštění z balíčku](../azure-functions/run-functions-from-deployment-package.md).

Existuje však několik zvláštních doporučení pro aplikace, které používají vazby služby signalizace. Pokud klient běží v prohlížeči, musí být povolená CORS. A pokud aplikace vyžaduje ověření, můžete pomocí App Service ověřování integrovat koncový bod Negotiate.

### <a name="enabling-cors"></a>Povolení CORS

Klient jazyka JavaScript/TypeScript zpřístupňuje vyjednávání připojení požadavky HTTP na funkci Negotiate. Když je klientská aplikace hostovaná v jiné doméně než aplikace funkce Azure, musí být ve Function App povolená možnost sdílení prostředků mezi zdroji (CORS), jinak se požadavky zablokuje v prohlížeči.

#### <a name="localhost"></a>Místního

Při spuštění aplikace Function App na místním počítači můžete přidat `Host` část, do které se *local.settings.js* pro povolení CORS. V `Host` části přidejte dvě vlastnosti:

* `CORS` -Zadejte základní adresu URL, která je zdrojem klientské aplikace.
* `CORSCredentials` – nastavte ho tak, aby `true` povoloval žádosti "withCredentials".

Příklad:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>Cloud – Azure Functions CORS

Pokud chcete povolit CORS ve službě Azure Function App, v Azure Portal na kartě *funkce platformy* ve vaší aplikaci Function App otevřete obrazovku konfigurace CORS.

> [!NOTE]
> Konfigurace CORS ještě není k dispozici v plánu využití Azure Functions Linux. K povolení CORS použijte [Azure API Management](#cloud---azure-api-management) .

CORS s oprávněním Access-Control-Allow-Credentials musí být povolena pro klienta signalizace volání funkce Negotiate. Zaškrtněte políčko, které chcete povolit.

V části *Povolené zdroje* přidejte položku se základní adresou URL vaší webové aplikace.

![Konfigurace CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Cloud – API Management Azure

Azure API Management poskytuje bránu rozhraní API, která přidává funkce do stávajících back-endové služby. Můžete ji použít k přidání CORS do aplikace Function App. Nabízí úroveň spotřeby s cenami plateb za akci a měsíčního grantu zdarma.

Informace o tom, jak [importovat aplikaci funkcí Azure Functions](../api-management/import-function-app-as-api.md), najdete v dokumentaci k API Management. Po importu můžete přidat příchozí zásadu a povolit CORS s podporou přístupu-řízení-povolit-přihlašovací údaje.

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

Nakonfigurujte klienty signalizace, aby používali API Management URL.

### <a name="using-app-service-authentication"></a>Použití ověřování App Service

Azure Functions má integrované ověřování, které podporuje oblíbené poskytovatele, jako je Facebook, Twitter, účet Microsoft, Google a Azure Active Directory. Tato funkce se dá integrovat s vazbou *SignalRConnectionInfo* k vytváření připojení ke službě Azure Signal, která byla ověřena pro ID uživatele. Vaše aplikace může odesílat zprávy pomocí výstupní vazby *signálu* , na kterou cílí toto ID uživatele.

V Azure Portal na kartě *funkce platformy* Function App otevřete okno nastavení *ověřování/autorizace* . Podle dokumentace pro [App Service ověřování](../app-service/overview-authentication-authorization.md) nakonfigurujte ověřování pomocí poskytovatele identity dle vašeho výběru.

Po nakonfigurování budou ověřené požadavky HTTP zahrnovat `x-ms-client-principal-name` a `x-ms-client-principal-id` hlavičky obsahující uživatelské jméno ověřené identity a ID uživatele v uvedeném pořadí.

Pomocí těchto hlaviček v konfiguraci vazby *SignalRConnectionInfo* můžete vytvářet ověřená připojení. Zde je příklad funkce Negotiate jazyka C#, která používá `x-ms-client-principal-id` hlavičku.

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

Pak můžete poslat zprávy tomuto uživateli nastavením `UserId` vlastnosti zprávy signálu.

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

Informace o dalších jazycích najdete v tématu [vazby služby signálu Azure](../azure-functions/functions-bindings-signalr-service.md) pro Azure Functions reference.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s postupem vývoje a konfigurace aplikací služby signalizace bez serveru pomocí Azure Functions. Zkuste vytvořit aplikaci sami pomocí některého z rychlých startů nebo kurzů na [stránce Přehled služby Signal](index.yml).