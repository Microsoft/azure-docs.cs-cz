---
title: 'Rychlý Start: Vytvoření aplikace Java ve Windows'
description: Nasaďte první Hello World Java pro Azure App Service v systému Windows během několika minut. Modul plug-in webové aplikace Azure pro Maven je vhodný pro nasazení aplikací Java.
keywords: Azure, App Service, Web App, Windows, Java, Maven, rychlý Start
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: d8f03d714ab44dc01d9e138a63a89892ead60fe9
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85249461"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Rychlý Start: Vytvoření aplikace Java v Azure App Service ve Windows

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service ve Windows. Pokud chcete nasadit nástroj na App Service v systému _Linux_, přečtěte si téma [Vytvoření webové aplikace Java v systému Linux](./containers/quickstart-java.md).
>

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.  V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) s [modulem plug-in webové aplikace Azure pro Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) nasadit soubor webového archivu Java (War).

> [!NOTE]
> Stejné věci je taky možné udělat pomocí oblíbených prostředí, jako je IntelliJ a zatmění. Podívejte se na naše podobné dokumenty při [Azure Toolkit for IntelliJ rychlý Start](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) nebo v [rychlém startu Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Ukázková aplikace spuštěná v Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

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

Proces nasazení, který Azure App Service, může automaticky vyzvednutí přihlašovacích údajů Azure z Azure CLI. Pokud nemáte nainstalované rozhraní příkazového řádku Azure CLI, modul plug-in Maven vás přihlásí pomocí protokolu OAuth nebo přihlášení zařízení. Pokud potřebujete, Projděte si podrobné informace o [ověřování pomocí modulů plug-in Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) .

Spuštěním následujícího příkazu Maven na příkazovém řádku můžete nakonfigurovat nasazení, vybrat **2** v operačním **systému Windows** v prvním kroku a pak přijmout výchozí konfigurace stisknutím klávesy **ENTER** , dokud nezískáte výzvu k **potvrzení (Y/N)** , stiskněte klávesu **Y** a konfigurace se provede. 

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

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

> [!NOTE]
> V tomto článku pracujeme jenom s aplikacemi Java zabalenými do souborů WAR. Modul plug-in podporuje také webové aplikace JAR. Pokud si to chcete vyzkoušet, přečtěte si téma věnované [nasazení souboru Java SE JAR do služby App Service v Linuxu](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

Otevřete a `pom.xml` Zobrazte aktualizovanou konfiguraci.

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

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=app-service-web-get-started-java&step=config)

## <a name="deploy-the-app"></a>Nasazení aplikace

Proces nasazení Azure App Service používá přihlašovací údaje účtu z Azure CLI. Než budete pokračovat, [Přihlaste se pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

```azurecli
az login
```
Potom můžete aplikaci Java nasadit do Azure pomocí následujícího příkazu:

```bash
mvn package azure-webapp:deploy
```

Po dokončení nasazení přejděte ve webovém prohlížeči pomocí následující adresy URL, například `http://<webapp>.azurewebsites.net/`, do nasazené aplikace.

![Ukázková aplikace spuštěná v Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Gratulujeme!** Nasadili jste svoji první aplikaci Java, abyste App Service ve Windows.

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
> [Mapování vlastní domény](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Další informace o modulech plug-in Maven pro Azure](https://github.com/microsoft/azure-maven-plugins)
