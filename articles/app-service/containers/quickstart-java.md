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
ms.openlocfilehash: 2018f5b7051f2b6906372dad3319c763974b93b1
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34355181"
---
# <a name="quickstart-create-a-java-web-app-in-app-service-on-linux"></a>Rychlý start: Vytvoření webové aplikace v Javě ve službě App Service v Linuxu

App Service v Linuxu v současné době poskytuje funkci Preview pro podporu webových aplikací v Javě. Další informace o verzích Preview najdete v [dodatečných podmínkách používání verzí Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

[App Service v Linuxu](app-service-linux-intro.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů s využitím operačního systému Linux. Tento rychlý start ukazuje, jak se dá [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) s [modulem plug-in Maven pro Azure Web Apps (Preview)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) použít k nasazení webové aplikace v Javě za použití předdefinované image Linuxu.

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-java/java-hello-world-in-browser.png)

Alternativním způsobem nasazení aplikace v Javě do vlastního kontejneru je [nasazování webových aplikací v Javě do kontejneru Linuxu v cloudu pomocí sady Azure Toolkit pro IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-hello-world-web-app-linux).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba: 

* [Rozhraní Azure CLI 2.0 nebo novější](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nainstalované v místním prostředí
* [Apache Maven](http://maven.apache.org/)



## <a name="create-a-java-app"></a>Vytvoření aplikace v Javě

Pomocí nástroje Maven spusťte následující příkaz, který slouží k vytvoření nové webové aplikace *helloworld*:  

    mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp

Přejděte do nového adresáře projektu *helloworld* a pomocí následujícího příkazu sestavte všechny moduly:

    mvn verify

Tento příkaz ověří a vytvoří všechny moduly včetně souboru *helloworld.war* v podadresáři *helloworld/target*.


## <a name="deploying-the-java-app-to-app-service-on-linux"></a>Nasazení aplikace v Javě do služby App Service v Linuxu

Existuje několik možností, jak v Linuxu nasazovat webové aplikace v Javě do služby App Service. K těmto možnostem patří:

* [Nasazení pomocí modulu plug-in Maven pro Azure Web Apps](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)
* [Nasazení přes ZIP nebo WAR](https://docs.microsoft.com/azure/app-service/app-service-deploy-zip)
* [Nasazení přes FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)

V tomto rychlém startu použijete modul plug-in Maven pro Azure Web Apps. Jeho výhoda spočívá v tom, že se jedná o jednoduchý nástroj Maven, který pro vás vytvoří potřebné prostředky Azure (skupinu prostředků, plán služby App Service a webovou aplikaci).

### <a name="deploy-with-maven"></a>Nasazení pomocí nástroje Maven

K nasazení z nástroje Maven přidejte do elementu `<build>` souboru *pom.xml* následující definici modulu plug-in:

```xml
    <plugins>
      <plugin>
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-webapp-maven-plugin</artifactId> 
        <version>1.1.0</version>
        <configuration> 
          <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup> 
          <appName>YOUR_WEB_APP</appName> 
          <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>
          <deploymentType>ftp</deploymentType> 
          <resources> 
              <resource> 
                  <directory>${project.basedir}/target</directory> 
                  <targetPath>webapps</targetPath> 
                  <includes> 
                      <include>*.war</include> 
                  </includes> 
                  <excludes> 
                      <exclude>*.xml</exclude> 
                  </excludes> 
              </resource> 
          </resources> 
        </configuration>
      </plugin>
    </plugins>
```    

V konfiguraci modulu plug-in aktualizujte následující zástupné symboly:

| Zástupný symbol | Popis |
| ----------- | ----------- |
| `YOUR_RESOURCE_GROUP` | Název nové skupiny prostředků, ve které chcete vytvořit webovou aplikaci. Když umístíte všechny prostředky pro aplikaci do skupiny, můžete je spravovat společně. Odstraněním příslušné skupiny prostředků by se například odstranily všechny prostředky, které jsou přidružené k dané aplikaci. Aktualizujte tuto hodnotu zadáním jedinečného názvu nové skupiny prostředků, třeba *TestResources*. Tento název skupiny prostředků použijete v pozdější fázi k vyčištění všech prostředků Azure. |
| `YOUR_WEB_APP` | Název aplikace bude součástí názvu hostitele webové aplikace při nasazení do Azure (YOUR_WEB_APP.azurewebsites.net). Aktualizujte tuto hodnotu zadáním jedinečného názvu nové webové aplikace Azure, která bude hostitelem vaší aplikace v Javě, třeba *contoso*. |

Element `linuxRuntime` konfigurace určuje, která integrovaná image Linuxu se má ve vaší aplikaci použít.

Spusťte v rozhraní příkazového řádku Azure následující příkaz a postupujte podle pokynů k ověření:

    az login

Pomocí následujícího příkazu nasaďte do webové aplikace svoji aplikaci v jazyce Java:

    mvn clean package azure-webapp:deploy


Po dokončení nasazení přejděte ve webovém prohlížeči pomocí následující adresy URL do nasazené aplikace.

```bash
http://<app_name>.azurewebsites.net/helloworld
```

Vzorový kód v Javě je spuštěný ve webové aplikaci s použitím integrované image.

![Ukázková aplikace spuštěná ve službě Azure](media/quickstart-java/java-hello-world-in-browser-curl.png)

**Blahopřejeme!** Nasadili jste svou první aplikaci v Javě do služby App Service v Linuxu.


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili webovou aplikaci v Javě pomocí nástroje Maven a potom jste tuto webovou aplikaci v Javě nasadili do služby App Service v Linuxu. Další informace o používání Javy v Azure získáte po kliknutí na následující odkaz.

> [!div class="nextstepaction"]
> [Azure pro vývojáře v Javě](https://docs.microsoft.com/java/azure/)

