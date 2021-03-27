---
ms.openlocfilehash: 06033f13b1f63849408646835a844c2eafa8d64f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629369"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) verze 8 nebo vyšší.
- [Apache Maven](https://maven.apache.org/download.cgi). – nasazený prostředek komunikačních služeb a připojovací řetězec. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-java-application"></a>Vytvoření nové aplikace Java

Otevřete okno terminálu nebo příkazového řádku. Přejděte do adresáře, do kterého chcete vytvořit aplikaci Java. Spuštěním následujícího příkazu vygenerujte projekt Java ze šablony Maven-Archetype-Starter.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Všimněte si, že úloha generovat vytvořila adresář se stejným názvem, jako má `artifactId` . V tomto adresáři obsahuje src/Main/Java adresář zdrojového kódu projektu, `src/test/java directory` obsahuje zdroj testu a `pom.xml` soubor je projektový model projektu nebo pom.

### <a name="install-the-package"></a>Instalace balíčku

Otevřete **pom.xml** soubor v textovém editoru. Přidejte následující prvky závislosti do skupiny závislostí.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-phonenumbers</artifactId>
    <version>1.0.0-beta.6</version>
</dependency>

<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Z adresáře projektu:

1. Přejděte do adresáře */Src/Main/Java/com/Communication/Quickstart*
1. Otevřete soubor *App. Java* v editoru.
1. Nahradit `System.out.println("Hello world!");` příkaz
1. Přidat `import` direktivy

Pro začátek použijte následující kód:

```java
import com.azure.communication.phonenumbers.*;
import com.azure.communication.phonenumbers.models.*;
import java.io.*;
import com.azure.core.http.*;
import com.azure.core.http.netty.*;
import com.azure.core.util.Context;
import com.azure.core.util.polling.PollResponse;
import com.azure.identity.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Phone Numbers Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-phone-numbers-client"></a>Ověření klienta telefonních čísel

PhoneNumberClientBuilder je povoleno používat ověřování Azure Active Directory.
<!-- embedme ./src/samples/java/com/azure/communication/phonenumbers/ReadmeSamples.java#L52-L62 -->
```java
// You can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

PhoneNumbersClient phoneNumberClient = new PhoneNumbersClientBuilder()
    .endpoint(endpoint)
    .credential(new DefaultAzureCredentialBuilder().build())
    .httpClient(httpClient)
    .buildClient();
```

Alternativně je překročila povolený pomocí koncového bodu a přístupového klíče z komunikačního prostředku k ověření také.
<!-- embedme ./src/samples/java/com/azure/communication/phonenumbers/ReadmeSamples.java#L30-L41 -->
```java
// You can find your connection string from your resource in the Azure Portal
String connectionString = "https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<ACCESS_KEY>";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

PhoneNumbersClient phoneNumberClient = new PhoneNumbersClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="manage-phone-numbers"></a>Správa telefonních čísel

### <a name="search-for-available-phone-numbers"></a>Vyhledat dostupná telefonní čísla

Aby bylo možné koupit telefonní čísla, je třeba nejprve vyhledat dostupná telefonní čísla. Chcete-li vyhledat telefonní čísla, zadejte směrové číslo oblasti, typ přiřazení, [Možnosti telefonního čísla](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [typ telefonního čísla](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)a množství. Všimněte si, že pro typ telefonního čísla bez poplatků je zadání kódu oblasti volitelné.

```java
 PhoneNumberCapabilities capabilities = new PhoneNumberCapabilities()
    .setCalling(PhoneNumberCapabilityType.INBOUND)
    .setSms(PhoneNumberCapabilityType.INBOUND_OUTBOUND);
PhoneNumberSearchOptions searchOptions = new PhoneNumberSearchOptions().setAreaCode("833").setQuantity(1);

PhoneNumberSearchResult searchResult = phoneNumberClient
    .beginSearchAvailablePhoneNumbers("US", PhoneNumberType.TOLL_FREE, PhoneNumberAssignmentType.APPLICATION, capabilities,  searchOptions, Context.NONE)
    .getFinalResult();

System.out.println("Searched phone numbers: " + searchResult.getPhoneNumbers());
System.out.println("Search expires by: " + searchResult.getSearchExpiresBy());
System.out.println("Phone number costs:" + searchResult.getCost().getAmount());
```

### <a name="purchase-phone-numbers"></a>Koupit telefonní čísla

Výsledkem hledání telefonních čísel je PhoneNumberSearchResult. Obsahuje, `searchId` který se dá předávat do rozhraní API nákupních čísel, aby se získaly čísla ve vyhledávání. Všimněte si, že volání rozhraní API pro telefonní čísla nákupu bude mít za následek poplatek za váš účet Azure.

```java
PollResponse<PhoneNumberOperation> purchaseResponse = phoneNumberClient.beginPurchasePhoneNumbers(searchResult.getSearchId(), Context.NONE).waitForCompletion();
System.out.println("Purchase phone numbers operation is: " + purchaseResponse.getStatus());
```

### <a name="get-phone-numbers"></a>Získat telefonní čísla

Po nákupním čísle ho můžete načíst z klienta.
```java
AcquiredPhoneNumber phoneNumber = phoneNumberClient.getPhoneNumber("+14255550123");
System.out.println("Phone Number Country Code: " + phoneNumber.getCountryCode());
```

Můžete také načíst všechna zakoupená telefonní čísla.
``` java
PagedIterable<AcquiredPhoneNumber> phoneNumbers = createPhoneNumberClient().listPhoneNumbers(Context.NONE);
AcquiredPhoneNumber phoneNumber = phoneNumbers.iterator().next();
System.out.println("Phone Number Country Code: " + phoneNumber.getCountryCode());
```

### <a name="update-phone-number-capabilities"></a>Aktualizace možností telefonního čísla

Po zakoupeném čísle můžete možnosti aktualizovat.
```java
PhoneNumberCapabilitiesRequest capabilitiesRequest = new PhoneNumberCapabilitiesRequest();
capabilitiesRequest
    .setCalling(PhoneNumberCapabilityType.INBOUND)
    .setSms(PhoneNumberCapabilityType.INBOUND);
AcquiredPhoneNumber phoneNumber = phoneNumberClient.beginUpdatePhoneNumberCapabilities("+18001234567", capabilitiesRequest, Context.NONE).getFinalResult();

System.out.println("Phone Number Calling capabilities: " + phoneNumber.getCapabilities().getCalling());
System.out.println("Phone Number SMS capabilities: " + phoneNumber.getCapabilities().getSms());
```

### <a name="release-phone-number"></a>Telefonní číslo verze

Můžete vydat zakoupené telefonní číslo.
```java
PollResponse<PhoneNumberOperation> releaseResponse =
    phoneNumberClient.beginReleasePhoneNumber("+18001234567", Context.NONE).waitForCompletion();
System.out.println("Release phone number operation is: " + releaseResponse.getStatus());
```

## <a name="run-the-code"></a>Spuštění kódu

Přejděte do adresáře obsahujícího soubor *pom.xml* a zkompilujte projekt pomocí následujícího `mvn` příkazu.

```console
mvn compile
```

Pak Sestavte balíček.

```console
mvn package
```

Spusťte následující `mvn` příkaz, který aplikaci spustí.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Výstup aplikace popisuje každou akci, která je dokončena:
<!---cSpell:disable --->
```console
Azure Communication Services - Phone Numbers Quickstart

Searched phone numbers: [+18001234567]

Purchase phone numbers operation is: SUCCESSFULLY_COMPLETED

Phone Number Country Code: US

Phone Number Calling capabilities: inbound

Phone Number SMS capabilities: inbound

Release phone number operation is: SUCCESSFULLY_COMPLETED

```
<!---cSpell:enable --->