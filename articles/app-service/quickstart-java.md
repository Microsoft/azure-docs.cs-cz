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
ms.openlocfilehash: 8289b21da5009459d2eb7ddc8d26b549f0920317
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2020
ms.locfileid: "88085039"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Rychlý Start: Vytvoření aplikace Java na Azure App Service

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.  V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) s [modulem plug-in webové aplikace Azure pro Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) nasadit soubor webového archivu Java (War).

> [!NOTE]
> Totéž můžete také udělat pomocí oblíbených prostředí, jako je IntelliJ a zatmění. Podívejte se na naše podobné dokumenty při [Azure Toolkit for IntelliJ rychlý Start](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) nebo v [rychlém startu Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).
>
![Ukázková aplikace spuštěná v Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Vytvoření aplikace v Javě

Spusťte následující příkaz Maven na příkazovém řádku Cloud Shell k vytvoření nové aplikace s názvem `helloworld` :

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Pak změňte pracovní adresář na složku projektu:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Konfigurace modulu plug-in Maven

Proces nasazení, který Azure App Service, může automaticky z Azure CLI převzít vaše přihlašovací údaje Azure. Modul plug-in Maven vás přihlásí pomocí protokolu OAuth nebo přihlášení zařízení, pokud není rozhraní příkazového řádku Azure nainstalované místně. Pokud potřebujete, Projděte si podrobné informace o [ověřování pomocí modulů plug-in Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) .

Spuštěním následujícího příkazu Maven můžete nakonfigurovat nasazení.
```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

::: zone pivot="platform-windows"  
Zobrazí se výzva k výběru 
* **Operační systém (výchozí: `linux` )**
* **Java verze (výchozí: `1.8` )**
* **Webový kontejner (výchozí: `tomcat 8.5` )** 

Při **`2`** výběru operačního **systému Windows** v prvním kroku buďte opatrní. Ostatní konfigurace můžou být ponechány ve výchozím nastavení stisknutím klávesy **ENTER**. Nakonec stiskněte **`Y`** tlačítko **Potvrdit (a/N)** s výzvou k dokončení konfigurace.

Vzorový proces vypadá takto:

```console
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use: 2
Define value for javaVersion(Default: 1.8): 
1. 1.7
2. 1.7.0_191_ZULU
3. 1.7.0_51
4. 1.7.0_71
5. 1.7.0_80
6. 1.8 [*]
7. 1.8.0_102
8. 1.8.0_111
9. 1.8.0_144
10. 1.8.0_172
11. 1.8.0_172_ZULU
12. 1.8.0_181
13. 1.8.0_181_ZULU
14. 1.8.0_202
15. 1.8.0_202_ZULU
16. 1.8.0_25
17. 1.8.0_60
18. 1.8.0_73
19. 1.8.0_92
20. 11
21. 11.0.2_ZULU
Enter index to use:
Define value for webContainer(Default: tomcat 8.5): 
1. jetty 9.1
2. jetty 9.1.0.20131115
3. jetty 9.3
4. jetty 9.3.13.20161014
5. tomcat 7.0
6. tomcat 7.0.50
7. tomcat 7.0.62
8. tomcat 8.0
9. tomcat 8.0.23
10. tomcat 8.5 [*]
11. tomcat 8.5.20
12. tomcat 8.5.31
13. tomcat 8.5.34
14. tomcat 8.5.37
15. tomcat 8.5.6
16. tomcat 9.0
17. tomcat 9.0.0
18. tomcat 9.0.12
19. tomcat 9.0.14
20. tomcat 9.0.8
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1590394316693
ResourceGroup : helloworld-1590394316693-rg
Region : westeurope
PricingTier : PremiumV2_P1v2
OS : Windows
Java : 1.8
WebContainer : tomcat 8.5
Deploy to slot : false
Confirm (Y/N)? :
[INFO] Saving configuration to pom.
```
::: zone-end
::: zone pivot="platform-linux"  
Vzorový proces vypadá takto:

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. Java 11
2. Java 8 [*]
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. TOMCAT 8.5 [*]
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```
::: zone-end

> [!NOTE]
> V tomto článku pracujeme jenom s aplikacemi Java zabalenými do souborů WAR. Modul plug-in podporuje také webové aplikace JAR. Pokud si to chcete vyzkoušet, přečtěte si téma věnované [nasazení souboru Java SE JAR do služby App Service v Linuxu](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

Otevřete `pom.xml` a zobrazte aktualizovanou konfiguraci.

```bash
code pom.xml
```

Konfigurace pro App Service můžete v případě potřeby upravit přímo v souboru pom:

 Vlastnost | Povinné | Popis | Verze
---|---|---|---
`<schemaVersion>` | false (nepravda) | Zadejte verzi schématu konfigurace. Podporované hodnoty jsou: `v1` , `v2` . | 1.5.2
`<resourceGroup>` | true | Skupina prostředků Azure pro vaši webovou aplikaci | 0.1.0 +
`<appName>` | true | Název vaší webové aplikace | 0.1.0 +
`<region>` | true | Určuje oblast, do které se webová aplikace hostuje. Výchozí hodnota je **westeurope**. Všechny platné oblasti v části [podporované oblasti](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) . | 0.1.0 +
`<pricingTier>` | false (nepravda) | Cenová úroveň pro vaši webovou aplikaci. Výchozí hodnota je **P1V2**.| 0.1.0 +
`<runtime>` | true | Konfigurace běhového prostředí můžete zobrazit podrobnosti [zde](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0 +
`<deployment>` | true | Konfigurace nasazení vám může [Zobrazit podrobnosti.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) | 0.1.0 +

::: zone pivot="platform-windows"
Buďte opatrní na hodnoty `<appName>` a `<resourceGroup>` ( `helloworld-1590394316693` a `helloworld-1590394316693-rg` odpovídajícím způsobem v ukázce), budou použity později.
::: zone-end
::: zone pivot="platform-linux"
::: zone-end
> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Nasazení aplikace

Proces nasazení Azure App Service používá přihlašovací údaje účtu z Azure CLI. Než budete pokračovat, [Přihlaste se pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

```azurecli
az login
```
Potom můžete aplikaci Java nasadit do Azure pomocí následujícího příkazu:

```bash
mvn package azure-webapp:deploy
```

::: zone pivot="platform-windows"
Po dokončení nasazení bude vaše aplikace připravená na `http://<appName>.azurewebsites.net/` ( `http://helloworld-1590394316693.azurewebsites.net` v ukázce). Otevřete adresu URL v místním webovém prohlížeči, měli byste vidět

![Ukázková aplikace spuštěná v Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci Java, abyste App Service ve Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]
::: zone-end
::: zone pivot="platform-linux"
Po dokončení nasazení přejděte ve webovém prohlížeči pomocí následující adresy URL, například `http://<webapp>.azurewebsites.net`, do nasazené aplikace. 

![Ukázková aplikace spuštěná v Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Blahopřejeme!** Nasadili jste svou první aplikaci v Javě do služby App Service v Linuxu.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu prostředků z portálu nebo spuštěním následujícího příkazu v Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Spuštění tohoto příkazu může trvat přibližně minut.
::: zone-end

Po dokončení nasazení bude vaše aplikace připravená na `http://<appName>.azurewebsites.net/` ( `http://helloworld-1590394316693.azurewebsites.net` v ukázce). Otevřete adresu URL v místním webovém prohlížeči, měli byste vidět

![Ukázková aplikace spuštěná v Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci v jazyce Java, abyste ji App Service.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?quickstart-java&step=deploy)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Připojení k Azure SQL Database pomocí Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Připojení k Azure DB for MySQL pomocí Java](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Připojení k Azure DB pro PostgreSQL pomocí Java](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Azure pro vývojáře v jazyce Java – prostředky](/java/azure/)

> [!div class="nextstepaction"]
> [Konfigurace aplikace Java](configure-language-java.md)

> [!div class="nextstepaction"]
> [CI/CD s využitím Jenkinse](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Mapování vlastní domény](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Další informace o modulech plug-in Maven pro Azure](https://github.com/microsoft/azure-maven-plugins)