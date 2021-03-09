---
ms.openlocfilehash: bb3925c5c642f2a6d00f8f5b7fe6471676c23c30
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486584"
---
## <a name="add-managed-identity-to-your-communication-services-solution-java"></a>Přidání spravované identity do řešení komunikačních služeb (Java)

### <a name="install-the-client-library-packages"></a>Instalace balíčků klientské knihovny
V souboru pom.xml přidejte následující prvky závislosti do skupiny závislostí.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="use-the-client-library-packages"></a>Použití balíčků klientské knihovny

Přidejte následující `import` direktivy do kódu, aby bylo možné použít klientské knihovny Azure identity a Azure Communications.

```java
import com.azure.identity.*;
import com.azure.communication.sms.*;
import com.azure.communication.identity.*;
import com.azure.communication.common.*;
```

Níže uvedené příklady používají rozhraní [DefaultAzureCredential](/java/api/azure.identity.defaultazurecredential). Tato pověření jsou vhodná pro produkční a vývojové prostředí.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`a `AZURE_TENANT_ID` proměnné prostředí jsou potřeba k vytvoření `DefaultAzureCredential` objektu. Pokud chcete vytvořit registrovanou aplikaci ve vývojovém prostředí a nastavit proměnné prostředí, přečtěte si téma [autorizace přístupu pomocí spravované identity](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Vytvoření identity a vydání tokenu se spravovanou identitou

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby se spravovanou identitou.
Pak použijte klienta k vydání tokenu pro nového uživatele:

```java
     public AccessToken createIdentityAndGetTokenAsync() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();
          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .httpClient(httpClient)
               .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          AccessToken userToken = communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
          return userToken;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Odeslání SMS pomocí spravované identity

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby se spravovanou identitou a pak pomocí klienta odeslat zprávu SMS:

```java
     public SendSmsResponse sendSms() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();
          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .httpClient(httpClient)
               .buildClient();

          // Send the message and check the response for a message id
          SendSmsResponse response = smsClient.sendMessage(
               new PhoneNumberIdentifier("<leased-phone-number>"),
               to,
               "your message",
               options /* Optional */
          );
          return response;
    }
```

