---
title: 'Úvodní příručka: Vytvoření linuxové java aplikace'
description: Začínáme s linuxovými aplikacemi ve službě Azure App Service nasazením první aplikace Java do kontejneru Linuxu ve službě App Service.
keywords: azure, app service, webová aplikace, linux, java, maven, quickstart
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 2192260c3106a6b8d3d3ffd0e063ee945734f308
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79136308"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Úvodní příručka: Vytvoření aplikace Java ve službě Azure App Service na Linuxu

[App Service v Linuxu](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů s využitím operačního systému Linux. Tento rychlý start ukazuje, jak používat [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) s [Plugin azure web app pro Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) nasadit soubor webového archivu Java (WAR) na operačním systému Linux.

> [!NOTE]
>
> Totéž lze také provést pomocí populárních IDE, jako je IntelliJ, Eclipse a VS Code. Podívejte se na naše podobné dokumenty na [Azure Toolkit pro IntelliJ Úvodní k vychytaná,](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) [Azure Toolkit pro Eclipse Úvodní k tomu](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app) nebo [VS Code Quickstart](https://code.visualstudio.com/docs/java/java-webapp).
>
![Ukázková aplikace spuštěná ve službě Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Vytvoření aplikace v Javě

Spusťte následující příkaz Maven ve výzvě prostředí `helloworld`Cloud Shell a vytvořte novou aplikaci s názvem :

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp"
```
Potom změňte pracovní adresář do složky projektu:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Konfigurace modulu plug-in Maven

Proces nasazení do služby Azure App Service používá přihlašovací údaje účtu z azure cli. [Před pokračováním se přihlaste pomocí příkazového příkazového příkazu k Řešení Azure.](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

```azurecli
az login
```

Poté můžete nakonfigurovat nasazení, spustit příkaz maven v příkazovém řádku a použít výchozí konfigurace stisknutím klávesy **ENTER,** dokud nezískáte výzvu **Potvrdit (Y/N),** pak stiskněte **tlačítko "y"** a konfigurace je hotová. 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.0:config
```
Ukázkový proces vypadá takto:

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.0:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.0:config (default-cli) @ helloworld ---
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

> [!NOTE]
> V tomto článku pracujeme jenom s aplikacemi Java zabalenými do souborů WAR. Modul plug-in podporuje také webové aplikace JAR. Pokud si to chcete vyzkoušet, přečtěte si téma věnované [nasazení souboru Java SE JAR do služby App Service v Linuxu](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

Přejděte `pom.xml` znovu vidět konfigurace pluginu je aktualizován, Můžete upravit další konfigurace pro App Service přímo v pom souboru v případě potřeby, některé běžné jsou uvedeny níže:

 Vlastnost | Požaduje se | Popis | Version
---|---|---|---
`<schemaVersion>` | false (nepravda) | Zadejte verzi schématu konfigurace. Podporované hodnoty `v1`jsou: , `v2`. | 1.5.2
`<resourceGroup>` | true | Skupina prostředků Azure pro webovou aplikaci. | 0.1.0+
`<appName>` | true | Název webové aplikace. | 0.1.0+
`<region>` | true | Určuje oblast, kde bude webová aplikace hostována. výchozí hodnota je **westeurope**. Všechny platné oblasti v části [Podporované oblasti.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) | 0.1.0+
`<pricingTier>` | false (nepravda) | Cenová úroveň pro webovou aplikaci. Výchozí hodnota je **P1V2**.| 0.1.0+
`<runtime>` | true | Konfigurace prostředí runtime, můžete vidět podrobnosti [zde](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+
`<deployment>` | true | Konfigurace nasazení, můžete vidět podrobnosti [zde](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0+

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=config)

## <a name="deploy-the-app"></a>Nasazení aplikace

Pomocí následujícího příkazu nasaďte svoji aplikaci v jazyce Java do Azure:

```bash
mvn package azure-webapp:deploy
```

Po dokončení nasazení přejděte ve webovém prohlížeči pomocí následující adresy URL, například `http://<webapp>.azurewebsites.net`, do nasazené aplikace. 

![Ukázková aplikace spuštěná ve službě Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Blahopřejeme!** Nasadili jste svou první aplikaci v Javě do služby App Service v Linuxu.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. Pokud neočekáváte, že budete tyto prostředky v budoucnu potřebovat, odstraňte skupinu prostředků z portálu nebo spuštěním následujícího příkazu v prostředí Cloud:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Spuštění tohoto příkazu může trvat přibližně minut.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení k databázi Azure SQL pomocí Javy](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Připojení k Azure DB pro MySQL pomocí Javy](/azure/mysql/connect-java?toc=/azure/java/toc.json)

> [!div class="nextstepaction"]
> [Připojení k Azure DB pro PostgreSQL pomocí Javy](/azure/postgresql/connect-java?toc=/azure/java/toc.json)

> [!div class="nextstepaction"]
> [Konfigurace aplikace Java](configure-custom-container.md)

> [!div class="nextstepaction"]
> [CI/CD s využitím Jenkinse](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Další prostředky Azure pro vývojáře Vivy](/java/azure/)

> [!div class="nextstepaction"]
> [Další informace o pluginech Maven pro Azure](https://github.com/microsoft/azure-maven-plugins)
