---
ms.openlocfilehash: c642b0e5f459b2412bca6588c8ae625142f0f59f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450533"
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

Snadný způsob, jak přejít na použití spravovaného ověřování identity, najdete v tématu [autorizace přístupu pomocí spravované identity](../managed-identity-from-cli.md) .

Podrobnější informace o tom, jak objekt DefaultAzureCredential funguje a jak ho můžete používat způsobem, který není uvedený v tomto rychlém startu, najdete v tématu [Klientská knihovna Azure identity pro Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme) .

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

### <a name="list-all-your-purchased-phone-numbers"></a>Vypsat všechna Vaše zakoupená telefonní čísla

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby se službou Azure Managed identity, pak pomocí klienta Zobrazit všechna zakoupená telefonní čísla, která má prostředek:

```python
from azure.communication.phonenumbers import PhoneNumbersClient

def list_purchased_phone_numbers(resource_endpoint):
     credential = DefaultAzureCredential()
     phone_numbers_client = PhoneNumbersClient(resource_endpoint, credential)

     return phone_numbers_client.list_purchased_phone_numbers()
```