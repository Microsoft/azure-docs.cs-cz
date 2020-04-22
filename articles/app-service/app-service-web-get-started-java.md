---
title: 'Úvodní příručka: Vytvoření aplikace Java ve Windows'
description: Nasaďte svůj první Java Hello World do azure app služby ve Windows během několika minut. Díky pluginu Azure Web App pro Maven je nasazení aplikací Java pohodlné.
keywords: azure, app service, webová aplikace, okna, java, maven, rychlý start
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 6681b2688c7e8884a197ebe27fb784b1a195f4b5
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732164"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Úvodní příručka: Vytvoření aplikace Java ve službě Azure App Service ve Windows

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service ve Windows. Informace o nasazení ve službě App Service na _Linuxu_najdete v [tématu Vytvoření webové aplikace Java v Linuxu](./containers/quickstart-java.md).
>

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.  Tento rychlý start ukazuje, jak používat [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) s [Plugin azure web app pro Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) nasadit soubor webového archivu Java (WAR).

> [!NOTE]
> Totéž lze také provést pomocí populárních ID, jako je IntelliJ a Eclipse. Podívejte se na naše podobné dokumenty na [Azure Toolkit pro IntelliJ Úvodní k tomu](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) nebo Azure [Toolkit pro Eclipse Quickstart](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Ukázková aplikace spuštěná ve službě Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Vytvoření aplikace v Javě

Spusťte následující příkaz Maven ve výzvě prostředí `helloworld`Cloud Shell a vytvořte novou aplikaci s názvem :

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
        <version>1.9.0</version>
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
| `SUBSCRIPTION_ID` | Jedinečné ID předplatného, do kterého chcete aplikaci nasadit. Výchozí ID předplatného lze nalézt z cloudového prostředí `az account show` nebo cli pomocí příkazu. Pro všechna dostupná předplatná `az account list` použijte příkaz.|
| `RESOURCEGROUP_NAME` | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci. Když umístíte všechny prostředky pro aplikaci do skupiny, můžete je spravovat společně. Odstraněním příslušné skupiny prostředků by se například odstranily všechny prostředky, které jsou přidružené k dané aplikaci. Aktualizujte tuto hodnotu jedinečným názvem nové skupiny prostředků, například *myResourceGroup*. Tento název skupiny prostředků použijete v pozdější fázi k vyčištění všech prostředků Azure. |
| `WEBAPP_NAME` | Název aplikace bude součástí názvu hostitele pro aplikaci při nasazení do Azure (WEBAPP_NAME.azurewebsites.net). Aktualizujte tuto hodnotu jedinečným názvem pro novou aplikaci App Service, která bude hostovat vaši aplikaci Java, například *contoso*. |
| `REGION` | Oblast Azure, kde je aplikace hostovaná, například *westus2*. Seznam oblastí můžete získat z Cloud Shellu nebo rozhraní příkazového řádku pomocí příkazu `az account list-locations`. |

## <a name="deploy-the-app"></a>Nasazení aplikace

Pomocí následujícího příkazu nasaďte svoji aplikaci v jazyce Java do Azure:

```bash
mvn package azure-webapp:deploy
```

Po dokončení nasazení přejděte ve webovém prohlížeči pomocí následující adresy URL, například `http://<webapp>.azurewebsites.net/`, do nasazené aplikace.

![Ukázková aplikace spuštěná ve službě Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Blahopřejeme!** Svou první aplikaci Java jste nasadili do služby App Service ve Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Připojení k databázi Azure SQL pomocí Javy](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Připojení k Azure DB pro MySQL pomocí Javy](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Připojení k Azure DB pro PostgreSQL pomocí Javy](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Prostředky pro vývojáře Azure pro Jazyce Java](/java/azure/)

> [!div class="nextstepaction"]
> [Mapování vlastní domény](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Další informace o pluginech Maven pro Azure](https://github.com/microsoft/azure-maven-plugins)
