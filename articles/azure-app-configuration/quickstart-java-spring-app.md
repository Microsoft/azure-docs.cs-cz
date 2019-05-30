---
title: Rychlý Start se naučíte používat konfiguraci aplikací Azure | Dokumentace Microsoftu
description: Rychlý start pro konfiguraci aplikací Azure pomocí aplikace Java Spring.
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
ms.openlocfilehash: a91c61edd773b5742b092f5d72a5a22f1d90e63b
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393554"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Rychlý start: Vytvoření aplikace Java Spring pomocí konfigurace aplikace

Konfigurace aplikace pro Azure je služba spravované konfigurace v Azure. Můžete ho snadno ukládat a spravovat nastavení aplikace na jednom místě, který je oddělen od kódu. V tomto rychlém startu se dozvíte, jak začlenit službu do aplikace Java Spring.

Provést kroky v tomto rychlém startu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) skvělou možnost je k dispozici ve Windows, macOS a Linux platformy.

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu provedete instalaci podporované [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) verze 8 a [Apache Maven](https://maven.apache.org/) verze 3.0 nebo vyšší.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Průzkumník konfigurací** >  **+ vytvořit** přidáte následující páry klíč hodnota:

    | Klíč | Hodnota |
    |---|---|
    | /Application/config.Message | Dobrý den, |

    Ponechte **popisek** a **typ obsahu** zatím prázdný.

## <a name="create-a-spring-boot-app"></a>Vytvoření aplikace Spring Boot aplikace

Můžete použít [Spring Initializr](https://start.spring.io/) k vytvoření nového projektu Spring Boot.

1. Přejděte do <https://start.spring.io/>.

2. Určete následující možnosti:

   * Generování **Maven** projektu s **Java**.
   * Zadejte **Spring Boot** verzi, která je rovna nebo větší než 2,0.
   * Zadejte **skupiny** a **artefaktů** názvy pro vaši aplikaci.
   * Přidat **webové** závislostí.

3. Po zadání předchozích možností vyberte **generování projektu**. Po zobrazení výzvy, stáhněte si projekt na cestu v místním počítači.

## <a name="connect-to-an-app-configuration-store"></a>Připojit k úložišti konfigurace aplikace

1. Po extrahování souborů v místním systému vaší jednoduché aplikace Spring Boot připravený pro úpravy. Vyhledejte *pom.xml* souboru v kořenovém adresáři aplikace.

2. Otevřít *pom.xml* souboru v textovém editoru a přidejte počáteční konfigurace Spring cloudu Azure do seznamu `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.M3</version>
    </dependency>
    ```

3. Vytvořte nový soubor Java *MessageProperties.java* v adresáři balíčku aplikace. Přidejte následující řádky:

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

4. Vytvořte nový soubor Java *HelloController.java* v adresáři balíčku aplikace. Přidejte následující řádky:

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

5. Otevřete soubor Java hlavní aplikaci a přidejte `@EnableConfigurationProperties` tuto funkci povolil.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. Vytvořte nový soubor s názvem `bootstrap.properties` v adresáři prostředků vaší aplikace a přidejte následující řádky do souboru. Nahraďte ukázkové hodnoty příslušných vlastností pro úložiště konfigurace vaší aplikace.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string=[your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Sestavte a spusťte aplikaci místně

1. Sestavení aplikace Spring Boot pomocí Mavenu a běží u Tebe, například:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Jakmile je aplikace spuštěna, použijte *curl* a otestujte aplikaci, například:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Se zobrazí zpráva, kterou jste zadali v úložišti konfigurace aplikace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili novým úložištěm konfigurace aplikace a používat s využitím aplikace Java Spring. Další informace najdete v tématu [Spring na Azure](https://docs.microsoft.com/java/azure/spring-framework/).

Další informace o tom, jak používat konfiguraci aplikací, pokračujte k dalšímu kurzu, který ukazuje ověření.

> [!div class="nextstepaction"]
> [Integrace spravovaných identit](./howto-integrate-azure-managed-service-identity.md)
