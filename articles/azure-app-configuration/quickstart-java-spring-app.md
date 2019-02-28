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
ms.openlocfilehash: d607d6cd813b23051e1676153cbb134261bcf5bc
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960601"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Rychlý start: Vytvoření aplikace Java Spring pomocí konfigurace aplikace

Konfigurace aplikace pro Azure je služba spravované konfigurace v Azure. To umožňuje snadno ukládat a spravovat nastavení aplikace na jednom místě, který je oddělen od kódu. V tomto rychlém startu se dozvíte, jak začlenit službu do aplikace Java Spring.

K dokončení kroků v tomto rychlém startu můžete použít jakýkoli editor kódu. Skvělou volbou je však editor [Visual Studio Code](https://code.visualstudio.com/), který je dostupný pro platformy Windows, macOS a Linux.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, nainstalujte podporovanou [Java Development Kit (JDK)](https://aka.ms/azure-jdks) verze 8 a [Apache Maven](http://maven.apache.org/) verze 3.0 nebo vyšší.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

1. Pokud chcete vytvořit novým úložištěm konfigurace aplikace, nejdřív přihlásit k [webu Azure portal](https://aka.ms/azconfig/portal). V levém horním rohu stránky klikněte na **+ Vytvořit prostředek**. V **Hledat na Marketplace** textové pole, typ **konfigurace aplikace** a stiskněte klávesu **Enter**.

    ![Hledat konfigurace aplikace](./media/quickstarts/azure-app-configuration-new.png)

2. Klikněte na tlačítko **konfigurace aplikace** ve výsledcích hledání a následně **vytvořit**.

3. V **konfigurace aplikace** > **vytvořit** stránky, zadejte následující nastavení:

    | Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    | **Název prostředku** | Globálně jedinečný název | Zadejte název jedinečné prostředků pro prostředek aplikace konfigurace úložiště. Název musí být řetězec o délce 1 až 63 znaků a může obsahovat jenom čísla, písmena a znak `-`. Název nesmí začínat ani končit znakem `-` a po sobě jdoucí znaky `-` nejsou platné.  |
    | **Předplatné** | Vaše předplatné | Vyberte předplatné Azure, který chcete použít k otestování konfigurace aplikace. Pokud má váš účet jenom jedno předplatné, vybere se automaticky a rozevírací seznam **Předplatné** se nezobrazí. |
    | **Skupina prostředků** | *AppConfigTestResources* | Vyberte nebo vytvořte skupinu prostředků pro prostředek úložiště konfigurace vaší aplikace. Tato skupina umožňuje uspořádání více prostředků, které pak můžete odstranit najednou tím, že odstraníte skupinu prostředků. Další informace najdete v článku [Použití skupin prostředků ke správě prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
    | **Umístění** | *USA (střed)* | Pomocí **Umístění** určete zeměpisné umístění, ve kterém se prostředek služby SignalR bude hostovat. Pro zajištění nejlepšího výkonu doporučujeme vytvořit prostředek ve stejné oblasti jako ostatní komponenty vaší aplikace. |

    ![Vytvoření úložiště konfigurace aplikace](./media/quickstarts/azure-app-configuration-create.png)

4. Klikněte na možnost **Vytvořit**. Dokončení nasazení může trvat několik minut.

5. Po dokončení nasazení klikněte na tlačítko **nastavení** > **přístupové klíče**. Poznamenejte si buď primární jen pro čtení nebo primární čtení a zápis připojovací řetězec klíče. Bude to použít později ke konfiguraci vaší aplikace ke komunikaci s úložištěm aplikace konfigurace, kterou jste právě vytvořili. Připojovací řetězec má následující formát:

        Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>

    Je potřeba použít celý řetězec v aplikaci.

6. Klikněte na tlačítko **klíč/hodnota Explorer** a **+ vytvořit** přidáte následující páry klíč hodnota:

    | Klíč | Hodnota |
    |---|---|
    | /Application/config.Message | Dobrý den, |

    Ukončíte **popisek** a **typ obsahu** zatím prázdný.

## <a name="create-a-spring-boot-app"></a>Vytvoření aplikace Spring Boot aplikace

Budete používat [Spring Initializr](https://start.spring.io/) k vytvoření nového projektu Spring Boot.

1. Přejděte na <https://start.spring.io/>.

2. Určete následující možnosti:

   * Generování **Maven** projektu s **Java**.
   * Zadejte **Spring Boot** verzi, která je rovna nebo větší než 2,0.
   * Zadejte **skupiny** a **artefaktů** názvy pro vaši aplikaci.
   * Přidat **webové** závislostí.

3. Pokud jste zadali výše uvedených možností, klikněte na tlačítko **generování projektu**. Po zobrazení výzvy, stáhněte si projekt na cestu v místním počítači.

## <a name="connect-to-app-configuration-store"></a>Připojit k úložišti konfigurace aplikace

1. Po extrahování souborů v místním systému vaší jednoduché aplikace Spring Boot budou připraveny pro úpravy. Vyhledejte *pom.xml* souboru v kořenovém adresáři aplikace.

2. Otevřít *pom.xml* souboru v textovém editoru a přidejte počáteční konfigurace Spring cloudu Azure do seznamu `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.RC2</version>
    </dependency>
    ```

3. Vytvořte nový soubor Java *MessageProperties.java* v adresáři balíčku aplikace. Přidejte následující řádky.

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

4. Vytvořte nový soubor Java *HelloController.java* v adresáři balíčku aplikace. Přidejte následující řádky.

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

6. Vytvořte nový soubor s názvem `bootstrap.yaml` v adresáři prostředků vaší aplikace a přidejte následující řádky do souboru a nahraďte ukázkové hodnoty příslušných vlastností pro úložiště konfigurace vaší aplikace.

    ```yaml
    spring:
        cloud:
            azure:
                config:
                    stores:
                        - connection-string: [your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Sestavte a spusťte aplikaci místně

1. Sestavení aplikace Spring Boot pomocí Mavenu a spustit. Příklad:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Jakmile je aplikace spuštěna, můžete použít *curl* k testování aplikace; například:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Měli byste vidět zprávu, která jste zadali do app storu konfigurace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili novým úložištěm konfigurace aplikace a používat s využitím aplikace Java Spring. Navštivte [Spring na domovské stránce Azure](https://docs.microsoft.com/java/azure/spring-framework/) na další informace.

Další informace o použití konfigurace aplikace, pokračujte k dalšímu kurzu, který ukazuje ověření.

> [!div class="nextstepaction"]
> [Spravované identity pro integraci prostředků Azure](./integrate-azure-managed-service-identity.md)