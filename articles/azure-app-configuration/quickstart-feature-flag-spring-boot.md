---
title: Rychlý Start pro přidání příznaků funkcí do pružinového spouštění pomocí konfigurace aplikace Azure
description: Přidání příznaků funkcí do aplikací pro spouštění pružin a jejich správa pomocí konfigurace aplikací Azure
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.author: lcozzens
ms.custom: devx-track-java
ms.openlocfilehash: 220d446990a9c362c30b3352e247af2b76d3d128
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767667"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Rychlý Start: Přidání příznaků funkcí do aplikace na jaře Boot

V tomto rychlém startu zařadíte konfiguraci aplikací Azure do webové aplikace na jaře Boot, která vytvoří ucelenou implementaci správy funkcí. Službu konfigurace aplikací můžete použít k centrálnímu ukládání všech příznaků funkcí a řízení jejich stavů.

Knihovny pro správu funkcí pružiny rozšiřuje rámec s kompletní podporou příznaků funkcí. Tyto **knihovny nemají závislost** na žádné knihovně Azure. Bez problémů se integruje s konfigurací aplikací prostřednictvím poskytovatele konfigurace spouštění pružiny.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Podporovaná [sada Java Development Kit SDK](https://docs.microsoft.com/java/azure/jdk) s verzí 8.
* [Apache Maven](https://maven.apache.org/download.cgi) verze 3,0 nebo vyšší.

## <a name="create-an-app-configuration-instance"></a>Vytvoření instance konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Vyberte **správce funkcí**  >  **+ Přidat** a přidejte příznak funkce s názvem `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Povolit příznak funkce s názvem beta](media/add-beta-feature-flag.png)

    `label`Pro teď nechte nedefinovaného.

## <a name="create-a-spring-boot-app"></a>Vytvoření aplikace pro spouštění pružiny

Pomocí [jarního Initializru](https://start.spring.io/) vytvořte nový projekt pro spuštění pružiny.

1. Přejděte na adresu <https://start.spring.io/>.

1. Zadejte následující možnosti:

   * Vygenerujte projekt **Maven** v **Javě**.
   * Zadejte verzi pro **jarní spuštění** , která je rovna nebo větší než 2,0.
   * Zadejte názvy skupiny (**Group**) a artefaktu (**Artifact**) pro vaši aplikaci.  Tento článek používá `com.example` a `demo` .
   * Přidejte **webovou závislost pružiny** .

1. Po zadání předchozích možností vyberte **generovat projekt**. Po zobrazení výzvy Stáhněte projekt do svého místního počítače.

## <a name="add-feature-management"></a>Přidat správu funkcí

1. Po extrahování souborů v místním systému je vaše aplikace pro spouštění pružiny připravená k úpravám. Vyhledejte  *pom.xml* v kořenovém adresáři aplikace.

1. V textovém editoru otevřete soubor *pom.xml* a přidejte následující text do seznamu `<dependencies>` :

    **Jarní Cloud 1.1. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
        <version>1.1.5</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.5</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

    **Jarní Cloud 1.2. x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
        <version>1.2.7</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.2.7</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> Je k dispozici knihovna správy neweb Feature, která nemá závislost na jaře-Web. Rozdíly najdete v [dokumentaci](https://github.com/microsoft/spring-cloud-azure) k GitHubu.

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

1. Přejděte do `resources` adresáře aplikace a otevřete `bootstrap.properties` .  Pokud soubor neexistuje, vytvořte ho. Do souboru přidejte následující řádek.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Na portálu konfigurace aplikace pro úložiště konfigurace vyberte `Access keys` z tohoto bočního panelu. Vyberte kartu klíče jen pro čtení. Zkopírujte hodnotu primárního připojovacího řetězce.

1. Přidejte primární připojovací řetězec jako proměnnou prostředí pomocí názvu proměnné `APP_CONFIGURATION_CONNECTION_STRING` .

1. Otevřete soubor hlavní aplikace v jazyce Java a přidejte `@EnableConfigurationProperties` ho a povolte tuto funkci.

    ```java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.boot.context.properties.EnableConfigurationProperties;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. V adresáři balíčku aplikace vytvořte nový soubor Java s názvem *MessageProperties. Java* .

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.context.annotation.Configuration;

    @Configuration
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

1. V adresáři balíčku aplikace vytvořte nový soubor Java s názvem *HelloController. Java* .

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;

    import com.microsoft.azure.spring.cloud.feature.manager.FeatureManager;
    import org.springframework.web.bind.annotation.GetMapping;


    @Controller
    @ConfigurationProperties("controller")
    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabledAsync("featureManagement.Beta").block());
            return "welcome";
        }
    }
    ```

1. V adresáři templates vaší aplikace vytvořte nový soubor HTML s názvem *welcome.html* .

    ```html
    <!DOCTYPE html>
    <html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <title>Feature Management with Spring Cloud Azure</title>

        <link rel="stylesheet" href="/css/main.css">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>

    </head>
    <body>
        <header>
        <!-- Fixed navbar -->
        <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
            <a class="navbar-brand" href="#">TestFeatureFlags</a>
            <button class="navbar-toggler" aria-expanded="false" aria-controls="navbarCollapse" aria-label="Toggle navigation" type="button" data-target="#navbarCollapse" data-toggle="collapse">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarCollapse">
            <ul class="navbar-nav mr-auto">
                <li class="nav-item active">
                <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
                </li>
                <li class="nav-item" th:if="${Beta}">
                <a class="nav-link" href="#">Beta</a>
                </li>
                <li class="nav-item">
                <a class="nav-link" href="#">Privacy</a>
                </li>
            </ul>
            </div>
        </nav>
        </header>
        <div class="container body-content">
            <h1 class="mt-5">Welcome</h1>
            <p>Learn more about <a href="https://github.com/microsoft/spring-cloud-azure/blob/master/spring-cloud-azure-feature-management/README.md">Feature Management with Spring Cloud Azure</a></p>

        </div>
        <footer class="footer">
            <div class="container">
            <span class="text-muted">&copy; 2019 - Projects</span>
        </div>

        </footer>
    </body>
    </html>

    ```

1. Vytvořte novou složku s názvem CSS pod `static` a uvnitř IT novým souborem CSS s názvem *Main. CSS*.

    ```css
    html {
     position: relative;
     min-height: 100%;
    }
    body {
     margin-bottom: 60px;
    }
    .footer {
     position: absolute;
     bottom: 0;
     width: 100%;
     height: 60px;
     line-height: 60px;
     background-color: #f5f5f5;
    }

    body > .container {
     padding: 60px 15px 0;
    }

    .footer > .container {
     padding-right: 15px;
     padding-left: 15px;
    }

    code {
     font-size: 80%;
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Sestavte aplikaci Spring Boot pomocí Mavenu a spusťte ji.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Otevřete okno prohlížeče a použijte adresu URL: `http://localhost:8080/welcome` .

    ![Spuštění aplikace pro rychlý Start – místní](./media/quickstarts/spring-boot-feature-flag-local-before.png)

1. Na portálu konfigurace aplikace vyberte **správce funkcí**a změňte stav **beta** klíče na **zapnuto**:

    | Klíč | Stav |
    |---|---|
    | Beta | Zapnout |

1. Aktualizujte stránku prohlížeče, aby se zobrazilo nové nastavení konfigurace.

    ![Spuštění aplikace pro rychlý Start – místní](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nové úložiště konfigurace aplikací a použili ho ke správě funkcí v rámci webové aplikace ve jarních aplikacích pomocí [knihoven pro správu funkcí](https://go.microsoft.com/fwlink/?linkid=2074664).

* Přečtěte si další informace o [správě funkcí](./concept-feature-management.md).
* [Správa příznaků funkcí](./manage-feature-flags.md).
* [Používejte příznaky funkcí v aplikaci v jádru pro pružinové spouštění](./use-feature-flags-spring-boot.md).
