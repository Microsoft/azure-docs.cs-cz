---
ms.openlocfilehash: 283d7d1c83dc4a68901c17c36b548e00e1044e34
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629340"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 nebo novější.
- Nasazený prostředek komunikačních služeb a připojovací řetězec. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Otevřete okno terminálu nebo příkazového řádku a pro svou aplikaci vytvořte nový adresář a pak na něj přejděte.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Pomocí textového editoru vytvořte soubor s názvem phone_numbers_sample. py v kořenovém adresáři projektu a přidejte následující kód. Do následujících částí přidáváme zbývající kód pro rychlý Start.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

try:
   print('Azure Communication Services - Phone Numbers Quickstart')
   # Quickstart code goes here
except Exception as ex:
   print('Exception:')
   print(ex)
```

### <a name="install-the-package"></a>Instalace balíčku

Stále v adresáři aplikace nainstalujte klientskou knihovnu pro správu služby Azure Communication Services pro balíček python pomocí `pip install` příkazu.

```console
pip install azure-communication-phone-numbers
```

## <a name="authenticate-the-phone-numbers-client"></a>Ověření klienta telefonních čísel

`PhoneNumbersClient`Je povoleno použít Azure Active Directory ověřování. Použití `DefaultAzureCredential` objektu je nejjednodušší způsob, jak začít s Azure Active Directory a můžete ho nainstalovat pomocí `pip install` příkazu. 

```console
pip install azure-identity
```

Vytvoření `DefaultAzureCredential` objektu vyžaduje, abyste měli `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` a `AZURE_TENANT_ID` už nastavené jako proměnné prostředí s odpovídajícími hodnotami z vaší registrované aplikace služby Azure AD.

Po instalaci `azure-identity` knihovny můžeme ověřování klienta i nadále používat.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure Portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
except Exception as ex:
    print('Exception:')
    print(ex)
```

Alternativně je překročila povolený pomocí koncového bodu a přístupového klíče z komunikačního prostředku k ověření také.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

# You can find your connection string from your resource in the Azure Portal
connection_string = 'https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<YOUR_ACCESS_KEY>'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    phone_numbers_client = PhoneNumbersClient.from_connection_string(connection_string)
except Exception as ex:
    print('Exception:')
    print(ex)
```

## <a name="functions"></a>Functions

Po `PhoneNumbersClient` ověření můžeme začít pracovat na různých funkcích, které může dělat.

### <a name="search-for-available-phone-numbers"></a>Vyhledat dostupná telefonní čísla

Aby bylo možné koupit telefonní čísla, je třeba nejprve vyhledat všechna dostupná telefonní čísla. Pokud chcete vyhledat telefonní čísla, zadejte směrové číslo oblasti, typ přiřazení, [Možnosti telefonního čísla](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [typ telefonního čísla](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)a množství (výchozí množství je nastavené na 1). Všimněte si, že pro typ telefonního čísla bez poplatků je zadání kódu oblasti volitelné.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient, PhoneNumberCapabilityType, PhoneNumberAssignmentType, PhoneNumberType, PhoneNumberCapabilities
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure Portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = self.phone_number_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        polling = True
    )
    search_result = search_poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="purchase-phone-numbers"></a>Koupit telefonní čísla

Výsledek hledání telefonních čísel je `PhoneNumberSearchResult` . Obsahuje, `searchId` který se dá předávat do rozhraní API nákupních čísel, aby se získaly čísla ve vyhledávání. Všimněte si, že volání rozhraní API pro telefonní čísla nákupu bude mít za následek poplatek za váš účet Azure.

```python
import os
from azure.communication.phonenumbers import (
    PhoneNumbersClient, 
    PhoneNumberCapabilityType, 
    PhoneNumberAssignmentType, 
    PhoneNumberType, 
    PhoneNumberCapabilities
)
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure Portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        area_code="833",
        polling = True
    )
    search_result = poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

    purchase_poller = phone_numbers_client.begin_purchase_phone_numbers(search_result.search_id, polling = True)
    purchase_poller.result()
    print("The status of the purchase operation was: " + purchase_poller.status())
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="get-purchased-phone-numbers"></a>Koupit telefonní čísla

Po nákupním čísle ho můžete načíst z klienta. 

```python
purchased_phone_number_information = phone_numbers_client.get_purchased_phone_number("+18001234567")
print('Phone number: ' + purchased_phone_number_information.phone_number)
print('Country code: ' + purchased_phone_number_information.country_code)
```

Můžete také načíst všechna zakoupená telefonní čísla.

```python
purchased_phone_numbers = phone_numbers_client.list_purchased_phone_numbers()
print('Purchased phone numbers:')
for purchased_phone_number in purchased_phone_numbers:
    print(purchased_phone_number.phone_number)
```

### <a name="update-phone-number-capabilities"></a>Aktualizace možností telefonního čísla

Můžete aktualizovat možnosti dříve zakoupeného telefonního čísla.
```python
update_poller = phone_numbers_client.begin_update_phone_number_capabilities(
    "+18001234567",
    PhoneNumberCapabilityType.OUTBOUND,
    PhoneNumberCapabilityType.OUTBOUND,
    polling = True
)
update_poller.result()
print('Status of the operation: ' + update_poller.status())
```

### <a name="release-phone-number"></a>Telefonní číslo verze

Můžete vydat zakoupené telefonní číslo.

```python
release_poller = phone_numbers_client.begin_release_phone_number("+18001234567")
release_poller.result()
print('Status of the operation: ' + release_poller.status())
```

## <a name="run-the-code"></a>Spuštění kódu

V příkazovém řádku konzoly přejděte do adresáře, který obsahuje soubor phone_numbers_sample. py a spusťte následující příkaz Pythonu pro spuštění aplikace.

```console
./phone_numbers_sample.py
```
