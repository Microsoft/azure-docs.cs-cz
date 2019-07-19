---
title: Vytvoření webové aplikace v jazyce Java v systému Linux – Azure App Service
description: V tomto rychlém startu během několika minut nasadíte svou první aplikaci Hello World v jazyce Java ve službě Azure App Service v Linuxu.
keywords: Azure, App Service, Web App, Linux, Java, Maven, rychlý Start
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 30689e05a2567646ff541818dc68a90c13da7a56
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297260"
---
# <a name="quickstart-create-a-java-app-in-app-service-on-linux"></a>Rychlý start: Vytvoření aplikace Java v App Service v systému Linux

[App Service v Linuxu](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů s využitím operačního systému Linux. V tomto rychlém startu se dozvíte, jak používat [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) s [modulem plug-in Maven pro Azure App Service](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) k nasazení souboru webového archivu Java (War).
> [!NOTE]
>
> Stejné věci je taky možné udělat pomocí oblíbených prostředí, jako je IntelliJ a zatmění. Podívejte se na naše podobné dokumenty při [Azure Toolkit for IntelliJ rychlý Start](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) nebo v rychlém startu [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Vytvoření aplikace v Javě

Spusťte následující příkaz Maven na příkazovém řádku Cloud Shell k vytvoření nové aplikace s názvem `helloworld`:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Konfigurace modulu plug-in Maven

Abyste provedli nasazení z Mavenu, použijte editor kódu v Cloud Shellu k otevření souboru `pom.xml` projektu v adresáři `helloworld`. 

```bash
code pom.xml
```

Pak do elementu `<build>` souboru `pom.xml` přidejte následující definici modulu plug-in:

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Linux           -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
    </plugin>
</plugins>
```

Proces nasazení Azure App Service používá přihlašovací údaje účtu z Azure CLI. Než budete pokračovat, přihlaste [se pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

```azurecli
az login
```

Potom můžete nakonfigurovat nasazení, spustit na příkazovém řádku `mvn azure-webapp:config` příkaz Maven a pomocí výchozích konfigurací stisknout klávesu **ENTER** , dokud nezískáte výzvu k **potvrzení (Y/N)** , stiskněte klávesu Y a konfigurace se provede **.** .

```cmd
~@Azure:~/helloworld$ mvn azure-webapp:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.7.0:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. jre8 [*]
2. java11
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. jre8
3. TOMCAT 8.5 [*]
4. WILDFLY 14
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

`pom.xml` Pokud chcete zobrazit konfiguraci modulu plug-in, můžete v případě potřeby upravit další konfigurace pro App Service přímo v souboru pom:

 Vlastnost | Požadováno | Popis | Version
---|---|---|---
`<schemaVersion>` | false | Zadejte verzi schématu konfigurace. Podporované hodnoty jsou: `v1`, `v2`. | 1.5.2
`<resourceGroup>` | true | Skupina prostředků Azure pro vaši webovou aplikaci | 0.1.0 +
`<appName>` | true | Název vaší webové aplikace | 0.1.0 +
[`<region>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) | true | Určuje oblast, do které se webová aplikace hostuje. Výchozí hodnota je **westus**. Všechny platné oblasti v části [podporované oblasti](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) . | 0.1.0 +
[`<pricingTier>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme##pricingtier) | false | Cenová úroveň pro vaši webovou aplikaci. Výchozí hodnota je **P1V2**.| 0.1.0 +
[`<runtime>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting) | true | Konfigurace běhového prostředí můžete zobrazit podrobnosti [zde](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting). | 0.1.0 +
[`<deployment>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting) | true | Konfigurace nasazení vám může [Zobrazit podrobnosti.](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting) | 0.1.0 +

## <a name="deploy-the-app"></a>Nasazení aplikace

Pomocí následujícího příkazu nasaďte svoji aplikaci v jazyce Java do Azure:

```bash
mvn package azure-webapp:deploy
```

Po dokončení nasazení přejděte ve webovém prohlížeči pomocí následující adresy URL, například `http://<webapp>.azurewebsites.net`, do nasazené aplikace. 

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-java/java-hello-world-in-browser.png)

**Blahopřejeme!** Nasadili jste svou první aplikaci v Javě do služby App Service v Linuxu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. Pokud předpokládáte, že už tyto prostředky nebudete potřebovat, odstraňte skupinu prostředků spuštěním následujícího příkazu ve službě Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Spuštění tohoto příkazu může trvat přibližně minut.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Podniková aplikace Java s PostgreSQL](tutorial-java-enterprise-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace Java](configure-custom-container.md)

> [!div class="nextstepaction"]
> [CI/CD s Jenkinse](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Další materiály pro vývojáře na platformě Java](/java/azure/)
