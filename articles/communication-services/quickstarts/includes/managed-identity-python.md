---
ms.openlocfilehash: cefdf77052e559853cc85d129799e288032186b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645444"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Přidání spravované identity do řešení komunikačních služeb

### <a name="install-the-sdk-packages"></a>Instalace balíčků sady SDK

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-sdk-packages"></a>Použití balíčků sady SDK

Přidejte následující `import` kód do kódu pro použití identity Azure.

```python
from azure.identity import DefaultAzureCredential
```

Níže uvedené příklady používají rozhraní [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential). Tato pověření jsou vhodná pro produkční a vývojové prostředí.

Pokud chcete zaregistrovat aplikaci ve vývojovém prostředí a nastavit proměnné prostředí, přečtěte si téma [autorizace přístupu pomocí spravované identity](../managed-identity-from-cli.md) .

### <a name="create-an-identity-and-issue-a-token"></a>Vytvoření identity a vydání tokenu

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby se spravovanou identitou a potom použít klienta k vydání tokenu pro nového uživatele:

```python
from azure.communication.identity import CommunicationIdentityClient 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Odeslání SMS pomocí spravované identity Azure

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby se službou Azure Managed identity a pak pomocí klienta odeslat zprávu SMS:

```python
from azure.communication.sms import SmsClient

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```
