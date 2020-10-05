---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 1235cb299fe887f20dd3f7e47c22eed2b9df6dc9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90946960"
---
## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) verze 8 nebo vyšší.
- [Apache Maven](https://maven.apache.org/download.cgi)
- Nasazený prostředek komunikačních služeb a připojovací řetězec. [Vytvořte prostředek služby Communications](../create-communication-resource.md).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-java-application"></a>Vytvoření nové aplikace Java

Otevřete okno terminálu nebo příkazového řádku a přejděte do adresáře, ve kterém chcete vytvořit aplikaci Java. Spuštěním následujícího příkazu vygenerujte projekt Java ze šablony Maven-Archetype-Starter.

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
    <version>1.0.0-beta.1</version> 
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
import com.azure.communication.common.CommunicationUser;
import com.azure.communication.administration.models.CommunicationIdentityToken;
import com.azure.communication.administration.CommunicationIdentityClient;
import com.azure.communication.administration.CommunicationIdentityClientBuilder;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - User Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci a `CommunicationIdentityClient` pomocí přístupového klíče prostředku a koncového bodu. Naučte se [Spravovat připojovací řetězec prostředku](../create-communication-resource.md#store-your-connection-string).

Do metody `main` přidejte následující kód:

```java
// Your can find your endpoint and access token from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessToken = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationClientCredential(accessToken))
    .httpClient(httpClient)
    .buildClient();
```

Můžete inicializovat klienta pomocí libovolného vlastního klienta HTTP, který implementuje `com.azure.core.http.HttpClient` rozhraní. Výše uvedený kód ukazuje použití [klienta protokolu HTTP v Azure Core](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true) , který poskytuje `azure-core` .

## <a name="create-a-user"></a>Vytvoření uživatele

Komunikační služby Azure udržují zjednodušený adresář identit. Použijte `createUser` metodu k vytvoření nové položky v adresáři s jedinečným objektem `Id` . Měli byste udržovat mapování mezi uživateli vaší aplikace a komunikačními službami, které vygenerovaly identity (například jejich uložením do databáze aplikačního serveru).

```java
CommunicationUser user = communicationIdentityClient.createUser();
System.out.println("\nCreated a user with ID: " + user.getId());
```

## <a name="issue-user-access-tokens"></a>Vystavení přístupových tokenů uživatele

Použijte `issueToken` metodu pro vydání přístupového tokenu pro uživatele komunikačních služeb. Pokud nezadáte volitelný parametr, vytvoří se `user` Nový uživatel, který se vrátí s tokenem.

```java
// Issue an access token with the "voip" scope for a new user
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(user, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
String userId = response.getUser().getId();
System.out.println("\nIssued a access token with 'voip' scope for identity with ID: " + userId + ": " + token);
System.out.println(token);
```

Tokeny přístupu uživatele jsou krátkodobé přihlašovací údaje, které je potřeba znovu vystavit, aby nedocházelo k přerušení služeb uživatelů. `expiresAt`Vlastnost Response označuje dobu života tokenu.

## <a name="revoke-user-access-tokens"></a>Odvolat tokeny přístupu uživatele

V některých případech může být nutné explicitně odvolat tokeny přístupu uživatele, například když uživatel změní heslo, které používá k ověření vaší služby. Tato metoda používá `revokeTokens` k devalidaci všech přístupových tokenů uživatele.

```java  
communicationIdentityClient.revokeTokens(user, OffsetDateTime.now());
System.out.println("\nRevoked tokens for the user with ID: " + user.getId());
```

## <a name="delete-a-user"></a>Odstranění uživatele

Odstranění uživatele odvolá všechny aktivní tokeny a zabrání vám v vydávání následných tokenů pro identity. Zároveň se tím odebere veškerý trvalý obsah přidružený k uživateli.

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nSuccessfully deleted the identity with ID: " + user.getId());
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
