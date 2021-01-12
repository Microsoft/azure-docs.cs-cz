---
title: 'Rychlý Start: Vytvoření aplikace Java na Azure App Service'
description: Nasaďte první Hello World Java do Azure App Service během několika minut. Modul plug-in webové aplikace Azure pro Maven je vhodný pro nasazení aplikací Java.
keywords: Azure, App Service, Web App, Windows, Linux, Java, Maven, rychlý Start
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: 32d7dc913ae19353574e91837b4ea009d9f797c6
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071778"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Rychlý Start: Vytvoření aplikace Java na Azure App Service

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.  V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového [řádku Azure](/cli/azure/get-started-with-azure-cli) s [modulem plug-in webové aplikace Azure pro Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) nasadit soubor. jar nebo soubor. War. Pomocí karet můžete přepínat mezi pokyny Java SE a Tomcat.


> [!NOTE]
> Totéž můžete také udělat pomocí oblíbených prostředí, jako je IntelliJ a zatmění. Podívejte se na naše podobné dokumenty při [Azure Toolkit for IntelliJ rychlý Start](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) nebo v [rychlém startu Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).


![Ukázková aplikace spuštěná v Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Vytvoření aplikace v Javě

# <a name="java-se"></a>[Java SE](#tab/javase)

Naklonujte ukázkový projekt [jarního spuštění Začínáme](https://github.com/spring-guides/gs-spring-boot) .

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

Přejděte do adresáře pro dokončený projekt.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Spusťte následující příkaz Maven na příkazovém řádku Cloud Shell k vytvoření nové aplikace s názvem `helloworld` :

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Pak změňte pracovní adresář na složku projektu:

```bash
cd helloworld
```

---

## <a name="configure-the-maven-plugin"></a>Konfigurace modulu plug-in Maven

Proces nasazování do služby Azure App Service bude automaticky používat vaše přihlašovací údaje k Azure z Azure CLI. Pokud rozhraní příkazového řádku Azure není místně nainstalované, bude se modul plug-in Maven ověřovat pomocí protokolu OAuth nebo přihlášení zařízení. Další informace najdete v tématu [ověřování pomocí modulů plug-in Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

Spuštěním následujícího příkazu Maven nakonfigurujte nasazení. Tento příkaz vám pomůže nastavit App Service operační systém, verzi Java a verzi Tomcat.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Při zobrazení výzvy s možností **Subscription** (Předplatné) vyberte správné `Subscription` zadáním čísla zobrazeného na začátku řádku.
1. Po zobrazení výzvy s možností **webové aplikace** přijměte možnost defaut `<create>` stisknutím klávesy ENTER nebo výběrem existující aplikace.
1. Po zobrazení výzvy s možností **OS** vyberte **Windows** zadáním `3` .
1. Po zobrazení výzvy s možností **cenové úrovně** vyberte **B2** zadáním `2` .
1. Pomocí výchozí verze Java, **Java 8**, stiskněte klávesu ENTER.
1. Stisknutím klávesy Enter u poslední výzvy potvrďte svůj výběr.

    Váš souhrnný výstup bude vypadat podobně jako fragment uvedený níže.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Při zobrazení výzvy s možností **Subscription** (Předplatné) vyberte správné `Subscription` zadáním čísla zobrazeného na začátku řádku.
1. Po zobrazení výzvy s možností **webové aplikace** přijměte možnost defaut `<create>` stisknutím klávesy ENTER nebo výběrem existující aplikace.
1. Po zobrazení výzvy s možností **OS** vyberte **Windows** zadáním `3` .
1. Po zobrazení výzvy s možností **cenové úrovně** vyberte **B2** zadáním `2` .
1. Pomocí výchozí verze Java, **Java 8**, stiskněte klávesu ENTER.
1. Pomocí výchozího webového kontejneru **Tomcat 8,5** stiskněte klávesu ENTER.
1. Stisknutím klávesy Enter u poslední výzvy potvrďte svůj výběr.

    Váš souhrnný výstup bude vypadat podobně jako fragment uvedený níže.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. Při zobrazení výzvy s možností **Subscription** (Předplatné) vyberte správné `Subscription` zadáním čísla zobrazeného na začátku řádku.
1. Po zobrazení výzvy s možností **webové aplikace** přijměte možnost defaut `<create>` stisknutím klávesy ENTER nebo výběrem existující aplikace.
1. Po zobrazení výzvy s možností **OS** vyberte **Linux** stisknutím klávesy ENTER.
1. Po zobrazení výzvy s možností **cenové úrovně** vyberte **B2** zadáním `2` .
1. Pomocí výchozí verze Java, **Java 8**, stiskněte klávesu ENTER.
1. Stisknutím klávesy Enter u poslední výzvy potvrďte svůj výběr.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Při zobrazení výzvy s možností **Subscription** (Předplatné) vyberte správné `Subscription` zadáním čísla zobrazeného na začátku řádku.
1. Po zobrazení výzvy s možností **webové aplikace** přijměte možnost defaut `<create>` stisknutím klávesy ENTER nebo výběrem existující aplikace.
1. Po zobrazení výzvy s možností **OS** vyberte **Linux** stisknutím klávesy ENTER.
1. Po zobrazení výzvy s možností **cenové úrovně** vyberte **B2** zadáním `2` .
1. Pomocí výchozí verze Java, **Java 8**, stiskněte klávesu ENTER.
1. Pomocí výchozího webového kontejneru **Tomcat 8,5** stiskněte klávesu ENTER.
1. Stisknutím klávesy Enter u poslední výzvy potvrďte svůj výběr.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

Konfigurace pro App Service můžete v `pom.xml` případě potřeby upravit přímo. Níže jsou uvedeny některé běžné položky:

Vlastnost | Povinné | Popis | Verze
---|---|---|---
`<schemaVersion>` | false (nepravda) | Zadejte verzi schématu konfigurace. Podporované hodnoty jsou: `v1` , `v2` . | 1.5.2
`<subscriptionId>` | false (nepravda) | Zadejte ID předplatného. | 0.1.0 +
`<resourceGroup>` | true | Skupina prostředků Azure pro vaši webovou aplikaci | 0.1.0 +
`<appName>` | true | Název vaší webové aplikace | 0.1.0 +
`<region>` | true | Určuje oblast, do které se webová aplikace hostuje. Výchozí hodnota je **westeurope**. Všechny platné oblasti v části [podporované oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=app-service) . | 0.1.0 +
`<pricingTier>` | false (nepravda) | Cenová úroveň pro vaši webovou aplikaci. Výchozí hodnota je **P1V2** pro produkční úlohy, zatímco **B2** je Doporučené minimum pro vývoj a testování Java. [Další informace](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0 +
`<runtime>` | true | Konfigurace běhového prostředí můžete zobrazit podrobnosti [zde](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0 +
`<deployment>` | true | Konfigurace nasazení vám může [Zobrazit podrobnosti.](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details) | 0.1.0 +

Buďte opatrní na hodnoty `<appName>` a `<resourceGroup>` ( `helloworld-1590394316693` a `helloworld-1590394316693-rg` odpovídajícím způsobem v ukázce), budou použity později.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Nasazení aplikace

Modul plug-in Maven používá k nasazení do App Services přihlašovací údaje účtu z Azure CLI. Než budete pokračovat, [Přihlaste se pomocí Azure CLI](/cli/azure/authenticate-azure-cli) .

```azurecli
az login
```

Potom můžete aplikaci Java nasadit do Azure pomocí následujícího příkazu.

```bash
mvn package azure-webapp:deploy
```

Po dokončení nasazení bude vaše aplikace připravena na adrese `http://<appName>.azurewebsites.net/` (`http://helloworld-1590394316693.azurewebsites.net` v ukázce). Otevřete adresu URL v místním webovém prohlížeči, měli byste vidět

![Ukázková aplikace spuštěná v Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci v jazyce Java, abyste ji App Service.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. Pokud tyto prostředky už nebudete potřebovat, odstraňte skupinu prostředků z portálu nebo spuštěním následujícího příkazu v Cloud Shellu:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Spuštění tohoto příkazu může trvat přibližně minut.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení k Azure DB pro PostgreSQL pomocí Java](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [Nastavení CI/CD](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [Informace o cenách](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [Agregované protokoly a metriky](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [Vertikální navýšení kapacity](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Azure pro vývojáře v jazyce Java – prostředky](/java/azure/)

> [!div class="nextstepaction"]
> [Konfigurace aplikace Java](configure-language-java.md)
