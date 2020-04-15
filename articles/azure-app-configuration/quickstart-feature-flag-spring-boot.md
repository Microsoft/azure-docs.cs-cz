---
title: Úvodní příručka pro přidání příznaků funkcí do jarního spuštění pomocí konfigurace aplikace Azure
description: Přidání příznaků funkcí do aplikací spring boot a jejich správa pomocí konfigurace aplikací Azure
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: lcozzens
ms.openlocfilehash: 398005b37660cfa68936a0c8b3a3d90da8160e49
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309075"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Úvodní příručka: Přidání příznaků funkcí do aplikace Spring Boot

V tomto rychlém startu začleníte Azure App Configuration do webové aplikace Spring Boot a vytvoříte komplexní implementaci správy funkcí. Službu Konfigurace aplikace můžete použít k centrálnímu ukládání všech příznaků funkcí a řízení jejich stavů.

Knihovny Správy funkcí jarního spouštění rozšiřují rámec o komplexní podporu příznaku funkce. Tyto knihovny **nemají** závislost na žádné knihovny Azure. Hladce se integrují s aplikací App Configuration prostřednictvím svého poskytovatele konfigurace spring boot.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Podporovaná [java development kit sdk](https://docs.microsoft.com/java/azure/jdk) s verzí 8.
* [Apache Maven](https://maven.apache.org/download.cgi) verze 3.0 nebo vyšší.

## <a name="create-an-app-configuration-instance"></a>Vytvoření instance konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Správce** > funkcí **+Přidat,** `Beta`chcete-li přidat příznak prvku s názvem .

    > [!div class="mx-imgBorder"]
    > ![Povolit příznak funkce s názvem Beta](media/add-beta-feature-flag.png)

    Prozatím `label` nenechte být nedefinovaní.

## <a name="create-a-spring-boot-app"></a>Vytvoření aplikace jarního spuštění

Pomocí [pružinového inicializru vytvořte](https://start.spring.io/) nový projekt jarního startu.

1. Přejděte na <https://start.spring.io/>.

1. Zadejte následující možnosti:

   * Vygenerujte projekt **Maven** v **Javě**.
   * Zadejte verzi **jarního spuštění,** která je rovna nebo větší než 2.0.
   * Zadejte názvy skupiny (**Group**) a artefaktu (**Artifact**) pro vaši aplikaci.  Tento článek `com.example` `demo`používá a .
   * Přidejte závislost **spring web.**

1. Po zadání předchozích možností vyberte **Generovat projekt**. Po zobrazení výzvy stáhněte projekt do místního počítače.

## <a name="add-feature-management"></a>Přidání správy funkcí

1. Po extrahování souborů v místním systému je aplikace Spring Boot připravena k úpravám. Vyhledejte *pom.xml* v kořenovém adresáři aplikace.

1. Otevřete soubor *pom.xml* v textovém editoru `<dependencies>`a do seznamu .:

### <a name="spring-cloud-11x"></a>Jarní mrak 1.1.x

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

### <a name="spring-cloud-12x"></a>Jarní mrak 1.2.x

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.2</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.2.2</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> K dispozici je knihovna správy funkcí, která není webová, která nemá závislost na jarním webu. Rozdíly naleznete v [dokumentaci githubu.](https://github.com/microsoft/spring-cloud-azure/tree/master/spring-cloud-azure-feature-management)

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikací

1. Přejděte `resources` do adresáře aplikace `bootstrap.properties`a otevřete .  Pokud soubor neexistuje, vytvořte jej. Přidejte do souboru následující řádek.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Na portálu Konfigurace aplikace pro vaše `Access keys` úložiště konfigurace vyberte z postranního panelu. Vyberte kartu Klíče jen pro čtení. Zkopírujte hodnotu primárního připojovacího řetězce.

1. Přidejte primární připojovací řetězec jako `APP_CONFIGURATION_CONNECTION_STRING`proměnnou prostředí pomocí názvu proměnné .

1. Otevřete hlavní soubor Java `@EnableConfigurationProperties` aplikace a přidejte tuto funkci.

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

1. Vytvořte nový soubor Java s názvem *MessageProperties.java* v adresáři balíčků vaší aplikace.

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

1. Vytvořte nový soubor Java s názvem *HelloController.java* v adresáři balíčků vaší aplikace.

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
            model.addAttribute("Beta", featureManager.isEnabledAsync("Beta").block());
            return "welcome";
        }
    }
    ```

1. Vytvořte nový soubor HTML s názvem *welcome.html* v adresáři šablon vaší aplikace.

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

1. Vytvořte novou složku s `static` názvem CSS pod a uvnitř něj nový soubor CSS s názvem *main.css*.

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

## <a name="build-and-run-the-app-locally"></a>Sestavení a spuštění aplikace místně

1. Sestavte aplikaci Spring Boot pomocí Mavenu a spusťte ji.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Otevřete okno prohlížeče a přejděte `http://localhost:8080/welcome`na adresu URL: .

    ![Spuštění aplikace QuickStart místní](./media/quickstarts/spring-boot-feature-flag-local-before.png)

1. Na portálu Konfigurace aplikace vyberte **Správce funkcí**a změňte stav klíče **Beta** **na Zapnuto**:

    | Klíč | Stav |
    |---|---|
    | Beta | Zapnuto |

1. Aktualizujte stránku prohlížeče, abyste viděli nové nastavení konfigurace.

    ![Spuštění aplikace QuickStart místní](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nový obchod Skonfigurací aplikací a použili jste ho ke správě funkcí ve webové aplikaci Spring Boot prostřednictvím [knihoven správy funkcí](https://go.microsoft.com/fwlink/?linkid=2074664).

* Další informace o [správě funkcí](./concept-feature-management.md).
* [Spravovat příznaky funkcí](./manage-feature-flags.md).
* [Používejte příznaky funkcí v aplikaci Spring Boot Core](./use-feature-flags-spring-boot.md).
