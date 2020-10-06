---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: chrwhit
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: chrwhit
ms.openlocfilehash: d02968466920cf1294899907741ebf492e1d0274
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91757114"
---
Začínáme s komunikačními službami Azure pomocí klientské knihovny služby Communications Services Java SMS k posílání zpráv SMS.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Artifact (Maven)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) verze 8 nebo vyšší.
- [Apache Maven](https://maven.apache.org/download.cgi)
- Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- Telefonní číslo s povoleným SMS. [Získejte telefonní číslo](../get-phone-number.md).

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

- V terminálu nebo příkazovém okně spusťte příkaz `mvn -v` a ověřte, zda je Maven nainstalován.
- Chcete-li zobrazit telefonní čísla přidružená ke zdroji komunikačních služeb, přihlaste se k [Azure Portal](https://portal.azure.com/), vyhledejte prostředek komunikačních služeb a otevřete kartu **telefonní čísla** v levém navigačním podokně.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-java-application"></a>Vytvoření nové aplikace Java

Otevřete okno terminálu nebo příkaz a přejděte do adresáře, kam chcete vytvořit aplikaci Java. Spuštěním následujícího příkazu vygenerujte projekt Java ze šablony Maven-Archetype-Starter.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Cíl ' Generate ' vytvoří adresář se stejným názvem jako artifactId. V tomto adresáři obsahuje **Src/Main/Java** adresář zdrojového kódu projektu, zdrojový **/testovací/Java adresář** obsahuje zdroj testu a **pom.xml** soubor je projektový model projektu nebo pom.

### <a name="install-the-package"></a>Instalace balíčku

Otevřete **pom.xml** soubor v textovém editoru. Přidejte následující prvek závislosti do skupiny závislostí.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Přidejte `azure-core-http-netty` závislost do souboru **pom.xml** .

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.3.0</version>
</dependency>
```

Otevřete **/Src/Main/Java/com/Communication/Quickstart/App.Java** v textovém editoru, přidejte direktivy importu a odeberte `System.out.println("Hello world!");` příkaz:

```java
package com.communication.quickstart;

import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.List;

import com.azure.communication.common.CommunicationClientCredential;
import com.azure.communication.common.PhoneNumber;
import com.azure.communication.sms.SmsClient;
import com.azure.communication.sms.SmsClientBuilder;
import com.azure.communication.sms.models.SendSmsOptions;
import com.azure.communication.sms.models.SendSmsResponse;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;

public class App
{
    public static void main( String[] args ) throws IOException, NoSuchAlgorithmException, InvalidKeyException
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce klientské knihovny SMS služby Azure Communications Services pro jazyk Java.

| Název                                                             | Popis                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Tato třída vytvoří třídu SmsClient. Poskytnete ho pro koncové body, přihlašovací údaje a klienta http. |
| SmsClient                    | Tato třída je potřebná pro všechny funkce SMS. Použijete ho k posílání zpráv SMS.                |
| SendSmsResponse               | Tato třída obsahuje odpověď ze služby SMS.                                          |
| CommunicationClientCredential | Tato třída zpracovává požadavky na podepisování.                                                            |
| PhoneNumber                   | Tato třída obsahuje informace o telefonním čísle.

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci `SmsClient` s připojovacím řetězcem. Následující kód načte koncový bod a řetězce přihlašovacích údajů pro prostředek z proměnných prostředí s názvem `COMMUNICATION_SERVICES_ENDPOINT_STRING` a `COMMUNICATION_SERVICES_CREDENTIAL_STRING` (přihlašovací údaje jsou `Key` z Azure Portal. Naučte se [Spravovat připojovací řetězec prostředku](../../create-communication-resource.md#store-your-connection-string).

Do metody `main` přidejte následující kód:

```java
// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationClientCredential credential = new CommunicationClientCredential(accessKey);

// Configure and build a new SmsClient
SmsClient client = new SmsClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .httpClient(httpClient)
    .buildClient();
```

Můžete inicializovat klienta pomocí libovolného vlastního klienta HTTP, který implementuje `com.azure.core.http.HttpClient` rozhraní. Výše uvedený kód ukazuje použití [klienta protokolu HTTP v Azure Core](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true) , který poskytuje `azure-core` .

## <a name="send-an-sms-message"></a>Odeslání zprávy SMS

Odešle zprávu SMS voláním metody sendMessage. Přidejte tento kód na konec `main` metody:

```java
List<PhoneNumber> to = new ArrayList<PhoneNumber>();
to.add(new PhoneNumber("<to-phone-number>"));

// SendSmsOptions is an optional field. It can be used
// to enable a delivery report to the Azure Event Grid
SendSmsOptions options = new SendSmsOptions();
options.setEnableDeliveryReport(true);

// Send the message and check the response for a message id
SendSmsResponse response = client.sendMessage(
    new PhoneNumber("<leased-phone-number>"),
    to,
    "<message-text>",
    options);

System.out.println("MessageId: " + response.getMessageId());
```

Měli byste nahradit `<leased-phone-number>` telefonním číslem s povoleným serverem SMS přidruženým k vašemu prostředku komunikačních služeb a `<to-phone-number>` telefonním číslem, na které chcete poslat zprávu.

`enableDeliveryReport`Parametr je volitelný parametr, který můžete použít ke konfiguraci vytváření sestav o doručení. To je užitečné ve scénářích, kdy chcete generovat události při doručování zpráv SMS. Nastavování sestav doručení pro zprávy SMS najdete v rychlém startu pro [zpracování událostí SMS](../handle-sms-events.md) .

<!--todo: the signature of the `sendMessage` method changes when configuring delivery reporting. Need to confirm that this is how our client library is to be used.-->

## <a name="run-the-code"></a>Spuštění kódu

Přejděte do adresáře obsahujícího soubor **pom.xml** a zkompilujte projekt pomocí `mvn` příkazu.

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
