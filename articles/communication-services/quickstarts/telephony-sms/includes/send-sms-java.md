---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: pvicencio
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/12/2021
ms.topic: include
ms.custom: include file
ms.author: pvicencio
ms.openlocfilehash: 2739079b67d80f3e4a9f367aaa58f6dcbbb650ca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622052"
---
Začínáme s komunikačními službami Azure pomocí klientské knihovny služby Communications Services Java SMS k posílání zpráv SMS.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) verze 8 nebo vyšší.
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
    <version>1.0.0-beta.4</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Přidejte `azure-core-http-netty` závislost do souboru **pom.xml** .

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.8.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core</artifactId>
    <version>1.13.0</version> <!-- {x-version-update;com.azure:azure-core;dependency} -->
</dependency>
```

Otevřete **/Src/Main/Java/com/Communication/Quickstart/App.Java** v textovém editoru, přidejte direktivy importu a odeberte `System.out.println("Hello world!");` příkaz:

```java
package com.communication.quickstart;

import com.azure.communication.sms.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.communication.sms.*;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;
import com.azure.core.util.Context;
import java.util.Arrays;

public class App
{
    public static void main( String[] args )
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce klientské knihovny SMS služby Azure Communications Services pro jazyk Java.

| Název                                                             | Description                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Tato třída vytvoří třídu SmsClient. Poskytnete ho pro koncové body, přihlašovací údaje a klienta http. |
| SmsClient                    | Tato třída je potřebná pro všechny funkce SMS. Použijete ho k posílání zpráv SMS.                |
| SmsSendResult                | Tato třída obsahuje výsledek ze služby SMS.                                          |
| SmsSendOptions               | Tato třída poskytuje možnosti pro přidání vlastních značek a konfiguraci vytváření sestav o doručení. Pokud je deliveryReportEnabled nastavené na hodnotu true, vygeneruje se po úspěšném doručení událost.|                           |

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci `SmsClient` s připojovacím řetězcem. (Přihlašovací údaje jsou `Key` z Azure Portal. Naučte se [Spravovat připojovací řetězec prostředku](../../create-communication-resource.md#store-your-connection-string).

Do metody `main` přidejte následující kód:

```java
// You can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<resource-name>.communication.azure.com/";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key-credential>");

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
                .endpoint(endpoint)
                .credential(azureKeyCredential)
                .httpClient(httpClient)
                .buildClient();
```

Můžete inicializovat klienta pomocí libovolného vlastního klienta HTTP, který implementuje `com.azure.core.http.HttpClient` rozhraní. Výše uvedený kód ukazuje použití [klienta protokolu HTTP v Azure Core](/java/api/overview/azure/core-http-netty-readme) , který poskytuje `azure-core` .

Můžete také zadat celý připojovací řetězec pomocí funkce connectionString () místo poskytnutí koncového bodu a přístupového klíče. 
```java
// You can find your connection string from your resource in the Azure Portal
String connectionString = "https://<resource-name>.communication.azure.com/;<access-key>";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
            .connectionString(connectionString)
            .httpClient(httpClient)
            .buildClient();
```

## <a name="send-a-11-sms-message"></a>Poslat zprávu SMS 1:1

Chcete-li odeslat zprávu SMS jednomu příjemci, zavolejte `send` metodu z SmsClient s jedním příjemcem telefonního čísla. K určení, zda má být povolena zpráva o doručení a nastavena vlastní značky, můžete také předat volitelné parametry.

```java
SmsSendResult sendResult = smsClient.send(
                "<from-phone-number>",
                "<to-phone-number>",
                "Weekly Promotion");

System.out.println("Message Id: " + sendResult.getMessageId());
System.out.println("Recipient Number: " + sendResult.getTo());
System.out.println("Send Result Successful:" + sendResult.isSuccessful());
```
## <a name="send-a-1n-sms-message-with-options"></a>Odeslat zprávu o 1: N SMS s možnostmi
Chcete-li odeslat zprávu SMS seznamu příjemců, zavolejte `send` metodu se seznamem telefonních čísel příjemců. K určení, zda má být povolena zpráva o doručení a nastavena vlastní značky, můžete také předat volitelné parametry.
```java
SmsSendOptions options = new SmsSendOptions();
options.setDeliveryReportEnabled(true);
options.setTag("Marketing");

Iterable<SmsSendResult> sendResults = smsClient.send(
    "<from-phone-number>",
    Arrays.asList("<to-phone-number1>", "<to-phone-number2>"),
    "Weekly Promotion",
    options /* Optional */,
    Context.NONE);

for (SmsSendResult result : sendResults) {
    System.out.println("Message Id: " + result.getMessageId());
    System.out.println("Recipient Number: " + result.getTo());
    System.out.println("Send Result Successful:" + result.isSuccessful());
}
```

Měli byste nahradit `<from-phone-number>` telefonní číslo s povoleným SMS, které je přidružené k vašemu prostředku komunikačních služeb, a `<to-phone-number>` telefonní číslo nebo seznam telefonních čísel, na které chcete poslat zprávu.

## <a name="optional-parameters"></a>Volitelné parametry

`deliveryReportEnabled`Parametr je volitelný parametr, který můžete použít ke konfiguraci vytváření sestav o doručení. To je užitečné ve scénářích, kdy chcete generovat události při doručování zpráv SMS. Nastavování sestav doručení pro zprávy SMS najdete v rychlém startu pro [zpracování událostí SMS](../handle-sms-events.md) .

`tag`Parametr je volitelný parametr, který můžete použít k použití značky pro sestavu doručení.

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