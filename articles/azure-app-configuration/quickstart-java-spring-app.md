---
title: Úvodní příručka se dozvíte, jak používat Azure App Configuration
description: Rychlý start pro používání Azure App Configuration s java jarníaplikacemi.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.author: lcozzens
ms.openlocfilehash: 85b966a9423fa6c8b2dc9e97cab1e24d9756caa4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687342"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Úvodní příručka: Vytvoření aplikace Java Spring s konfigurací aplikací Azure

V tomto rychlém startu můžete začlenit Azure App Configuration do aplikace Java Spring centralizovat ukládání a správu nastavení aplikací odděleně od vašeho kódu.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
- Podporovaná [java development kit (JDK)](https://docs.microsoft.com/java/azure/jdk) s verzí 8.
- [Apache Maven](https://maven.apache.org/download.cgi) verze 3.0 nebo vyšší.

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikací

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Průzkumník** > konfigurace **+ Vytvořit** > **klíč-hodnotu** přidat následující dvojice klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | /application/config.message | Dobrý den |

    Ponechte **popisek** a **typ obsahu** prozatím prázdný.

7. Vyberte **Použít**.

## <a name="create-a-spring-boot-app"></a>Vytvoření aplikace jarního spuštění

Pomocí [pružinového inicializru vytvořte](https://start.spring.io/) nový projekt jarního startu.

1. Přejděte na <https://start.spring.io/>.

1. Zadejte následující možnosti:

   - Vygenerujte projekt **Maven** v **Javě**.
   - Zadejte verzi **jarního spuštění,** která je rovna nebo větší než 2.0.
   - Zadejte názvy skupiny (**Group**) a artefaktu (**Artifact**) pro vaši aplikaci.
   - Přidejte závislost **spring web.**

1. Po zadání předchozích možností vyberte **Generovat projekt**. Po zobrazení výzvy stáhněte projekt na místní počítač.

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikací

1. Po extrahování souborů v místním systému je vaše jednoduchá aplikace Spring Boot připravena k úpravám. Vyhledejte soubor *pom.xml* v kořenovém adresáři aplikace.

1. Otevřete soubor *pom.xml* v textovém editoru a přidejte startér `<dependencies>`Spring Cloud Azure Config do seznamu :

    **Jarní mrak 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.2</version>
    </dependency>
    ```

    **Jarní mrak 1.2.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.2</version>
    </dependency>
    ```

1. Vytvořte nový soubor Java s názvem *MessageProperties.java* v adresáři balíčků vaší aplikace. Přidejte následující řádky:

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. Vytvořte nový soubor Java s názvem *HelloController.java* v adresáři balíčků vaší aplikace. Přidejte následující řádky:

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

1. Otevřete hlavní soubor Java `@EnableConfigurationProperties` aplikace a přidejte tuto funkci.

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Vytvořte nový `bootstrap.properties` soubor pojmenovaný v adresáři prostředků aplikace a přidejte do něj následující řádky. Nahraďte ukázkové hodnoty příslušnými vlastnostmi pro váš obchod SKonfigurací aplikací.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string=${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Nastavte proměnnou prostředí s názvem **APP_CONFIGURATION_CONNECTION_STRING**a nastavte ji na přístupový klíč do obchodu konfigurace aplikací. Na příkazovém řádku spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

    ```cmd
    setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

    ```azurepowershell
    $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

    ```cmd
    export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>Sestavení a spuštění aplikace místně

1. Sestavte si aplikaci Spring Boot s Maven a spusťte ji, například:

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. Po spuštění aplikace použijte *curl* k testování aplikace, například:

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    Zobrazí se zpráva, kterou jste zadali v úložišti konfigurace aplikací.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nový obchod S konfigurací aplikací a použili ho s aplikací Java Spring. Další informace najdete [v tématu jaro v Azure](https://docs.microsoft.com/java/azure/spring-framework/). Chcete-li se dozvědět, jak povolit aplikaci Java Spring dynamicky aktualizovat nastavení konfigurace, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Povolit dynamickou konfiguraci](./enable-dynamic-configuration-java-spring-app.md)
