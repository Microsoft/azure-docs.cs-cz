---
title: Rychlý Start, kde se dozvíte, jak používat konfiguraci aplikací Azure
description: Rychlý Start pro použití konfigurace aplikací Azure s aplikacemi pro pružinu v jazyce Java
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: 2521adfda731c06c879f5cfeb6283567228bf664
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919358"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Rychlý Start: Vytvoření aplikace s pružinou v jazyce Java pomocí konfigurace aplikace Azure

V tomto rychlém startu zařadíte konfiguraci aplikace Azure do aplikace v pružině Java, která bude centralizovat úložiště a správu nastavení aplikace odděleně od vašeho kódu.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- Podporovaná [sada Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) s verzí 8.
- [Apache Maven](https://maven.apache.org/download.cgi) verze 3,0 nebo vyšší.

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

1. Vyberte **Průzkumníka konfigurace** >  **+ vytvořit** a přidejte následující páry klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | /application/config.message | Dobrý den, |

    V tuto chvíli ponechat **popisek** a **typ obsahu** prázdné.

## <a name="create-a-spring-boot-app"></a>Vytvoření aplikace pro spouštění pružiny

Pomocí [jarního Initializru](https://start.spring.io/) vytvořte nový projekt pro spuštění pružiny.

1. Přejděte na <https://start.spring.io/>.

1. Zadejte následující možnosti:

   - Vygenerujte projekt **Maven** v **Javě**.
   - Zadejte verzi pro **jarní spuštění** , která je rovna nebo větší než 2,0.
   - Zadejte názvy skupiny (**Group**) a artefaktu (**Artifact**) pro vaši aplikaci.
   - Přidejte **webovou závislost pružiny** .

1. Po zadání předchozích možností vyberte **generovat projekt**. Po zobrazení výzvy stáhněte projekt na místní počítač.

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

1. Po extrahování souborů v místním systému je vaše jednoduchá aplikace pro spouštění pomocí pružiny připravená k úpravám. V kořenovém adresáři vaší aplikace vyhledejte soubor *pom. XML* .

1. V textovém editoru otevřete soubor *pom. XML* a přidejte do seznamu `<dependencies>`u jarní cloud Azure config Starter:

    **Jarní Cloud 1.1. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.2</version>
    </dependency>
    ```

    **Jarní Cloud 1.2. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.2</version>
    </dependency>
    ```

1. V adresáři balíčku aplikace vytvořte nový soubor Java s názvem *MessageProperties. Java* . Přidejte následující řádky:

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

1. V adresáři balíčku aplikace vytvořte nový soubor Java s názvem *HelloController. Java* . Přidejte následující řádky:

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

1. Otevřete soubor hlavní aplikace v jazyce Java a přidejte `@EnableConfigurationProperties` pro povolení této funkce.

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

1. V adresáři Resources vaší aplikace vytvořte nový soubor s názvem `bootstrap.properties` a do souboru přidejte následující řádky. Nahraďte vzorové hodnoty odpovídajícími vlastnostmi úložiště konfigurace aplikace.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].name= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Nastavte proměnnou prostředí s názvem **APP_CONFIGURATION_CONNECTION_STRING**a nastavte ji na přístupový klíč na úložiště konfigurace aplikace. Na příkazovém řádku spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

    ```CLI
        setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte Windows PowerShell, spusťte následující příkaz:

    ```azurepowershell
        $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

    ```console
        export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Sestavte aplikaci pro jarní spouštění pomocí Maven a spusťte ji například takto:

    ```CLI
    mvn clean package
    mvn spring-boot:run
    ```

2. Po spuštění aplikace použijte k otestování aplikace *kudrlinkou* . Příklad:

      ```CLI
      curl -X GET http://localhost:8080/
      ```

    Zobrazí se zpráva, kterou jste zadali v úložišti konfigurace aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nové úložiště konfigurace aplikace a použili ho v aplikaci Java pružiny. Další informace najdete v tématu [pružina v Azure](https://docs.microsoft.com/java/azure/spring-framework/). Pokud se chcete dozvědět, jak pomocí spravované identity Azure zjednodušit přístup ke konfiguraci aplikací, pokračujte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Spravovaná integrace identit](./howto-integrate-azure-managed-service-identity.md)
