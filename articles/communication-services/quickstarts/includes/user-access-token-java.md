---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: a0f3e3547c38df63bdab77cf378525072d1e9ad4
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106126031"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) verze 8 nebo vyšší.
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
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
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
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.core.credential.*;

import java.io.IOException;
import java.time.*;
import java.util.*;

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

Vytvořte instanci a `CommunicationIdentityClient` pomocí přístupového klíče prostředku a koncového bodu. Naučte se [Spravovat připojovací řetězec prostředku](../create-communication-resource.md#store-your-connection-string). Kromě toho můžete inicializovat klienta pomocí libovolného vlastního klienta HTTP, který implementuje `com.azure.core.http.HttpClient` rozhraní.

Do metody `main` přidejte následující kód:

```java
// Your can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
        .endpoint(endpoint)
        .credential(new AzureKeyCredential(accessKey))
        .buildClient();
```

Celý připojovací řetězec můžete zadat také pomocí `connectionString()` funkce namísto zadání koncového bodu a přístupového klíče.
```java
// Your can find your connection string from your resource in the Azure portal
String connectionString = "<connection_string>";

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

Pokud jste nastavili spravovanou identitu, přečtěte si téma [použití spravovaných identit](../managed-identity.md). můžete se taky ověřit pomocí spravované identity.
```java
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
TokenCredential credential = new DefaultAzureCredentialBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
        .endpoint(endpoint)
        .credential(credential)
        .buildClient();
```

## <a name="create-an-identity"></a>Vytvoření identity

Komunikační služby Azure udržují zjednodušený adresář identit. Použijte `createUser` metodu k vytvoření nové položky v adresáři s jedinečným objektem `Id` . Uložte si získanou identitu s mapováním na uživatele vaší aplikace. Například uložením do databáze aplikačního serveru. Identita se vyžaduje později pro vydávání přístupových tokenů.

```java
CommunicationUserIdentifier user = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + user.getId());
```

## <a name="issue-access-tokens"></a>Vystavení přístupových tokenů

Použijte `getToken` metodu pro vydání přístupového tokenu pro již existující identitu komunikačních služeb. Parametr `scopes` definuje sadu primitivních hodnot, které budou autorizovat tento přístupový token. Podívejte se na [seznam podporovaných akcí](../../concepts/authentication.md). Nová instance parametru `user` se dá sestavit na základě řetězcové reprezentace identity komunikační služby Azure.

```java
// Issue an access token with the "voip" scope for a user identity
List<CommunicationTokenScope> scopes = new ArrayList<>(Arrays.asList(CommunicationTokenScope.VOIP));
AccessToken accessToken = communicationIdentityClient.getToken(user, scopes);
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'voip' scope that expires at: " + expiresAt + ": " + token);
```

## <a name="create-an-identity-and-issue-token-in-one-call"></a>Vytvoření tokenu identity a problému v jednom volání

Alternativně můžete pomocí metody ' createUserAndToken ' vytvořit novou položku v adresáři s jedinečným `Id` a vydáním přístupového tokenu.

```java
List<CommunicationTokenScope> scopes = Arrays.asList(CommunicationTokenScope.CHAT);
CommunicationUserIdentifierAndToken result = communicationIdentityClient.createUserAndToken(scopes);
CommunicationUserIdentifier user = result.getUser();
System.out.println("\nCreated a user identity with ID: " + user.getId());
AccessToken accessToken = result.getUserToken();
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'chat' scope that expires at: " + expiresAt + ": " + token);
```

Přístupové tokeny jsou krátkodobé přihlašovací údaje, které je potřeba znovu vydat. V takovém případě může dojít k přerušení činnosti uživatelů vaší aplikace. `expiresAt`Vlastnost označuje dobu života přístupového tokenu.

## <a name="refresh-access-tokens"></a>Obnovení přístupových tokenů

K aktualizaci přístupového tokenu použijte `CommunicationUserIdentifier` objekt, který se má znovu vystavit:

```java
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
CommunicationUserIdentifier identity = new CommunicationUserIdentifier(existingIdentity);
AccessToken response = communicationIdentityClient.getToken(identity, scopes);
```

## <a name="revoke-access-tokens"></a>Odvolat přístupové tokeny

V některých případech je možné explicitně odvolat přístupové tokeny. Například když uživatel aplikace změní heslo, které používá k ověření vaší služby. Metoda `revokeTokens` zruší platnost všech aktivních přístupových tokenů, které byly vystaveny identitě.

```java
communicationIdentityClient.revokeTokens(user);
System.out.println("\nSuccessfully revoked all access tokens for user identity with ID: " + user.getId());
```

## <a name="delete-an-identity"></a>Odstranění identity

Odstranění identity odvolá všechny aktivní přístupové tokeny a zabrání vám v vystavování přístupových tokenů pro danou identitu. Zároveň se tím odebere veškerý trvalý obsah přidružený k identitě.

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nDeleted the user identity with ID: " + user.getId());
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
