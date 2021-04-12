---
ms.openlocfilehash: c1d19b5b37a60914c1d7f2a2e42cd387bd030583
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106125814"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) verze 8 nebo vyšší.
- [Apache Maven](https://maven.apache.org/download.cgi)
- Nasazený prostředek komunikačních služeb a připojovací řetězec. [Vytvořte prostředek služby Communications](../create-communication-resource.md).

## <a name="add-managed-identity-to-your-communication-services-solution-java"></a>Přidání spravované identity do řešení komunikačních služeb (Java)

### <a name="install-the-sdk-packages"></a>Instalace balíčků sady SDK
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

### <a name="use-the-sdk-packages"></a>Použití balíčků sady SDK

Přidejte následující `import` direktivy do kódu, aby bylo možné používat sady Azure identity a sady Communications SDK.

```java
import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.core.credential.*;
import com.azure.identity.*;

import java.io.IOException;
import java.util.*;
```

Níže uvedené příklady používají rozhraní [DefaultAzureCredential](/java/api/com.azure.identity.defaultazurecredential). Tato pověření jsou vhodná pro produkční a vývojové prostředí.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`a `AZURE_TENANT_ID` proměnné prostředí jsou potřeba k vytvoření `DefaultAzureCredential` objektu. Pokud chcete vytvořit registrovanou aplikaci ve vývojovém prostředí a nastavit proměnné prostředí, přečtěte si téma [autorizace přístupu pomocí spravované identity](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Vytvoření identity a vydání tokenu se spravovanou identitou

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby se spravovanou identitou.
Pak použijte klienta k vydání tokenu pro nového uživatele:

```java
     public AccessToken createIdentityAndGetTokenAsync() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
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

          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .buildClient();

          // Send the message and check the response for a message id
          SmsSendResult response = smsClient.send(
               "<from-phone-number>",
               "<to-phone-number>",
               "your message"
          );
          return response;
    }
```

