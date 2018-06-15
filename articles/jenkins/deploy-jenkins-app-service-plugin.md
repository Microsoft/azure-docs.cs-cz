---
title: Nasazení do Azure App Service pomocí modulu plug-in volaných | Microsoft Docs
description: Další informace o použití modulu plug-in Azure App Service volaných nasazení webové aplikace v jazyce Java do Azure ve volaných
services: app-service\web
documentationcenter: ''
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 7/24/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 0128ad37e3ba66710279de42cf4eae0ce5431b5b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31418412"
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Nasazení do Azure App Service pomocí modulu plug-in volaných 

Chcete-li nasadit webovou aplikaci Java do Azure, můžete použít rozhraní příkazového řádku Azure v [volaných kanálu](/azure/jenkins/execute-cli-jenkins-pipeline) nebo můžete použít [modul plug-in Azure App Service volaných](https://plugins.jenkins.io/azure-app-service). Modul plug-in verze 1.0 volaných podporuje průběžné nasazování pomocí funkce Web Apps služby Azure App Service pomocí:
* Git a FTP.
* Docker pro webové aplikace v systému Linux.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nakonfigurujte volaných jak nasadit webové aplikace prostřednictvím Git a FTP.
> * Nakonfigurujte volaných nasadit webovou aplikaci pro kontejnery.

## <a name="create-and-configure-a-jenkins-instance"></a>Vytvořit a nakonfigurovat instanci volaných

Pokud ještě nemáte volaných Master, začínat [šablona řešení](install-jenkins-solution-template.md), což zahrnuje Java Development Kit (JDK) verze 8 a modulů plug-in volaných vyžaduje následující:

* [Modul plug-in klienta volaných Git](https://plugins.jenkins.io/git-client) verze 2.4.6 
* [Modul plug-in docker Commons](https://plugins.jenkins.io/docker-commons) verze 1.4.0
* [Přihlašovací údaje Azure](https://plugins.jenkins.io/azure-credentials) verze 1.2
* [Aplikační služba Azure](https://plugins.jenkins.io/azure-app-service) verze 0,1

Modul plug-in volaných můžete použít k nasazení webové aplikace ve všech jazycích, které podporuje webové aplikace, například C#, PHP, Java a Node.js. V tomto kurzu používáme [jednoduché webové aplikace Java pro Azure](https://github.com/azure-devops/javawebappsample). Chcete-li rozvětvit úložiště k účtu GitHub, vyberte **rozvětvení** tlačítko v pravém horním rohu rozhraní Githubu.  
> [!NOTE]
> Vyžaduje Java JDK a Maven k sestavení projektu Java. Tyto součásti nainstalujte, nebo na hlavním volaných na agenta virtuálního počítače používáte-li pro nepřetržitou integraci agenta. 

Chcete-li nainstalovat komponenty, přihlaste se k instanci volaných pomocí protokolu SSH a spusťte následující příkazy:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Pokud chcete nasadit do webové aplikace pro kontejnery, nainstalujte Docker na hlavním volaných nebo na agenta virtuálního počítače, který se používá pro sestavení. Pokyny najdete v tématu [Docker nainstalovat na Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

##<a name="service-principal"></a> Přidat objektu zabezpečení služby Azure na volaných pověření

Budete potřebovat objektu zabezpečení služby Azure k nasazení do Azure. 


1. Chcete-li vytvořit objekt služby Azure, použijte [rozhraní příkazového řádku Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) nebo [portál Azure](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. Na řídicím panelu volaných vyberte **pověření** > **systému**. Pak vyberte **globální credentials(unrestricted)**.
3. Chcete-li přidat hlavní název služby Microsoft Azure, vyberte **přidat přihlašovací údaje**. Zadejte hodnoty pro **ID předplatného**, **ID klienta**, **tajný klíč klienta**, a **koncový bod tokenu OAuth 2.0** pole. Nastavte **ID** do **mySp**. Toto ID použít v následných kroků v tomto článku.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Konfigurace volaných nasazování webových aplikací s nahrávání souborů

K nasazení projektu do webové aplikace, můžete nahrát artefaktů sestavení (například soubor WAR v jazyce Java) pomocí Git a FTP.

Před nastavením pro úlohu ve volaných potřebujete plán aplikační služby Azure a webové aplikace a spusťte aplikaci Java.


1. Vytvořit plán aplikační služby Azure s **volné** cenová úroveň pomocí `az appservice plan create` [příkazu příkazového řádku Azure CLI](/cli/azure/appservice/plan#az_appservice_plan_create). Plán služby App Service definuje fyzické prostředky, které budou použity k hostování vaší aplikace. Všechny aplikace, které jsou přiřazeny k plán služby App Service sdílení těchto prostředků. Sdílené prostředky můžete uložit na nákladů při hostování více aplikací.
2. Vytvořte webovou aplikaci. Můžete použít [portál Azure](/azure/app-service-web/web-sites-configure) nebo následující `az` příkazu příkazového řádku Azure CLI:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Nastavte konfiguraci Java runtime, která vaše aplikace potřebuje. Následující příkaz rozhraní příkazového řádku Azure nakonfiguruje webové aplikace pro spouštění v posledních 8 JDK a [Apache Tomcat](http://tomcat.apache.org/) verze 8.0:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Nastavení úloh volaných

1. Vytvořte novou **volný styl** projektu na řídicím panelu volaných.
2. Konfigurace **správu zdrojového kódu** pole, které chcete používat vaše místní pokračovatelem [jednoduché webové aplikace Java pro Azure](https://github.com/azure-devops/javawebappsample). Zadejte **adresu URL úložiště** hodnotu. Příklad: http://github.com/ &lt;your_ID > / javawebappsample.
3. Přidejte krok pro sestavení projektu pomocí Maven přidáním **spustit prostředí** příkaz. V tomto příkladu budeme potřebovat další příkaz Přejmenovat \*.war souborů v cílové složce do **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Přidat akci po sestavení tak, že vyberete **publikování webové aplikace Azure**.
5. Zadejte **mySp** jako objekt zabezpečení služby Azure. Tento objekt zabezpečení byl uložený jako [přihlašovací údaje Azure](#service-principal) v předchozím kroku.
6. V **konfigurace aplikací** zvolte prostředků skupiny a webové aplikace v rámci vašeho předplatného. Modul plug-in volaných automaticky zjistí, zda webová aplikace je založena na systému Windows nebo Linux. Pro webovou aplikaci systému Windows **publikovat soubory** možnost se zobrazí.
7. Zadejte soubory, které chcete nasadit. Můžete například zadejte balíček WAR, pokud používáte Java. Použít nepovinný **zdrojový adresář** a **cílový adresář** parametry k určení zdrojové a cílové složky, které chcete použít pro nahrávání souborů. Webové aplikace Java v Azure běží na serveru Tomcat. Proto pro jazyk Java, nahrajete vašeho balíčku WAR do je umístěna složka. V tomto příkladu nastavit **zdrojový adresář** hodnotu **cíl** a **cílový adresář** hodnotu **webapps**.
8. Pokud chcete nasadit do přihrádky než produkční, můžete také nastavit **slotu** název.
9. Uložte projekt a sestavte jej. Webové aplikace se nasadí do Azure, po dokončení sestavení.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Nahrávání souborů pomocí kanálu volaných nasadit webové aplikace

Modul plug-in Azure App Service volaných je připravené pro kanál. Najdete v následujícím příkladu v úložišti GitHub.

1. V rozhraní Githubu, otevřete **Jenkinsfile_ftp_plugin** souboru. Pokud chcete soubor upravit, vyberte ikonu tužky. Aktualizace **resourceGroup** a **webAppName** definice pro vaši webovou aplikaci na řádků 11 a 12, v uvedeném pořadí:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Nastavte **withCredentials** definici v řádku 14 ID přihlašovacích údajů v instanci volaných:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Vytvoření kanálu volaných

1. Volaných otevřete ve webovém prohlížeči. Vyberte **novou položku**.
2. Zadejte název pro úlohy, vyberte **kanálu**. Vyberte **OK**.
3. Vyberte **kanálu** kartě.
4. Pro **definice** hodnotu, vyberte **kanálu skript z SCM**.
5. Pro **SCM** hodnotu, vyberte **Git**. Zadejte adresu URL webu GitHub pro vaše forked úložišti. Příklad: https://&lt;your_forked_repo > .git.
6. Aktualizace **cestu ke skriptu** hodnotu **Jenkinsfile_ftp_plugin**.
7. Vyberte **Uložit** a spusťte úlohu.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Konfigurace volaných nasadit webovou aplikaci pro kontejnery

Webové aplikace v systému Linux podporuje nasazení pomocí Docker. Nasazení webové aplikace pomocí Docker, budete muset zadat soubor Docker, který balíčky vaší webové aplikace s běh služby do bitové kopie Docker. Modul plug-in volaných potom vytvoří bitovou kopii, doručí do registru Docker a nasadí bitovou kopii do vaší webové aplikace.

Webové aplikace v systému Linux také podporuje tradiční nasazení metody, jako je Git a FTP, ale jenom pro předdefinované jazyků (.NET Core, Node.js, PHP a Ruby). Pro jiné jazyky potřebujete společně balíček runtime kód a služby vaší aplikace do bitové kopie Docker a nasadit pomocí Docker.

Před nastavením pro úlohu ve volaných, je třeba webové aplikace v systému Linux. Budete také potřebovat kontejneru registru k ukládání a správě privátního kontejner imagí Dockeru. DockerHub slouží k vytvoření kontejneru registru. V tomto příkladu používáme registru kontejner Azure.

* [Vytvoření webové aplikace v systému Linux](../app-service/containers/quickstart-nodejs.md).
* Azure registru kontejneru je spravované [Docker registru](https://docs.docker.com/registry/) služba, která je založena na open source Docker registru verze 2.0. [Vytvoření služby Azure kontejneru registru](/azure/container-registry/container-registry-get-started-azure-cli). Můžete také použít DockerHub.

### <a name="set-up-the-jenkins-job-for-docker"></a>Nastavení úloh volaných pro Docker

1. Vytvořte novou **volný styl** projektu na řídicím panelu volaných.
2. Konfigurace **správu zdrojového kódu** pole, které chcete používat vaše místní pokračovatelem [jednoduché webové aplikace Java pro Azure](https://github.com/azure-devops/javawebappsample). Zadejte **adresu URL úložiště** hodnotu. Příklad: http://github.com/ &lt;your_ID > / javawebappsample.
3. Přidejte krok pro sestavení projektu pomocí Maven přidáním **spustit prostředí** příkaz. Následující řádek do příkazu zahrnout:
    ```bash
    mvn clean package
    ```

4. Přidat akci po sestavení tak, že vyberete **publikování webové aplikace Azure**.
5. Zadejte **mySp** jako objekt zabezpečení služby Azure. Tento objekt zabezpečení byl uložený jako [přihlašovací údaje Azure](#service-principal) v předchozím kroku.
6. V **konfigurace aplikací** zvolte skupinu prostředků a Linux webové aplikace v rámci vašeho předplatného.
7. Zvolte **publikování přes Docker**.
8. Vyplňte **soubor Docker** hodnota cesty. Můžete ponechat /Dockerfile výchozí hodnotu.
Pro **Docker registru URL** hodnotu, zadejte adresu URL pomocí formátu https://&lt;yourRegistry >. azurecr.io, pokud používáte Azure kontejneru registru. Pokud používáte DockerHub, ponechejte tuto hodnotu prázdnou.
9. Pro **registru pověření** hodnotu, přidat přihlašovací údaje pro kontejner registru. ID uživatele a heslo můžete získat spuštěním následujících příkazů v Azure CLI. První příkaz umožňuje účet správce:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. Docker název a značky hodnotu obrázku v **Upřesnit** kartě jsou volitelné. Výchozí hodnota pro název bitové kopie se získávají z název bitové kopie, který jste nakonfigurovali v portálu Azure v **kontejner Docker** nastavení. Značky se generují z $BUILD_NUMBER.
    > [!NOTE]
    > Nezapomeňte zadat název bitové kopie na portálu Azure nebo zadat **Docker Image** hodnotu **Upřesnit** kartě. V tomto příkladu nastavit **Docker image** hodnotu &lt;your_Registry >.azurecr.io/calculator a nechte **značka obrázku Docker** prázdnou hodnotu.

11. Nasazení selže, pokud použijete předdefinované nastavení Docker bitové kopie. Změnit konfiguraci Docker používat vlastní image ve **kontejner Docker** nastavení na portálu Azure. Předdefinované bitové kopie použijte postup nahrávání souboru k nasazení.
12. Podobně jako u Postup nahrání souboru, můžete zvolit jiné **slotu** název jiné než **produkční**.
13. Uložte a sestavte projekt. Bitové kopie kontejneru vložena do registru a webové aplikace je nasazená.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Nasazení webové aplikace pro kontejnery pomocí volaných kanálu

1. V rozhraní Githubu, otevřete **Jenkinsfile_container_plugin** souboru. Pokud chcete soubor upravit, vyberte ikonu tužky. Aktualizace **resourceGroup** a **webAppName** definice pro vaši webovou aplikaci na řádků 11 a 12, v uvedeném pořadí:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Změňte řádek 13 k registru serveru kontejneru:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Změňte řádek 16 používat ID přihlašovacích údajů v instanci volaných:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Vytvoření kanálu volaných    

1. Volaných otevřete ve webovém prohlížeči. Vyberte **novou položku**.
2. Zadejte název pro úlohy, vyberte **kanálu**. Vyberte **OK**.
3. Vyberte **kanálu** kartě.
4. Pro **definice** hodnotu, vyberte **kanálu skript z SCM**.
5. Pro **SCM** hodnotu, vyberte **Git**. Zadejte adresu URL webu GitHub pro vaše forked úložišti. Příklad: https://&lt;your_forked_repo > .git.
7. Aktualizace **cestu ke skriptu** hodnotu **Jenkinsfile_container_plugin**.
8. Vyberte **Uložit** a spusťte úlohu.

## <a name="verify-your-web-app"></a>Ověření webové aplikace

1. Pokud chcete ověřit, že je soubor WAR úspěšně nasazena do vaší webové aplikace, otevřete webový prohlížeč.
2. Přejděte na http://&lt;your_app_name >.azurewebsites.net/api/calculator/ping. Nahraďte &lt;your_app_name > s názvem vaší webové aplikace. Zobrazí se zpráva:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Přejděte na http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Nahraďte &lt;x > a &lt;y > s všechna čísla získat součet hodnot x a y. Rozhraní kalkulačky zobrazí součet: ![kalkulačky: Přidat](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Pro službu Azure App Service v systému Linux

1. Pokud chcete ověřit vaší webové aplikace, spusťte následující příkaz v rozhraní příkazového řádku Azure:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    Zobrazí se následující zpráva:
    ```CLI
    ["calculator"]
    ```
    
2. Přejděte na http://&lt;your_app_name >.azurewebsites.net/api/calculator/ping. Nahraďte &lt;your_app_name > s názvem vaší webové aplikace. Zobrazí se zpráva: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Přejděte na http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Nahraďte &lt;x > a &lt;y > s všechna čísla získat součet hodnot x a y.
    
## <a name="next-steps"></a>Další postup

V tomto kurzu použít modul plug-in Azure App Service volaných k nasazení do Azure.

Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Konfigurace volaných k nasazení služby Azure App Service pomocí služby FTP 
> * Konfigurace volaných k nasazení do webové aplikace pro kontejnery 
