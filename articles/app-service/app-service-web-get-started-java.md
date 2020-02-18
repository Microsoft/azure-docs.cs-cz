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
ms.openlocfilehash: 3cf759294a31fcf90c5a3f4a6cdc68e3c35882e0
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425342"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Rychlý Start: Vytvoření aplikace Java v Azure App Service ve Windows

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service v systému Windows. Pokud chcete nasadit nástroj na App Service v systému _Linux_, přečtěte si téma [Vytvoření webové aplikace Java v systému Linux](./containers/quickstart-java.md).
>

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.  V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) s [modulem plug-in webové aplikace Azure pro Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) nasadit soubor webového archivu Java (War).

> [!NOTE]
> Stejné věci je taky možné udělat pomocí oblíbených prostředí, jako je IntelliJ a zatmění. Podívejte se na naše podobné dokumenty při [Azure Toolkit for IntelliJ rychlý Start](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) nebo v [rychlém startu Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Ukázková aplikace spuštěná v Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.8.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>SUBSCRIPTION_ID</subscriptionId>
            <resourceGroup>RESOURCEGROUP_NAME</resourceGroup>
            <appName>WEBAPP_NAME</appName>
            <region>REGION</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> V tomto článku pracujeme jenom s aplikacemi Java zabalenými do souborů WAR. Modul plug-in podporuje také webové aplikace JAR. Pokud si to chcete vyzkoušet, přečtěte si téma věnované [nasazení souboru Java SE JAR do služby App Service v Linuxu](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).


V konfiguraci modulu plug-in aktualizujte následující zástupné symboly:

| Zástupný symbol | Popis |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | Jedinečné ID předplatného, na které chcete aplikaci nasadit. ID výchozího předplatného se dá najít z Cloud Shell nebo CLI pomocí příkazu `az account show`. Pro všechna dostupná předplatná použijte příkaz `az account list`.|
| `RESOURCEGROUP_NAME` | Název nové skupiny prostředků, ve které se má vytvořit aplikace Když umístíte všechny prostředky pro aplikaci do skupiny, můžete je spravovat společně. Odstraněním příslušné skupiny prostředků by se například odstranily všechny prostředky, které jsou přidružené k dané aplikaci. Aktualizujte tuto hodnotu jedinečným názvem nové skupiny prostředků, například *myResourceGroup*. Tento název skupiny prostředků použijete v pozdější fázi k vyčištění všech prostředků Azure. |
| `WEBAPP_NAME` | Název aplikace bude součástí názvu hostitele aplikace při nasazení do Azure (WEBAPP_NAME. azurewebsites. NET). Aktualizujte tuto hodnotu jedinečným názvem pro novou aplikaci App Service, která bude hostovat aplikaci Java, třeba *Contoso*. |
| `REGION` | Oblast Azure, ve které je aplikace hostovaná, například *westus2*. Seznam oblastí můžete získat z Cloud Shellu nebo rozhraní příkazového řádku pomocí příkazu `az account list-locations`. |

## <a name="deploy-the-app"></a>Nasazení aplikace

Pomocí následujícího příkazu nasaďte svoji aplikaci v jazyce Java do Azure:

```bash
mvn package azure-webapp:deploy
```

Po dokončení nasazení přejděte ve webovém prohlížeči pomocí následující adresy URL, například `http://<webapp>.azurewebsites.net/`, do nasazené aplikace.

![Ukázková aplikace spuštěná v Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Blahopřejeme!** Nasadili jste svoji první aplikaci Java, abyste App Service ve Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Azure pro vývojáře v jazyce Java – prostředky](/java/azure/)

> [!div class="nextstepaction"]
> [Mapování vlastní domény](app-service-web-tutorial-custom-domain.md)
