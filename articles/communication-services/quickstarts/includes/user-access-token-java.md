---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 6be69a1ec20ed859769c944a2f66de1310c09507
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94915372"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) verze 8 nebo vyšší.
- [Apache Maven](https://maven.apache.org/download.cgi)
- Nasazený prostředek komunikačních služeb a připojovací řetězec. [Vytvořte prostředek služby Communications](../create-communication-resource.md).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-java-application"></a>Vytvoření nové aplikace Java

Otevřete okno terminálu nebo příkazového řádku. Přejděte do adresáře, do kterého chcete vytvořit aplikaci Java. Spuštěním následujícího příkazu vygenerujte projekt Java ze šablony Maven-Archetype-Starter.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Všimněte si, že úloha generovat vytvořila adresář se stejným názvem, jako má `artifactId` . V tomto adresáři obsahuje src/Main/Java adresář zdrojového kódu projektu, `src/test/java directory` obsahuje zdroj testu a `pom.xml` soubor je projektový model projektu nebo pom.

### <a name="install-the-package"></a>Instalace balíčku

Otevřete **pom.xml** soubor v textovém editoru. Přidejte následující prvek závislosti do skupiny závislostí.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-administration</artifactId>
    <version>1.0.0-beta.3</version> 
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
import com.azure.communication.administration.*;
import com.azure.communication.common.*;
import java.io.*;
import java.util.*;
import java.time.*;

import com.azure.core.http.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci a `CommunicationIdentityClient` pomocí přístupového klíče prostředku a koncového bodu. Naučte se [Spravovat připojovací řetězec prostředku](../create-communication-resource.md#store-your-connection-string).

Do metody `main` přidejte následující kód:

```java
// Your can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
// -> Add "import com.azure.core.http.netty.*;"
// -> Add azure-core-http-netty dependency to file pom.xml

HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .accessKey(accessKey)
    .httpClient(httpClient)
    .buildClient();
```

Můžete inicializovat klienta pomocí libovolného vlastního klienta HTTP, který implementuje `com.azure.core.http.HttpClient` rozhraní. Výše uvedený kód ukazuje použití [klienta protokolu HTTP v Azure Core](/java/api/overview/azure/core-http-netty-readme?preserve-view=true&view=azure-java-stable) , který poskytuje `azure-core` .

Můžete také zadat celý připojovací řetězec pomocí funkce connectionString () místo poskytnutí koncového bodu a přístupového klíče. 
```java
// Your can find your connection string from your resource in the Azure Portal
String connectionString = "<connection_string>";
CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="create-an-identity"></a>Vytvoření identity

Komunikační služby Azure udržují zjednodušený adresář identit. Použijte `createUser` metodu k vytvoření nové položky v adresáři s jedinečným objektem `Id` . Uložte si získanou identitu s mapováním na uživatele vaší aplikace. Například uložením do databáze aplikačního serveru. Identita se vyžaduje později pro vydávání přístupových tokenů.

```java
CommunicationUser identity = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + identity.getId());
```

## <a name="issue-access-tokens"></a>Vystavení přístupových tokenů

Použijte `issueToken` metodu pro vydání přístupového tokenu pro již existující identitu komunikačních služeb. Parametr `scopes` definuje sadu primitivních hodnot, které budou autorizovat tento přístupový token. Podívejte se na [seznam podporovaných akcí](../../concepts/authentication.md). Nová instance parametru `user` se dá sestavit na základě řetězcové reprezentace identity komunikační služby Azure.

```java
// Issue an access token with the "voip" scope for an identity
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(identity, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
String identityId = response.getUser().getId();
System.out.println("\nIssued a access token with 'voip' scope for identity with ID: " + identityId + ": " + token);
```

Přístupové tokeny jsou krátkodobé přihlašovací údaje, které je potřeba znovu vydat. V takovém případě může dojít k přerušení činnosti uživatelů vaší aplikace. `expiresAt`Vlastnost Response označuje dobu života přístupového tokenu.

## <a name="refresh-access-tokens"></a>Obnovení přístupových tokenů

K aktualizaci přístupového tokenu použijte `CommunicationUser` objekt, který se má znovu vystavit:

```java  
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
CommunicationUser identity = new CommunicationUser(existingIdentity);
response = communicationIdentityClient.issueToken(identity, scopes);
```

## <a name="revoke-access-tokens"></a>Odvolat přístupové tokeny

V některých případech je možné explicitně odvolat přístupové tokeny. Například když uživatel aplikace změní heslo, které používá k ověření vaší služby. Metoda `revokeTokens` zruší platnost všech aktivních přístupových tokenů, které byly vystaveny identitě.

```java  
communicationIdentityClient.revokeTokens(identity, OffsetDateTime.now());
System.out.println("\nRevoked access tokens for the user with ID: " + identity.getId());
```

## <a name="delete-an-identity"></a>Odstranění identity

Odstranění identity odvolá všechny aktivní přístupové tokeny a zabrání vám v vystavování přístupových tokenů pro danou identitu. Zároveň se tím odebere veškerý trvalý obsah přidružený k identitě.

```java
communicationIdentityClient.deleteUser(identity);
System.out.println("\nSuccessfully deleted the identity with ID: " + identity.getId());
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