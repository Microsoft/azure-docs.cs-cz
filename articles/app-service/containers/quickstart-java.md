---
title: Vytvoření webové aplikace v Javě v Linuxu – Azure App Service
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
ms.date: 12/10/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: d27491d84d4df1757f77a403cd754496bbff6887
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53252605"
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
> V tomto článku pracujeme jenom s aplikacemi Java zabalenými do souborů WAR. Modul plug-in podporuje také webové aplikace JAR. Pokud si to chcete vyzkoušet, přečtěte si téma věnované [nasazení souboru Java SE JAR do služby App Service v Linuxu](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).


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


## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste použili Maven k vytvoření webové aplikace v Javě, nakonfigurovali jste [modul plug-in Maven pro Azure Web Apps](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) a potom jste nasadili aplikaci v Javě zabalenou do webového archivu do služby App Service v Linuxu. Odkazovat na následující kurzy a články s návody pro další informace o hostování aplikací v Javě ve službě App v Linuxu.

- [Kurz: Nasazení aplikace v Javě podnikové aplikace s PostgreSQL](tutorial-java-enterprise-postgresql-app.md)
- [Konfigurace zdroje dat Tomcat](app-service-linux-java.md#connecting-to-data-sources)
- [CI/CD pomocí Jenkinse](/azure/jenkins/deploy-jenkins-app-service-plugin)
- [Nastavení nástroje pro monitorování výkonu aplikací](how-to-java-apm-monitoring.md)

