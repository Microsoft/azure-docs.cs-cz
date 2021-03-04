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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 64e4c05e9439c164329dede5d714bec160bc5ae2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050371"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Rychlý Start: Vytvoření aplikace Java na Azure App Service

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.  V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového [řádku Azure](/cli/azure/get-started-with-azure-cli) s [modulem plug-in webové aplikace Azure pro Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) nasadit soubor. jar nebo soubor. War. 

K dispozici jsou také verze rozhraní IDE tohoto článku. Projděte si [Azure Toolkit for IntelliJ rychlý Start](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) nebo [Azure Toolkit for Eclipse rychlý Start](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).

## <a name="1-prepare-your-environment"></a>1. Příprava prostředí

Než začnete, musíte mít následující:

+ Je <abbr title="Profil, který uchovává fakturační informace pro využití Azure">Účet Azure</abbr> s aktivní <abbr title="Základní organizační struktura, ve které spravujete prostředky v Azure, obvykle přidružené k jednotlivcům nebo oddělením v rámci organizace.">předplatné</abbr>. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli)

+ [Sada Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), verze 8 nebo 11.

+ [Apache Maven](https://maven.apache.org)verze 3,0 nebo vyšší.

Modul plug-in Maven používá k nasazení do App Services přihlašovací údaje účtu z Azure CLI. Než budete pokračovat, [Přihlaste se pomocí Azure CLI](/cli/azure/authenticate-azure-cli) . Další informace najdete v tématu [ověřování pomocí modulů plug-in Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

```azurecli
az login
```

<br>
<hr/>

## <a name="2-create-a-java-app"></a>2. Vytvoření aplikace Java

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

<br>
<hr/>

## <a name="3-configure-the-maven-plugin"></a>3. konfigurace modulu plug-in Maven

Spuštěním následujícího příkazu Maven nakonfigurujte nasazení. Tento příkaz vám pomůže nastavit App Service operační systém, verzi Java a verzi Tomcat.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Při zobrazení výzvy s možností **Subscription** (Předplatné) vyberte správné `Subscription` zadáním čísla zobrazeného na začátku řádku.
1. Po zobrazení výzvy s možností **webové aplikace** přijměte výchozí možnost `<create>` stisknutím klávesy ENTER nebo výběrem existující aplikace.
1. Po zobrazení výzvy s možností **OS** vyberte **Windows** zadáním `3` .
1. Po zobrazení výzvy s možností **cenové úrovně** vyberte **B2** zadáním `2` .
1. Pomocí výchozí verze Java, **Java 8**, stiskněte klávesu ENTER.
1. Stisknutím klávesy Enter u poslední výzvy potvrďte svůj výběr.

    Váš souhrnný výstup bude vypadat podobně jako fragment uvedený níže.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
    </pre>

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Při zobrazení výzvy s možností **Subscription** (Předplatné) vyberte správné `Subscription` zadáním čísla zobrazeného na začátku řádku.
1. Po zobrazení výzvy s možností **webové aplikace** přijměte výchozí možnost `<create>` stisknutím klávesy ENTER nebo výběrem existující aplikace.
1. Po zobrazení výzvy s možností **OS** vyberte **Windows** zadáním `3` .
1. Po zobrazení výzvy s možností **cenové úrovně** vyberte **B2** zadáním `2` .
1. Pomocí výchozí verze Java, **Java 8**, stiskněte klávesu ENTER.
1. Pomocí výchozího webového kontejneru **Tomcat 8,5** stiskněte klávesu ENTER.
1. Stisknutím klávesy Enter u poslední výzvy potvrďte svůj výběr.

    Váš souhrnný výstup bude vypadat podobně jako fragment uvedený níže.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
    </pre>

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. Při zobrazení výzvy s možností **Subscription** (Předplatné) vyberte správné `Subscription` zadáním čísla zobrazeného na začátku řádku.
1. Po zobrazení výzvy s možností **webové aplikace** přijměte výchozí možnost `<create>` stisknutím klávesy ENTER nebo výběrem existující aplikace.
1. Po zobrazení výzvy s možností **OS** vyberte **Linux** stisknutím klávesy ENTER.
1. Po zobrazení výzvy s možností **cenové úrovně** vyberte **B2** zadáním `2` .
1. Pomocí výchozí verze Java, **Java 8**, stiskněte klávesu ENTER.
1. Stisknutím klávesy Enter u poslední výzvy potvrďte svůj výběr.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
    </pre>

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Při zobrazení výzvy s možností **Subscription** (Předplatné) vyberte správné `Subscription` zadáním čísla zobrazeného na začátku řádku.
1. Po zobrazení výzvy s možností **webové aplikace** přijměte výchozí možnost `<create>` stisknutím klávesy ENTER nebo výběrem existující aplikace.
1. Po zobrazení výzvy s možností **OS** vyberte **Linux** stisknutím klávesy ENTER.
1. Po zobrazení výzvy s možností **cenové úrovně** vyberte **B2** zadáním `2` .
1. Pomocí výchozí verze Java, **Java 8**, stiskněte klávesu ENTER.
1. Pomocí výchozího webového kontejneru **Tomcat 8,5** stiskněte klávesu ENTER.
1. Stisknutím klávesy Enter u poslední výzvy potvrďte svůj výběr.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
    </pre>

---

::: zone-end

Konfigurace pro App Service můžete v `pom.xml` případě potřeby upravit přímo. Níže jsou uvedeny některé běžné položky:

Vlastnost | Povinné | Popis | Verze
---|---|---|---
`<schemaVersion>` | false (nepravda) | Zadejte verzi schématu konfigurace. Podporované hodnoty jsou: `v1` , `v2` . | 1.5.2
`<subscriptionId>` | false (nepravda) | Zadejte ID předplatného. | 0.1.0 +
`<resourceGroup>` | true | Azure <abbr title="Logický kontejner pro související prostředky Azure, které můžete spravovat jako jednotku.">skupina prostředků</abbr> pro vaši webovou aplikaci. | 0.1.0 +
`<appName>` | true | Název vaší webové aplikace | 0.1.0 +
`<region>` | true | Určuje oblast, do které se webová aplikace hostuje. Výchozí hodnota je **westeurope**. Všechny platné oblasti v části [podporované oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=app-service) . | 0.1.0 +
`<pricingTier>` | false (nepravda) | Cenová úroveň pro vaši webovou aplikaci. Výchozí hodnota je **P1V2** pro produkční úlohy, zatímco **B2** je Doporučené minimum pro vývoj a testování Java. [Další informace](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0 +
`<runtime>` | true | Konfigurace běhového prostředí můžete zobrazit podrobnosti [zde](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0 +
`<deployment>` | true | Konfigurace nasazení vám může [Zobrazit podrobnosti.](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details) | 0.1.0 +

Buďte opatrní na hodnoty `<appName>` a `<resourceGroup>` ( `helloworld-1590394316693` a `helloworld-1590394316693-rg` následně v příkladu výstupu), budou použity později.

[Nahlášení problému](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

<br>
<hr/>

## <a name="4-deploy-the-app"></a>4. nasazení aplikace

Nasaďte aplikaci Java do Azure pomocí následujícího příkazu.

```bash
mvn package azure-webapp:deploy
```

Po dokončení nasazení bude vaše aplikace připravená na `http://<appName>.azurewebsites.net/` . Otevřete adresu URL v místním webovém prohlížeči. Měl by se zobrazit následující zobrazení:

![Ukázková aplikace spuštěná v Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci v jazyce Java, abyste ji App Service.

[Nahlášení problému](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. vyčištění prostředků

Odstraňte aplikaci Java a její související prostředky, abyste se vyhnuli dalším nákladům.

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Spuštění tohoto příkazu může trvat přibližně minut.

<br>
<hr/>

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
