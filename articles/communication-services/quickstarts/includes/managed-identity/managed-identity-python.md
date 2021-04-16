---
ms.openlocfilehash: 3fcfb364ebffdd3643e803922cbe4f3dd0d87935
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510991"
---
## <a name="setting-up"></a>Nastavení

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Otevřete okno terminálu nebo příkazového řádku pro svoji aplikaci vytvořte nový adresář a přejděte do něj.

```console
mkdir managed-identity-quickstart && cd managed-identity-quickstart
```

### <a name="install-the-sdk-packages"></a>Instalace balíčků sady SDK

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="create-a-new-file"></a>Vytvoření nového souboru
Otevřete a uložte nový soubor v vytvořené složce `managed-identity.py` s názvem. do tohoto souboru umístíme náš kód.

### <a name="use-the-sdk-packages"></a>Použití balíčků sady SDK

Přidejte následující `import` příkazy do horní části souboru, abyste mohli používat sady SDK, které jsme nainstalovali.

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient
```

### <a name="create-a-defaultazurecredential"></a>Vytvoření DefaultAzureCredential

Budeme používat [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential). Tato pověření jsou vhodná pro produkční a vývojové prostředí. Jak ho budeme používat v rámci tohoto rychlého startu, vytvoříme ho v horní části souboru.

```python
     credential = DefaultAzureCredential()
```

## <a name="create-an-identity-and-issue-a-token-with-managed-identities"></a>Vytvoření identity a vydání tokenu se spravovanými identitami

Nyní přidáme kód, který používá vytvořené přihlašovací údaje, k vydání přístupového tokenu VoIP. Tento kód budeme volat později:

```python
def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-managed-identities"></a>Odeslání SMS pomocí spravovaných identit
Jako další příklad používání spravovaných identit přidáme tento kód, který používá stejné přihlašovací údaje k odeslání SMS:

```python
def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

## <a name="write-our-main-code"></a>Zápis našeho hlavního kódu

Díky našim vytvořeným funkcím teď můžeme zapsat hlavní kód, který bude volat funkce, které jsme dříve napsali.

```python
# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Managed Identities");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Managed Identities");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Managed Identities");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```

Konečný `managed-identity.py` soubor by měl vypadat přibližně takto:

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient

credential = DefaultAzureCredential()

def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     response = sms_client.send(
          from_=from_phone_number,
          to=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
     return response

# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Managed Identities");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Managed Identities");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Managed Identities");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```
## <a name="run-the-program"></a>Spuštění programu

Vše, co je všechno hotové, můžete soubor spustit tak, že zadáte `python managed-identity.py` z adresáře projektu. V případě, že vše se dobře zvýšilo, měli byste vidět něco podobného jako následující.

```Bash
    $ python managed-identity.py
    Retrieving new Access Token, using Managed Identities
    Retrieved Access Token: ey...Q
    Sending SMS using Managed Identities
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
