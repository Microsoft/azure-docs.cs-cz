---
ms.openlocfilehash: 0fd97fe0eebb23130513409698b75d2ee7b98a6f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657613"
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
import { SmsClient } from "@azure/communication-sms";
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
     const sendRequest: SendRequest = { 
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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o ověřování](../concepts/authentication.md)

Můžete také chtít:

- [Další informace o řízení přístupu na základě role v Azure](../../../../articles/role-based-access-control/index.yml)
- [Další informace o službě Azure identity Library pro JS](/javascript/api/overview/azure/identity-readme)
- [Vytváření tokenů přístupu uživatele](../../quickstarts/access-tokens.md)
- [Odeslání zprávy SMS](../../quickstarts/telephony-sms/send.md)
- [Další informace o SMS](../../concepts/telephony-sms/concepts.md)

