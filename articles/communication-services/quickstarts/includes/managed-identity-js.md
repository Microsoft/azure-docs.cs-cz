---
ms.openlocfilehash: 0d45e04bbafc7b2480abdcb2ab21bdb219a09b9e
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021044"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Přidání spravované identity do řešení komunikačních služeb (JS)

### <a name="install-the-client-library-packages"></a>Instalace balíčků klientské knihovny

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-client-library-packages"></a>Použití balíčků klientské knihovny

Přidejte následující `import` direktivy do kódu, abyste mohli použít klientské knihovny Azure identity a Azure Storage.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { SmsClient, SmsSendRequest } from "@azure/communication-sms";
```

Níže uvedené příklady používají rozhraní [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential). Tato pověření jsou vhodná pro produkční a vývojové prostředí.

Pokud chcete zaregistrovat aplikaci ve vývojovém prostředí a nastavit proměnné prostředí, přečtěte si téma [autorizace přístupu pomocí spravované identity](../managed-identity-from-cli.md) .  

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Vytvoření identity a vydání tokenu se spravovanou identitou

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby se spravovanou identitou a potom použít klienta k vydání tokenu pro nového uživatele:

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserWithToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>Odeslání SMS pomocí spravované identity

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby se spravovanou identitou a pak pomocí klienta odeslat zprávu SMS:

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SmsSendRequest = { 
          from: fromNumber, 
          to: [toNumber], 
          message: message 
     };

     const response = await smsClient.send(
          sendRequest, 
          {} //Optional SendOptions
          );
}
```

