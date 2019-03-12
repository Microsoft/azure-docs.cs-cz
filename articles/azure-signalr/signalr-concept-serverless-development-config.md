---
title: Vývoj a konfigurace aplikací služby Azure Functions SignalR
description: Podrobnosti o tom, jak vyvíjet a konfigurovat aplikace bez serveru v reálném čase pomocí Azure Functions a služby Azure SignalR
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: 9b68b9d0bbac984c29759cf4b7b026a559a9d819
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569143"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure Functions vývoje a konfigurace pomocí služby Azure SignalR

Aplikace Azure Functions můžete využít [vazby služby Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) to k přidávání funkcí v reálném čase. Klientské aplikace pomocí klientské sady SDK k dispozici v několika jazycích k připojení ke službě Azure SignalR a přijímání zpráv v reálném čase.

Tento článek popisuje koncepty pro vývoj a konfigurace aplikaci funkcí Azure je integrovaná se sadou služby SignalR.

## <a name="signalr-service-configuration"></a>Konfigurace služby SignalR

Službě Azure SignalR je možné nakonfigurovat různými způsoby. Při použití s Azure Functions, služba se musí nakonfigurovat na *bez serveru* režimu.

Na webu Azure Portal, vyhledejte *nastavení* stránku vašeho prostředku služby SignalR. Nastavte *režim služby* k *bez serveru*.

![Režim služby SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Vývoj pro Azure Functions

Aplikace bez serveru v reálném čase vytvořených pomocí Azure Functions a služby Azure SignalR obvykle vyžaduje dvě funkce Azure:

* Funkce "vyjednat", která volá klient získat platný služby SignalR přístupový token a adresu URL koncového bodu služby
* Jednu nebo více funkcí, které odesílání zpráv nebo spravovat členství ve skupině

### <a name="negotiate-function"></a>vyjednávání – funkce

Klientská aplikace vyžaduje platný přístupový token se připojit ke službě Azure SignalR. Přístupový token může být anonymní nebo ověřené pro ID daného uživatele. Aplikace bez serveru služby SignalR vyžaduje koncový bod HTTP s názvem "vyjednat" získat token a další informace o připojení, jako je například adresa URL koncového bodu služby SignalR.

Použití protokolu HTTP aktivované funkce Azure functions a *SignalRConnectionInfo* vstupní vazby ke generování objektu informací o připojení. Funkce musí být trasu HTTP s `/negotiate`.

Další informace o tom, jak vytvořit funkci negotiate, najdete v článku [ *SignalRConnectionInfo* vstupní vazby odkazu](../azure-functions/functions-bindings-signalr-service.md#signalr-connection-info-input-binding).

Další informace o tom, jak vytvořit token pro ověření, naleznete [pomocí ověřování služby App Service](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Zasílání zpráv a Správa členství ve skupině

Použití *SignalR* výstupní vazby pro odesílání zpráv do klientů připojení ke službě Azure SignalR. Zprávy můžete vysílat pro všechny klienty, nebo jim můžete poslat na podmnožinu klienti, kteří jsou ověření pomocí ID konkrétního uživatele nebo byly přidány do konkrétní skupiny.

Můžete přidat uživatele do jedné nebo více skupin. Můžete také použít *SignalR* výstupní vazbu k přidání nebo odebrání uživatelů do a ze skupiny.

Další informace najdete v tématu [ *SignalR* vazby odkazu na výstup](../azure-functions/functions-bindings-signalr-service.md#signalr-output-binding).

### <a name="signalr-hubs"></a>Rozbočovače SignalR

Funkce SignalR používá koncept "centra". Každé připojení klienta a každá zpráva odeslaná ze služby Azure Functions je vymezen na konkrétní centrum. Hubs slouží jako způsob, jak oddělit připojení a zprávy do logických oborů názvů.

## <a name="client-development"></a>Vývoj pro klientské

SignalR klientské aplikace můžou využívat funkce SignalR klientskou sadou SDK v jednom z několika jazyků snadno připojit k a přijímat zprávy ze služby Azure SignalR.

### <a name="configuring-a-client-connection"></a>Konfigurace připojení klienta

Připojení ke službě SignalR, musíte klienta dokončit vyjednávání o úspěšném připojení, která se skládá z následujících kroků:

1. Vytvořit žádost o *vyjednávání* koncový bod HTTP probírali výše získat platné informace o připojení
1. Připojení k službě SignalR pomocí adresy URL koncového bodu služby a získat přístupový token z *vyjednávání* koncového bodu

SignalR klientské sady SDK už obsahují logiku potřebnou k provedení vyjednávání metody handshake. Předat adresu URL koncového bodu negotiate, mínus `negotiate` segmentu, sady SDK `HubConnectionBuilder`. Tady je příklad v jazyce JavaScript:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Podle konvence, sada SDK automaticky připojí `/negotiate` na adresu URL a použije ho k začít vyjednávání.

> [!NOTE]
> Pokud používáte sady SDK JavaScript/TypeScript v prohlížeči, budete muset [povolení prostředků různého původu (CORS) pro sdílení obsahu](#enabling-cors) na vaši aplikaci Function App.

Další informace o tom, jak používat funkci SignalR Klientská sada SDK, naleznete v dokumentaci pro váš jazyk:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [JavaScript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Odesílání zpráv z klienta do služby

Ačkoli SignalR SDK umožňuje získat klientským aplikacím k vyvolání back-end logiku do rozbočovače SignalR, tato funkce není dosud podporována při použití služby SignalR službou Azure Functions. Použití protokolu HTTP žádosti k vyvolání Azure Functions.

## <a name="azure-functions-configuration"></a>Konfigurace služby Azure Functions

Azure aplikace funkcí, které se integrují se službou Azure SignalR je nasadit stejně jako jakékoli Typická aplikace funkcí Azure, pomocí technik, jako [průběžné nasazení](../azure-functions/functions-continuous-deployment.md), [zip nasazení](../azure-functions/deployment-zip-push.md)a [spustit z balíčku](../azure-functions/run-functions-from-deployment-package.md).

Existují však několik speciální aspekty pro aplikace, které používají vazby služby SignalR. Pokud klient běží v prohlížeči, musí být povolená CORS. A pokud aplikace vyžaduje ověření, můžete integrovat negotiate koncový bod se ověřování pomocí služby App Service.

### <a name="enabling-cors"></a>Povolení CORS

JavaScript/TypeScript klient vytvoří požadavky HTTP na funkci negotiate k zahájení připojení vyjednávání. Když klientská aplikace hostována v jiné doméně než aplikace Azure Function app, prostředků mezi zdroji (CORS) pro sdílení obsahu musí být povolené na aplikaci funkcí nebo prohlížeč bude blokovat požadavky.

#### <a name="localhost"></a>localhost

Při spuštění aplikace Function app v místním počítači, můžete přidat `Host` části *local.settings.json* k povolení sdílení CORS. V `Host` části, přidejte dvě vlastnosti:

* `CORS` -Zadejte základní adresu URL, který je původcem klientské aplikace
* `CORSCredentials` – Nastavte na `true` "withCredentials" požadavků

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

#### <a name="azure"></a>Azure

Povolení CORS na aplikaci funkcí Azure, přejděte na obrazovku pro konfiguraci CORS v části *funkce platformy* kartu aplikace Function App na webu Azure Portal.

CORS s přístup – ovládací prvek-Allow-Credentials musí být povolena pro funkci SignalR klientovi umožní zavolat funkci negotiate. Zaškrtněte políčko, aby je.

V *povolené zdroje* části, přidejte záznam s původní základní adresu URL webové aplikace.

![Konfigurace CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

### <a name="using-app-service-authentication"></a>Použití ověřování pomocí služby App Service

Azure Functions nabízí integrované ověřování podporuje oblíbené poskytovatele, jako je Facebook, Twitter, Account Microsoft, Google a Azure Active Directory. Tato funkce je možné integrovat se službou *SignalRConnectionInfo* vazby připojení k službě Azure SignalR ověřené ID uživatele. Vaše aplikace může odesílat zprávy pomocí *SignalR* výstupní vazby, které jsou cíleny na tuto ID uživatele.

Na webu Azure Portal, v aplikaci Function app *funkce platformy* otevřenou kartou *ověřování/autorizace* okno nastavení. Postupujte podle dokumentace pro [ověřování pomocí služby App Service](../app-service/overview-authentication-authorization.md) postup konfigurace ověřování pomocí zprostředkovatelů identity podle vašeho výběru.

Po nakonfigurování ověřených požadavků protokolu HTTP bude obsahovat `x-ms-client-principal-name` a `x-ms-client-principal-id` hlavičky, který obsahuje ověřená identita uživatelské jméno a ID uživatele.

Můžete použít tyto hlavičky v vaše *SignalRConnectionInfo* konfigurace vazby ověřené připojení. Tady je příklad C# vyjednávání funkci, která používá `x-ms-client-principal-id` záhlaví.

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

Pak můžete odesílat zprávy pro daného uživatele tak, že nastavíte `UserId` vlastnost zpráv SignalR.

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

Informace v jiných jazycích najdete v tématu [vazby služby Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) pro Azure Functions odkazovat.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak vyvíjet a nakonfigurovat pomocí služby Azure Functions aplikace bez serveru služby SignalR. Zkuste vytvořit aplikaci sami pomocí jednoho z kurzů nebo rychlých startů na [stránka s přehledem služby SignalR](index.yml).