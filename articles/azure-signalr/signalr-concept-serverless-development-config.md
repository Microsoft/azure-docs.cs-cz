---
title: Vývoj & konfigurace aplikace Azure Functions – Azure SignalR
description: Podrobnosti o vývoji a konfiguraci aplikací v reálném čase bez serveru pomocí azure functions a služby Azure SignalR Service
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: e1157a695d34c75b237391427b37365421366ef8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523166"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Vývoj a konfigurace služby Azure Functions s využitím služby Azure SignalR Service

Aplikace Azure Functions [můžou využít vazby služby Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) k přidání funkcí v reálném čase. Klientské aplikace používají klientské sady SDK, které jsou k dispozici v několika jazycích, pro připojení ke službě Azure SignalR service a přijímání zpráv v reálném čase.

Tento článek popisuje koncepty pro vývoj a konfiguraci aplikace Azure Function, která je integrovaná se službou SignalR.

## <a name="signalr-service-configuration"></a>Konfigurace služby SignalR

Azure SignalR Service lze nakonfigurovat v různých režimech. Při použití s funkcemi Azure musí být služba nakonfigurována v režimu *bez serveru.*

Na webu Azure Portal vyhledejte stránku *Nastavení* prostředku služby SignalR. Nastavte *režim služby* na *bezserverový režim*.

![Servisní režim SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Vývoj s využitím Azure Functions

Bezserverová aplikace v reálném čase vytvořená a s využitím Azure Functions a Azure SignalR Service zpravidla vyžaduje dvě funkce Azure Functions:

* Funkci „negotiate“, kterou klient volá k získání adresy URL koncového bodu a platného přístupového tokenu služby SignalR Service
* Jednu nebo několik funkcí, které odesílají zprávy nebo spravují členství ve skupinách

### <a name="negotiate-function"></a>vyjednat funkci

Klientská aplikace vyžaduje platný přístupový token pro připojení ke službě Azure SignalR. Přístupový token může být anonymní nebo ověřený na dané ID uživatele. Aplikace služby SignalR bez serveru vyžadují koncový bod HTTP s názvem "negotiate" k získání tokenu a dalších informací o připojení, jako je například adresa URL koncového bodu služby SignalR.

Ke generování objektu informací o připojení použijte funkci Azure aktivovanou protokolem HTTP a vstupní vazbu *SignalRConnectionInfo.* Funkce musí mít trasu HTTP, která končí v `/negotiate`.

Další informace o tom, jak vytvořit funkci negotiate, naleznete v [odkazu na vstupní vazbu *SignalRConnectionInfo* ](../azure-functions/functions-bindings-signalr-service-input.md).

Informace o tom, jak vytvořit ověřený token, naleznete [v odkazech Using App Service Authentication](#using-app-service-authentication).

### <a name="sending-messages-and-managing-group-membership"></a>Odesílání zpráv a správa členství ve skupině

Pomocí výstupní vazby *SignalR* odesílejte zprávy klientům připojeným ke službě Azure SignalR. Zprávy můžete vysílat všem klientům nebo je můžete odeslat podmnožině klientů, kteří jsou ověřeni pomocí určitého ID uživatele nebo byli přidáni do určité skupiny.

Uživatelé mohou být přidáni do jedné nebo více skupin. Výstupní vazbu *SignalR* můžete také použít k přidání nebo odebrání uživatelů do nebo ze skupin.

Další informace naleznete v [odkazu na vazbu výstupu *SignalR* ](../azure-functions/functions-bindings-signalr-service-output.md).

### <a name="signalr-hubs"></a>Rozbočovače SignalR

SignalR má koncept "rozbočovačů". Každé připojení klienta a každá zpráva odeslaná z Funkce Azure je vymezena do konkrétního rozbočovače. Rozbočovače můžete použít jako způsob, jak oddělit připojení a zprávy do logických oborů názvů.

## <a name="client-development"></a>Vývoj klientů

Klientské aplikace SignalR můžete využít Client SDK klienta SignalR v jednom z několika jazyků snadno připojit a přijímat zprávy ze služby Azure SignalR.

### <a name="configuring-a-client-connection"></a>Konfigurace připojení klienta

Chcete-li se připojit ke službě SignalR, musí klient dokončit úspěšné vyjednávání připojení, které se skládá z těchto kroků:

1. Vytvořte požadavek na výše popsaný koncový bod *protokolu HTTP pro získání* platných informací o připojení
1. Připojení ke službě SignalR pomocí adresy URL koncového bodu služby a přístupového tokenu získaného z *koncového* bodu vyjednávání

Sady SDK klienta SignalR již obsahují logiku potřebnou k provedení handshake vyjednávání. Předejte adresu URL koncového bodu `negotiate` vyjednávání bez segmentu do `HubConnectionBuilder`sady SDK . Zde je příklad v JavaScriptu:

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

Podle konvence sada SDK `/negotiate` automaticky připojí k adrese URL a používá ji k zahájení vyjednávání.

> [!NOTE]
> Pokud používáte javascriptovou/typescriptovou sdk v prohlížeči, musíte v aplikaci Function App [povolit sdílení prostředků mezi zdroji (CORS).](#enabling-cors)

Další informace o použití sady SDK klienta SignalR naleznete v dokumentaci k jazyku:

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [Javascript](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>Odesílání zpráv od klienta do služby

Přestože sada SignalR SDK umožňuje klientským aplikacím vyvolat back-endovou logiku v rozbočovači SignalR, tato funkce ještě není podporovaná, pokud používáte službu SignalR s funkcemi Azure. Pomocí požadavků HTTP k vyvolání funkce Azure.

## <a name="azure-functions-configuration"></a>Konfigurace Azure Functions

Aplikace Azure Function, které se integrují se službou Azure SignalR, se můžou nasazovat jako všechny typické aplikace Azure Function pomocí technik, jako je [průběžné nasazování](../azure-functions/functions-continuous-deployment.md), [nasazení zip](../azure-functions/deployment-zip-push.md)u [balíku](../azure-functions/run-functions-from-deployment-package.md).

Existují však několik zvláštních aspekty pro aplikace, které používají vazby služby SignalR. Pokud klient běží v prohlížeči, musí být povolen a musí být povolen. A pokud aplikace vyžaduje ověření, můžete integrovat koncový bod vyjednat s ověřováním služby App Service.

### <a name="enabling-cors"></a>Povolení CORS

Klient JavaScript/TypeScript provádí požadavky HTTP pro funkci negotiate, aby zahájil vyjednávání o připojení. Když je klientská aplikace hostovaná v jiné doméně než aplikace Azure Function, musí být v aplikaci Function povoleno sdílení prostředků mezi zdroji (CORS), jinak prohlížeč požadavky zablokuje.

#### <a name="localhost"></a>Localhost

Při spuštění aplikace Function v místním počítači `Host` můžete přidat oddíl na *local.settings.json* a povolit cors. V `Host` části přidejte dvě vlastnosti:

* `CORS`- zadejte základní ADRESU URL, která je původem klientské aplikace
* `CORSCredentials`- nastavte `true` jej tak, aby povoluje požadavky "withCredentials"

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

Pokud chcete povolit CORS v aplikaci Azure Function, přejděte na konfigurační obrazovku CORS na kartě *Funkce platformy* aplikace Function na webu Azure Portal.

> [!NOTE]
> Konfigurace CORS ještě není dostupná v plánu spotřeby Azure Functions Linux. K povolení CORS použijte [Azure API Management.](#cloud---azure-api-management)

Cors s přístup-control-allow-credentials musí být povolena pro klienta SignalR volat vyjednat funkci. Chcete-li povolit, zaškrtněte políčko.

V části *Povolený původ* přidejte položku se základní adresou URL webové aplikace.

![Konfigurace CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>Cloud – správa rozhraní Azure API

Azure API Management poskytuje bránu rozhraní API, která přidává funkce do stávajících back-endových služeb. Můžete ji použít k přidání CORS do aplikace funkce. Nabízí úroveň spotřeby s cenami za akci a měsíčním bezplatným grantem.

Informace o [importu aplikace Azure Function](../api-management/import-function-app-as-api.md)najdete v dokumentaci ke správě rozhraní API . Po importu můžete přidat příchozí zásady pro povolení CORS s podporou přístupu k řízení a povolení pověření.

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

Nakonfigurujte klienty SignalR tak, aby používali adresu URL pro správu rozhraní API.

### <a name="using-app-service-authentication"></a>Použití ověřování služby App Service

Azure Functions má integrované ověřování, které podporuje oblíbené poskytovatele, jako je Facebook, Twitter, účet Microsoft, Google a Azure Active Directory. Tuto funkci lze integrovat s vazby *SignalRConnectionInfo* k vytvoření připojení ke službě Azure SignalR, které byly ověřeny na ID uživatele. Aplikace můžete odesílat zprávy pomocí *signalr* výstupní vazby, které jsou zaměřeny na toto ID uživatele.

Na webu Azure Portal otevřete na kartě *Funkce aplikace platforma* okno *Nastavení ověřování/autorizace.* Podle dokumentace pro [ověřování služby App Service](../app-service/overview-authentication-authorization.md) nakonfigurujte ověřování pomocí zvoleného poskytovatele identity.

Po nakonfigurování budou ověřené `x-ms-client-principal-name` `x-ms-client-principal-id` požadavky HTTP obsahovat a záhlaví obsahující uživatelské jméno a ID uživatele ověřené identity.

Tyto hlavičky můžete použít v konfiguraci vazby *SignalRConnectionInfo* k vytvoření ověřených připojení. Zde je příklad C# vyjednat `x-ms-client-principal-id` funkci, která používá záhlaví.

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

Potom můžete odeslat zprávy tomuto `UserId` uživateli nastavením vlastnosti zprávy SignalR.

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

Informace o jiných jazycích najdete v [azby služby Azure SignalR](../azure-functions/functions-bindings-signalr-service.md) pro azure funkce odkaz.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak vyvíjet a konfigurovat aplikace služby SignalR bez serveru pomocí funkce Azure. Zkuste vytvořit aplikaci sami pomocí jednoho z rychlých spuštění nebo kurzů na [stránce přehled služby SignalR](index.yml).