---
title: Řešení potíží v komunikačních službách Azure
description: Naučte se shromažďovat informace, které potřebujete k řešení problémů s řešením komunikačních služeb.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 10/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4921078e9e7b5d9de06fbbc9a97dc4a964569e04
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754741"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Řešení potíží v komunikačních službách Azure

Tento dokument vám pomůže shromáždit informace, které potřebujete k řešení problémů s řešením komunikačních služeb.

## <a name="getting-help"></a>Získání nápovědy

Doporučujeme vývojářům odesílat dotazy, navrhovat funkce a nahlásit problémy jako problémy v [úložišti GitHub](https://github.com/Azure/communication)komunikačních služeb. Mezi další fóra patří:

* [Microsoft – otázky a odpovědi](https://docs.microsoft.com/answers/questions/topics/single/101418.html)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure+communication)

V závislosti na [plánu podpory](https://azure.microsoft.com/support/plans/) předplatného Azure můžete odeslat lístek podpory přímo prostřednictvím [Azure Portal](https://azure.microsoft.com/support/create-ticket/).

Pro usnadnění odstraňování určitých typů problémů se může zobrazit výzva k zadání následujících informací:

* **MS-CV ID** : Toto ID se používá k odstraňování potíží s voláními a zprávami. 
* **ID volání** : Toto ID slouží k identifikaci volání komunikačních služeb.
* **ID zprávy SMS** : Toto ID slouží k identifikaci zpráv SMS.

## <a name="access-your-ms-cv-id"></a>Přístup k vašemu ID MS-CV

K ID MS-CV je možné přistupovat při inicializaci diagnostiky v `clientOptions` instanci objektu při inicializaci klientských knihoven. Diagnostiku můžete nakonfigurovat pro kteroukoli z klientských knihoven Azure, včetně konverzace, správy a volání VoIP.

### <a name="client-options-example"></a>Příklad možností klienta

Následující fragmenty kódu ukazují konfiguraci diagnostiky. Pokud jsou klientské knihovny použity s povolenou diagnostikou, budou diagnostické informace generovány do nakonfigurovaného naslouchacího procesu událostí:

# <a name="c"></a>[C#](#tab/csharp)
``` 
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions 
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
``` 
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>Přístup k ID volání

Při podání žádosti o podporu prostřednictvím Azure Portal souvisejícím s voláním problémů můžete být požádáni o zadání ID volání, na které odkazujete. K tomuto je možné přistupovat prostřednictvím volající klientské knihovny:

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods 
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends 
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId) 
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods 
Log.d(call.getCallId()) 
```
---


## <a name="access-your-sms-message-id"></a>Přístup k ID zprávy SMS

V případě problémů se serverem SMS můžete shromáždit ID zprávy z objektu Response.

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="related-information"></a>Související informace
- [Protokoly a diagnostika](logging-and-diagnostics.md)
- [Metriky](metrics.md)
