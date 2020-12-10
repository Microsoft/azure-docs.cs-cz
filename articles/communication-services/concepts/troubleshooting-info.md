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
ms.openlocfilehash: 88ad060c1ba28285051a91bd928a2a7116dff1ce
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937538"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Řešení potíží v komunikačních službách Azure

Tento dokument vám pomůže řešit problémy, se kterými se můžete setkat v rámci řešení komunikačních služeb. Pokud řešíte řešení potíží s SMS, můžete [Povolit vytváření sestav doručení pomocí Event Grid](../quickstarts/telephony-sms/handle-sms-events.md) pro zachycení podrobností o doručení SMS.

## <a name="getting-help"></a>Získání nápovědy

Doporučujeme vývojářům odesílat dotazy, navrhovat funkce a nahlásit problémy jako problémy v [úložišti GitHub](https://github.com/Azure/communication)komunikačních služeb. Mezi další fóra patří:

* [Microsoft – otázky a odpovědi](/answers/questions/topics/single/101418.html)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure+communication)

V závislosti na [plánu podpory](https://azure.microsoft.com/support/plans/) předplatného Azure můžete odeslat lístek podpory přímo prostřednictvím [Azure Portal](https://azure.microsoft.com/support/create-ticket/).

Pro usnadnění odstraňování určitých typů problémů se může zobrazit výzva k zadání následujících informací:

* **MS-CV ID**: Toto ID se používá k odstraňování potíží s voláními a zprávami. 
* **ID volání**: Toto ID slouží k identifikaci volání komunikačních služeb.
* **ID zprávy SMS**: Toto ID slouží k identifikaci zpráv SMS.
* **Protokoly volání**: tyto protokoly obsahují podrobné informace, které lze použít k řešení problémů s voláním a sítí.


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

## <a name="enable-and-access-call-logs"></a>Povolení a přístup k protokolům volání




# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující kód lze použít ke konfiguraci `AzureLogger` pro výstup protokolů do konzoly pomocí klientské knihovny JavaScriptu:

```javascript
import { AzureLogger } from '@azure/logger'; 

AzureLogger.verbose = (...args) => { console.info(...args); } 
AzureLogger.info = (...args) => { console.info(...args); } 
AzureLogger.warning = (...args) => { console.info(...args); } 
AzureLogger.error = (...args) => { console.info(...args); } 

callClient = new CallClient({logger: AzureLogger}); 
```

# <a name="ios"></a>[iOS](#tab/ios)

Při vývoji pro iOS se protokoly ukládají do `.blog` souborů. Mějte na paměti, že protokoly nemůžete zobrazit přímo, protože jsou zašifrované.

K nim lze přistupovat otevřením Xcode. V zařízeních s Windows > a simulátorech > zařízení. Vyberte své zařízení. V části nainstalované aplikace vyberte svou aplikaci a klikněte na Stáhnout kontejner. 

To vám poskytne `xcappdata` soubor. Klikněte pravým tlačítkem na tento soubor a vyberte Zobrazit obsah balíčku. Pak uvidíte `.blog` soubory, které pak můžete připojit k žádosti o podporu Azure.

# <a name="android"></a>[Android](#tab/android)

Při vývoji pro Android se protokoly ukládají do `.blog` souborů. Mějte na paměti, že protokoly nemůžete zobrazit přímo, protože jsou zašifrované.

V Android Studio přejděte do Průzkumníka souborů zařízení tak, že vyberete Zobrazit > nástrojů Windows > Průzkumník souborů zařízení z simulátoru i ze zařízení. `.blog`Soubor se bude nacházet v adresáři vaší aplikace, který by měl vypadat nějak takto `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog` . Tento soubor můžete připojit k žádosti o podporu. 
   

---


## <a name="calling-client-library-error-codes"></a>Volání klientských chybových kódů klientské knihovny

Služba Azure Communication Services, která volá klientskou knihovnu, používá následující chybové kódy, které vám pomůžou vyřešit problémy s voláním. Tyto kódy chyb jsou zpřístupněny prostřednictvím `call.callEndReason` vlastnosti po ukončení volání.

| Kód chyby | Popis | Akce, která se má provést |
| -------- | ---------------| ---------------|
| 403 | Zakázáno/ověřování se nezdařilo. | Ujistěte se, že token komunikačních služeb je platný a nevypršela jeho platnost. |
| 404 | Volání nebylo nalezeno. | Ujistěte se, že počet volání (nebo volání, které se připojujete) existuje. |
| 408 | Vypršel časový limit kontroleru volání. | Řadiči volání vypršel časový limit při čekání na zprávy protokolu z koncových bodů uživatele. Ujistěte se, že jsou klienti připojení a jsou k dispozici. |
| 410 | Došlo k chybě místního zásobníku médií nebo mediální infrastruktury. | Ujistěte se, že používáte nejnovější klientskou knihovnu v podporovaném prostředí. |
| 430 | Nelze doručit zprávu do klientské aplikace. | Ujistěte se, že je klientská aplikace spuštěná a dostupná. |
| 480 | Koncový bod vzdáleného klienta není zaregistrován. | Ujistěte se, že je vzdálený koncový bod k dispozici. |
| 481 | Nepovedlo se zpracovat příchozí volání. | Zasouborujte žádost o podporu prostřednictvím Azure Portal. |
| 487 | Volání bylo zrušeno, místně odmítnuto, bylo ukončeno z důvodu problému s neshodou koncového bodu, nebo se nepodařilo vygenerovat nabídku médií. | Očekávané chování. |
| 490, 491, 496, 487, 498 | Problémy se sítí místního koncového bodu. | Ověřte svou síť. |
| 500, 503, 504 | Došlo k chybě infrastruktury komunikačních služeb. | Zasouborujte žádost o podporu prostřednictvím Azure Portal. |
| 603 | Volání globálně odmítnuto účastníkem vzdálené komunikační služby | Očekávané chování. |


## <a name="related-information"></a>Související informace
- [Protokoly a diagnostika](logging-and-diagnostics.md)
- [Metriky](metrics.md)