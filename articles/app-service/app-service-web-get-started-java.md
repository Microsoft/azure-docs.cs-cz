---
title: Vytvoření webové aplikace Java na Windows – služby Azure App Service
description: V tomto rychlém startu nasadíte svou první Java Hello World ve službě Azure App Service na Windows během několika minut.
keywords: Azure, služby app service, web Apps, windows, java, maven, rychlý start
services: app-service\web
documentationcenter: ''
author: msangapu-msft
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jasonfreeberg
ms.custom: mvc
ms.openlocfilehash: c77f7afe3941395a156896135043710252637ef3
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393242"
---
# <a name="quickstart-create-a-java-app-in-app-service"></a>Rychlý start: Vytvoření aplikace v Javě ve službě App Service

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service ve Windows. Nasazení do služby App Service v _Linux_, naleznete v tématu [Java vytvoření webové aplikace v Linuxu](./containers/quickstart-java.md).
>

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.  Tento rychlý start ukazuje, jak používat [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) s [modul plug-in Maven pro Azure App Service](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) k nasazení souboru webového archivu (WAR) Java.

> [!NOTE]
> Je možné provést totéž používat oblíbená prostředí IDE, jako je IntelliJ a Eclipse. Podívejte se na naše dokumenty podobně jako na [sady Azure Toolkit pro IntelliJ Quickstart](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) nebo [sady Azure Toolkit pro Eclipse Quickstart](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Ukázková aplikace spuštěná ve službě Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Vytvoření aplikace v Javě

Ve službě Cloud Shell výzvu k vytvoření nové aplikace s názvem spustíte následující příkaz Mavenu `helloworld`:

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
        <version>1.5.4</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>${SUBSCRIPTION_ID}</subscriptionId>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
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
| `SUBSCRIPTION_ID` | Jedinečné ID předplatného, kterou chcete nasadit aplikaci. ID výchozího předplatného najdete na službě Cloud Shell nebo rozhraní příkazového řádku pomocí `az account show` příkazu. Pro všechna dostupná předplatná, použijte `az account list` příkazu.|
| `RESOURCEGROUP_NAME` | Název pro novou skupinu prostředků, ve kterém chcete vytvořit aplikaci. Když umístíte všechny prostředky pro aplikaci do skupiny, můžete je spravovat společně. Odstraněním příslušné skupiny prostředků by se například odstranily všechny prostředky, které jsou přidružené k dané aplikaci. Aktualizujte tuto hodnotu zadáním jedinečného názvu nové skupiny prostředků, třeba *TestResources*. Tento název skupiny prostředků použijete v pozdější fázi k vyčištění všech prostředků Azure. |
| `WEBAPP_NAME` | Část názvu hostitele pro aplikace nasazené do Azure (WEBAPP_NAME.azurewebsites.net) bude mít název aplikace. Aktualizujte tuto hodnotu jedinečný název pro novou aplikaci služby App Service, který bude hostovat aplikaci v Javě, například *contoso*. |
| `REGION` | Určitá oblast Azure, ve kterém je aplikace hostovaná, například `westus2`. Seznam oblastí můžete získat z Cloud Shellu nebo rozhraní příkazového řádku pomocí příkazu `az account list-locations`. |

## <a name="deploy-the-app"></a>Nasazení aplikace

Pomocí následujícího příkazu nasaďte svoji aplikaci v jazyce Java do Azure:

```bash
mvn package azure-webapp:deploy
```

Po dokončení nasazení přejděte ve webovém prohlížeči pomocí následující adresy URL, například `http://<webapp>.azurewebsites.net/`, do nasazené aplikace.

![Ukázková aplikace spuštěná ve službě Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

**Blahopřejeme!** Nasadili jste svou první aplikaci v Javě do služby App Service ve Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Azure pro prostředky vývojářům v Javě](/java/azure/)

> [!div class="nextstepaction"]
> [Mapování vlastní domény](app-service-web-tutorial-custom-domain.md)