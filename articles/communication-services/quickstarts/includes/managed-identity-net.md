---
ms.openlocfilehash: 8a46e93efbc4435fdc48648f658d13982981812a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957979"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Přidání spravované identity do řešení komunikačních služeb (.NET)

### <a name="install-the-sdk-packages"></a>Instalace balíčků sady SDK

```console
dotnet add package Azure.Communication.Identity  --version 1.0.0
dotnet add package Azure.Communication.Sms  --version 1.0.0
dotnet add package Azure.Identity
```

### <a name="use-the-sdk-packages"></a>Použití balíčků sady SDK

Přidejte následující `using` direktivy do kódu, abyste mohli používat Azure identity a sady Azure Storage SDK.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
```

Níže uvedené příklady používají rozhraní [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Tato pověření jsou vhodná pro produkční a vývojové prostředí.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`a `AZURE_TENANT_ID` proměnné prostředí jsou potřeba k vytvoření `DefaultAzureCredential` objektu. Pokud chcete vytvořit registrovanou aplikaci ve vývojovém prostředí a nastavit proměnné prostředí, přečtěte si téma [autorizace přístupu pomocí spravované identity](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Vytvoření identity a vydání tokenu se spravovanou identitou

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby s Azure Active Directory tokeny.

Pak použijte klienta k vydání tokenu pro nového uživatele:

```csharp
     public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();

          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = client.CreateUser();
          var identity = identityResponse.Value;

          var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Odeslání SMS pomocí spravované identity

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby SMS se spravovanou identitou a pak pomocí klienta odeslat zprávu SMS:

```csharp
     public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          SmsSendResult sendResult = smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );

          return sendResult;
      }
```

