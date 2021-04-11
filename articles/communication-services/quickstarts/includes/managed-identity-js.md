---
ms.openlocfilehash: a3771a21914831f249696fc3d733c5ea935c2c7e
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251526"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Přidání spravované identity do řešení komunikačních služeb (JS)

### <a name="install-the-sdk-packages"></a>Instalace balíčků sady SDK

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-sdk-packages"></a>Použití balíčků sady SDK

Přidejte následující `import` direktivy do kódu, abyste mohli používat Azure identity a sady Azure Storage SDK.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient, CommunicationUserToken } from "@azure/communication-identity";
import { SmsClient, SmsSendRequest } from "@azure/communication-sms";
```

Níže uvedené příklady používají rozhraní [DefaultAzureCredential](/javascript/api/@azure/identity/defaultazurecredential). Tato pověření jsou vhodná pro produkční a vývojové prostředí.

Snadný způsob, jak přejít na použití spravovaného ověřování identity, najdete v tématu [autorizace přístupu pomocí spravované identity](../managed-identity-from-cli.md) .

Podrobnější informace o tom, jak objekt DefaultAzureCredential funguje a jak ho můžete používat způsobem, který není uvedený v tomto rychlém startu, najdete v tématu [Klientská knihovna Azure identity pro JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme) .

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Vytvoření identity a vydání tokenu se spravovanou identitou

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby se spravovanou identitou a potom použít klienta k vydání tokenu pro nového uživatele:

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserAndToken(["chat"]);
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