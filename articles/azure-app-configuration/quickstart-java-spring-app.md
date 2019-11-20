---
title: Rychlý Start, kde se dozvíte, jak používat konfiguraci aplikací Azure | Microsoft Docs
description: Rychlý Start pro použití konfigurace aplikací Azure s aplikacemi pro pružinu v jazyce Java
services: azure-app-configuration
documentationcenter: ''
author: yidon
manager: jeffya
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: Spring
ms.workload: tbd
ms.date: 01/08/2019
ms.author: yidon
ms.openlocfilehash: e8f6f9ca610c515deca6ed1bdbee54f40cacf427
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184942"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Rychlý Start: Vytvoření aplikace s pružinou v jazyce Java pomocí konfigurace aplikace Azure

V tomto rychlém startu zařadíte konfiguraci aplikace Azure do aplikace v pružině Java, která bude centralizovat úložiště a správu nastavení aplikace odděleně od vašeho kódu.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- Podporovaná [sada Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) s verzí 8.
- [Apache Maven](https://maven.apache.org/download.cgi) verze 3,0 nebo vyšší.

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Průzkumníka konfigurace** >  **+ vytvořit** a přidejte následující páry klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | /application/config.message | Dobrý den, |

    V tuto chvíli ponechat **popisek** a **typ obsahu** prázdné.

## <a name="create-a-spring-boot-app"></a>Vytvoření aplikace pro spouštění pružiny

Pomocí [jarního Initializru](https://start.spring.io/) vytvoříte nový projekt pro spuštění pružiny.

1. Přejděte do <https://start.spring.io/>.

2. Zadejte následující možnosti:

   * Vygenerujte projekt **Maven** pomocí **jazyka Java**.
   * Zadejte verzi pro **jarní spuštění** , která je rovna nebo větší než 2,0.
   * Zadejte název **skupiny** a **artefakty** pro vaši aplikaci.
   * Přidejte **webovou** závislost.

3. Po zadání předchozích možností vyberte **generovat projekt**. Po zobrazení výzvy Stáhněte projekt do cesty na místním počítači.

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

1. Po extrahování souborů v místním systému je vaše jednoduchá aplikace pro spouštění pomocí pružiny připravená k úpravám. V kořenovém adresáři vaší aplikace vyhledejte soubor *pom. XML* .

2. V textovém editoru otevřete soubor *pom. XML* a přidejte do seznamu `<dependencies>`u jarní cloud Azure config Starter:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M5</version>
    </dependency>
    ```

3. V adresáři balíčku aplikace vytvořte nový soubor Java s názvem *MessageProperties. Java* . Přidejte následující řádky:

    ```java
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

4. V adresáři balíčku aplikace vytvořte nový soubor Java s názvem *HelloController. Java* . Přidejte následující řádky:

    ```java
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

5. Otevřete soubor hlavní aplikace v jazyce Java a přidejte `@EnableConfigurationProperties` pro povolení této funkce.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. V adresáři Resources vaší aplikace vytvořte nový soubor s názvem `bootstrap.properties` a do souboru přidejte následující řádky. Nahraďte vzorové hodnoty odpovídajícími vlastnostmi úložiště konfigurace aplikace.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Sestavte aplikaci pro jarní spouštění pomocí Maven a spusťte ji například takto:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Po spuštění aplikace použijte k otestování aplikace *kudrlinkou* . Příklad:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Zobrazí se zpráva, kterou jste zadali v úložišti konfigurace aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nové úložiště konfigurace aplikace a použili ho v aplikaci Java pružiny. Další informace najdete v tématu [pružina v Azure](https://docs.microsoft.com/java/azure/spring-framework/). Pokud se chcete dozvědět, jak pomocí spravované identity Azure zjednodušit přístup ke konfiguraci aplikací, pokračujte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Spravovaná integrace identit](./howto-integrate-azure-managed-service-identity.md)
