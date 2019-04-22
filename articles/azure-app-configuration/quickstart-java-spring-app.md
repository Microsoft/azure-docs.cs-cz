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
ms.openlocfilehash: e52c14051d038ae36af9f31f1ab13da11d032a17
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699283"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Rychlý start: Vytvoření aplikace Java Spring pomocí konfigurace aplikace

Konfigurace aplikace pro Azure je služba spravované konfigurace v Azure. Můžete ho snadno ukládat a spravovat nastavení aplikace na jednom místě, který je oddělen od kódu. V tomto rychlém startu se dozvíte, jak začlenit službu do aplikace Java Spring.

Provést kroky v tomto rychlém startu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) skvělou možnost je k dispozici ve Windows, macOS a Linux platformy.

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu provedete instalaci podporované [Java Development Kit (JDK)](https://aka.ms/azure-jdks) verze 8 a [Apache Maven](https://maven.apache.org/) verze 3.0 nebo vyšší.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

1. Pokud chcete vytvořit novým úložištěm konfigurace aplikace, přihlaste se k [webu Azure portal](https://aka.ms/azconfig/portal). V levém horním rohu stránky vyberte **+ vytvořit prostředek**. V **Hledat na Marketplace** zadejte **konfigurace aplikace** a stiskněte klávesu Enter.

    ![Hledat konfigurace aplikace](./media/quickstarts/azure-app-configuration-new.png)

2. Vyberte **konfigurace aplikace** z výsledků hledání a pak vyberte **vytvořit**.

3. Na **konfigurace aplikace** > **vytvořit** stránky, zadejte následující nastavení.

    | Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    | **Název prostředku** | Globálně jedinečný název | Zadejte název jedinečné prostředků pro prostředek aplikace konfigurace úložiště. Název musí být řetězec o délce 1 až 63 znaků a může obsahovat jenom čísla, písmena a znak `-`. Název nesmí začínat ani končit `-` znak a po sobě jdoucích `-` znaky nejsou platné.  |
    | **Předplatné** | Vaše předplatné | Vyberte předplatné Azure, který chcete použít k otestování konfigurace aplikace. Pokud má váš účet jenom jedno předplatné, je automaticky vybrána a **předplatné** rozevíracím seznamu nezobrazí. |
    | **Skupina prostředků** | *AppConfigTestResources* | Vyberte nebo vytvořte skupinu prostředků pro prostředek úložiště konfigurace vaší aplikace. Tato skupina je užitečné pro uspořádání různé prostředky, které můžete chtít odstranit tak, že odstraníte skupinu prostředků ve stejnou dobu. Další informace najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
    | **Umístění** | *USA (střed)* | Pomocí **Umístění** určete zeměpisné umístění, ve kterém se prostředek služby SignalR bude hostovat. Pro zajištění nejlepšího výkonu vytvořte prostředek ve stejné oblasti jako součásti vaší aplikace. |

    ![Vytvoření úložiště konfigurace aplikace](./media/quickstarts/azure-app-configuration-create.png)

4. Vyberte **Vytvořit**. Nasazení může trvat několik minut déle.

5. Po dokončení nasazení vyberte **nastavení** > **přístupové klíče**. Poznamenejte buď primární jen pro čtení nebo primární čtení a zápis připojovací řetězec klíče. Tento připojovací řetězec použijete později ke konfiguraci vaší aplikace ke komunikaci s úložišti konfigurace aplikace, kterou jste vytvořili.

6. Vyberte **klíč/hodnota Explorer** > **+ vytvořit** přidáte následující páry klíč hodnota:

    | Klíč | Hodnota |
    |---|---|
    | /Application/config.Message | Dobrý den, |

    Ponechte **popisek** a **typ obsahu** zatím prázdný.

## <a name="create-a-spring-boot-app"></a>Vytvoření aplikace Spring Boot aplikace

Můžete použít [Spring Initializr](https://start.spring.io/) k vytvoření nového projektu Spring Boot.

1. Přejděte na <https://start.spring.io/>.

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
> [Spravované identity pro integraci prostředků Azure](./howto-integrate-azure-managed-service-identity.md)
