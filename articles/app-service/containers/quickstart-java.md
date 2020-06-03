---
title: 'Rychlý Start: Vytvoření aplikace pro Linux Java'
description: Začněte s aplikacemi pro Linux v Azure App Service nasazením první aplikace Java do kontejneru Linux v App Service.
keywords: Azure, App Service, Web App, Linux, Java, Maven, rychlý Start
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 1ed7126f2698294ac6706aafcb85e3229a7491bb
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300059"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Rychlý Start: Vytvoření aplikace Java na Azure App Service v systému Linux

[App Service v Linuxu](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů s využitím operačního systému Linux. V tomto rychlém startu se dozvíte, jak používat [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) s [modulem plug-in webové aplikace Azure pro Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) k nasazení souboru webového archivu Java (War) v operačním systému Linux.

> [!NOTE]
>
> Stejné věci je taky možné udělat pomocí oblíbených prostředí pro IDEs, jako je IntelliJ, zatmění a VS Code. Podívejte se na naše podobné dokumenty v [Azure Toolkit for IntelliJ rychlý Start](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app), [Azure Toolkit for Eclipse rychlý Start](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app) nebo [vs Code rychlý Start](https://code.visualstudio.com/docs/java/java-webapp).
>
![Ukázková aplikace spuštěná v Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Vytvoření aplikace v Javě

Spusťte následující příkaz Maven na příkazovém řádku Cloud Shell k vytvoření nové aplikace s názvem `helloworld` :

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" -Dversion=1.0-SNAPSHOT
```
Pak změňte pracovní adresář na složku projektu:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Konfigurace modulu plug-in Maven

Proces nasazení, který Azure App Service, může automaticky vyzvednutí přihlašovacích údajů Azure z Azure CLI. Pokud nemáte nainstalované rozhraní příkazového řádku Azure CLI, modul plug-in Maven vás přihlásí pomocí protokolu OAuth nebo přihlášení zařízení. Pokud potřebujete, Projděte si podrobné informace o [ověřování pomocí modulů plug-in Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authenticatio) .

Pokud chcete nakonfigurovat nasazení, spusťte na příkazovém řádku příkaz Maven a pomocí výchozích konfigurací stiskněte klávesu **ENTER** , dokud se nezobrazí výzva k **potvrzení (a/N)** , stiskněte klávesu **Y** a konfigurace se provede. 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```
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
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=config)

## <a name="deploy-the-app"></a>Nasazení aplikace

Pomocí následujícího příkazu nasaďte svoji aplikaci v jazyce Java do Azure:

```bash
mvn package azure-webapp:deploy
```

Po dokončení nasazení přejděte ve webovém prohlížeči pomocí následující adresy URL, například `http://<webapp>.azurewebsites.net`, do nasazené aplikace. 

![Ukázková aplikace spuštěná v Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gratulujeme!** Nasadili jste svou první aplikaci v Javě do služby App Service v Linuxu.

> [!div class="nextstepaction"]
> [Narazil(a) jsem na problém](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu prostředků z portálu nebo spuštěním následujícího příkazu v Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Spuštění tohoto příkazu může trvat přibližně minut.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení ke službě Azure SQL Database pomocí jazyka Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Připojení k Azure DB for MySQL pomocí Java](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Připojení k Azure DB pro PostgreSQL pomocí Java](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Konfigurace aplikace Java](configure-language-java.md)

> [!div class="nextstepaction"]
> [CI/CD s využitím Jenkinse](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Další materiály pro vývojáře na platformě Java](/java/azure/)

> [!div class="nextstepaction"]
> [Další informace o modulech plug-in Maven pro Azure](https://github.com/microsoft/azure-maven-plugins)
