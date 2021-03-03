---
ms.openlocfilehash: 9da0cbc3777359994fac3778dcc126c844754861
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657614"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Přidání spravované identity do řešení komunikačních služeb

### <a name="install-the-client-library-packages"></a>Instalace balíčků klientské knihovny

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-client-library-packages"></a>Použití balíčků klientské knihovny

Přidejte následující `import` kód do kódu pro použití identity Azure.

```python
from azure.identity import DefaultAzureCredential
```

Níže uvedené příklady používají rozhraní [DefaultAzureCredential](/python/api/azure.identity.defaultazurecredential). Tato pověření jsou vhodná pro produkční a vývojové prostředí.

Pokud chcete zaregistrovat aplikaci ve vývojovém prostředí a nastavit proměnné prostředí, přečtěte si téma [autorizace přístupu pomocí spravované identity](../managed-identity-from-cli.md) .

### <a name="create-an-identity-and-issue-a-token"></a>Vytvoření identity a vydání tokenu

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby se spravovanou identitou a potom použít klienta k vydání tokenu pro nového uživatele:

```python
import azure.communication.identity 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Odeslání SMS pomocí spravované identity Azure

Následující příklad kódu ukazuje, jak vytvořit objekt klienta služby se službou Azure Managed identity a pak pomocí klienta odeslat zprávu SMS:

```python
from azure.communication.sms import (
    PhoneNumberIdentifier,
    SendSmsOptions,
    SmsClient
)

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_phone_number=PhoneNumberIdentitifier(from_phone_number),
          to_phone_numbers=[PhoneNumberIdentifier(to_phone_number)],
          message=message_content,
          send_sms_options=SendSmsOptions(enable_delivery_report=True))  # optional property
     )
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o ověřování](../../concepts/authentication.md)

Můžete také chtít:

- [Další informace o řízení přístupu na základě role v Azure](../../../../articles/role-based-access-control/index.yml)
- [Další informace o službě Azure identity Library pro Python](/net/api/overview/azure/identity-readme)
- [Vytváření tokenů přístupu uživatele](../../quickstarts/access-tokens.md)
- [Odeslání zprávy SMS](../../quickstarts/telephony-sms/send.md)
- [Další informace o SMS](../../concepts/telephony-sms/concepts.md)