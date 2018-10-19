---
title: Rychlý start pro vytvoření webové aplikace v Javě ve službě Azure App Service v Linuxu
description: V tomto rychlém startu během několika minut nasadíte svou první aplikaci Hello World v jazyce Java ve službě Azure App Service v Linuxu.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/07/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 92dde19e42c1adb6d83e1708106f844f228e8989
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239338"
---
# <a name="quickstart-create-a-java-web-app-in-app-service-on-linux"></a>Rychlý start: Vytvoření webové aplikace v Javě ve službě App Service v Linuxu

[App Service v Linuxu](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů s využitím operačního systému Linux. Tento rychlý start ukazuje, jak se dá [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) s [modulem plug-in Maven pro Azure Web Apps (Preview)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) použít k nasazení souboru webového archivu (WAR) webové aplikace v Javě.

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Vytvoření aplikace v Javě

Spusťte následující příkaz Maven v příkazovém řádku Cloud Shellu k vytvoření nové webové aplikace s názvem `helloworld`:

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
        <version>1.4.0</version>
        <configuration>
   
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
   
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>tomcat 8.5-jre8</linuxRuntime>
   
        </configuration>
    </plugin>
</plugins>
```    


> [!NOTE] 
> V tomto článku pracujeme jenom s aplikacemi Java zabalenými do souborů WAR. Modul plug-in podporuje také webové aplikace JAR. Pro tyto aplikace použijte následující alternativní definici modulu plug-in. Tato konfigurace nasadí JAR vytvořený pomocí Mavenu v `${project.build.directory}/${project.build.finalName}.jar` v místním systému souborů.
>
>```xml
><plugin>
>            <groupId>com.microsoft.azure</groupId>
>            <artifactId>azure-webapp-maven-plugin</artifactId>
>            <version>1.4.0</version>
>            <configuration>
>                <deploymentType>jar</deploymentType>
>
>           <!-- Web App information -->
>            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
>            <appName>${WEBAPP_NAME}</appName>
>            <region>${REGION}</region>  
>
>                <!-- Java Runtime Stack for Web App on Linux-->
>                <linuxRuntime>jre8</linuxRuntime>
>            </configuration>
>         </plugin>
>```    


V konfiguraci modulu plug-in aktualizujte následující zástupné symboly:

| Zástupný symbol | Popis |
| ----------- | ----------- |
| `RESOURCEGROUP_NAME` | Název nové skupiny prostředků, ve které chcete vytvořit webovou aplikaci. Když umístíte všechny prostředky pro aplikaci do skupiny, můžete je spravovat společně. Odstraněním příslušné skupiny prostředků by se například odstranily všechny prostředky, které jsou přidružené k dané aplikaci. Aktualizujte tuto hodnotu zadáním jedinečného názvu nové skupiny prostředků, třeba *TestResources*. Tento název skupiny prostředků použijete v pozdější fázi k vyčištění všech prostředků Azure. |
| `WEBAPP_NAME` | Název aplikace bude součástí názvu hostitele webové aplikace při nasazení do Azure (NAZEV_WEBOVE_APLIKACE.azurewebsites.net). Aktualizujte tuto hodnotu zadáním jedinečného názvu nové webové aplikace Azure, která bude hostitelem vaší aplikace v Javě, třeba *contoso*. |
| `REGION` | Oblast Azure, kde je webová aplikace hostovaná, například `westus2`. Seznam oblastí můžete získat z Cloud Shellu nebo rozhraní příkazového řádku pomocí příkazu `az account list-locations`. |

## <a name="deploy-the-app"></a>Nasazení aplikace

Pomocí následujícího příkazu nasaďte svoji aplikaci v jazyce Java do Azure:

```bash
mvn package azure-webapp:deploy
```

Po dokončení nasazení přejděte ve webovém prohlížeči pomocí následující adresy URL, například `http://<webapp>.azurewebsites.net/helloworld`, do nasazené aplikace. 

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-java/java-hello-world-in-browser-curl.png)

**Blahopřejeme!** Nasadili jste svou první aplikaci v Javě do služby App Service v Linuxu.


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili Maven k vytvoření webové aplikace v Javě, nakonfigurovali jste [modul plug-in Maven pro Azure Web Apps (Preview)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) a potom jste nasadili webovou aplikaci v Javě zabalenou do webového archivu do App Service v Linuxu. Další informace o používání Javy v Azure získáte po kliknutí na následující odkaz.

> [!div class="nextstepaction"]
> [Azure pro vývojáře v Javě](https://docs.microsoft.com/java/azure/)

