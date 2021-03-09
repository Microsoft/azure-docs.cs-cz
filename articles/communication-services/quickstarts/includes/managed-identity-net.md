---
ms.openlocfilehash: 11b10817959a390b4ea0215d72f97513a6b23345
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486581"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Přidání spravované identity do řešení komunikačních služeb (.NET)

### <a name="install-the-client-library-packages"></a>Instalace balíčků klientské knihovny

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Použití balíčků klientské knihovny

Přidejte následující `using` direktivy do kódu, abyste mohli použít klientské knihovny Azure identity a Azure Storage.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
```

Níže uvedené příklady používají rozhraní [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Tato pověření jsou vhodná pro produkční a vývojové prostředí.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`a `AZURE_TENANT_ID` proměnné prostředí jsou potřeba k vytvoření `DefaultAzureCredential` objektu. Pokud chcete vytvořit registrovanou aplikaci ve vývojovém prostředí a nastavit proměnné prostředí, přečtěte si téma [autorizace přístupu pomocí spravované identity](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Vytvoření identity a vydání tokenu se spravovanou identitou

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby s Azure Active Directory tokeny.

Pak použijte klienta k vydání tokenu pro nového uživatele:

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndGetTokenAsync(Uri resourceEdnpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();

          var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Odeslání SMS pomocí spravované identity

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby SMS se spravovanou identitou a pak pomocí klienta odeslat zprávu SMS:

```csharp
     public async Task SendSms(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

