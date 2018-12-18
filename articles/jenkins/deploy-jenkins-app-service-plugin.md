---
title: Nasazení do Azure App Service s využitím modulu plug-in Jenkinse
description: Zjistěte, jak pomocí modulu plug-in Jenkinse pro Azure App Service nasadit webovou aplikaci v Javě do Azure.
ms.service: jenkins
keywords: jenkins, azure, devops, app service
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 98e69c7759f736c132601305156290f7a43eeaf9
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53537575"
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Nasazení do Azure App Service s využitím modulu plug-in Jenkinse 

K nasazení webové aplikace v Javě do Azure můžete použít Azure CLI v [kanálu Jenkinse](/azure/jenkins/execute-cli-jenkins-pipeline) nebo [modul plug-in Jenkinse pro Azure App Service](https://plugins.jenkins.io/azure-app-service). Modul plug-in Jenkinse verze 1.0 podporuje průběžné nasazování s využitím funkce Web Apps v Azure App Service přes:
* Nahrání souboru.
* Docker pro webové aplikace v Linuxu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Konfigurace Jenkinse pro nasazení webových aplikací nahráním souborů
> * Konfigurace Jenkinse pro nasazení do funkce Web App for Containers.

## <a name="create-and-configure-a-jenkins-instance"></a>Vytvoření a konfigurace instance Jenkinse

Pokud ještě nemáte hlavní server Jenkinse, začněte [šablonou řešení](install-jenkins-solution-template.md), která zahrnuje sadu JDK (Java Development Kit) verze 8 a následující požadované moduly plug-in Jenkinse:

* [Klientský modul plug-in Jenkins Git](https://plugins.jenkins.io/git-client) verze 2.4.6 
* [Modul plug-in Docker Commons](https://plugins.jenkins.io/docker-commons) verze 1.4.0
* [Azure Credentials](https://plugins.jenkins.io/azure-credentials) verze 1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-service) verze 0.1

Pomocí modulu plug-in Jenkinse můžete nasadit webovou aplikaci v jakémkoli jazyce, který podporuje funkce Web Apps, jako je například jazyk C#, PHP, Java a Node.js. V tomto kurzu použijeme [jednoduchou webovou aplikaci v Javě pro Azure](https://github.com/azure-devops/javawebappsample). Pokud chcete vytvořit fork úložiště do svého vlastního účtu GitHub, vyberte tlačítko **Fork** (Vytvořit fork) v pravém horním rohu rozhraní GitHubu.  

> [!NOTE]
> K sestavení projektu v Javě se vyžaduje sada Java JDK a Maven. Nainstalujte tyto komponenty na hlavní server Jenkinse nebo do agenta virtuálního počítače, pokud pro účely průběžné integrace využíváte agenta. Pokud nasazujete aplikaci Java SE, je na sestavovacím serveru potřebný také ZIP.
>

Pokud chcete tyto komponenty nainstalovat, přihlaste se přes SSH k instanci Jenkinse a spusťte následující příkazy:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Pokud chcete provést nasazení do funkce Web App for Containers, na hlavní server Jenkinse nebo do agenta virtuálního počítače, který se používá k sestavení, nainstalujte Docker. Pokyny najdete v tématu [Instalace Dockeru v Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

## <a name="service-principal"></a>Přidání instančního objektu Azure do přihlašovacích údajů Jenkinse

K nasazení do Azure budete potřebovat instanční objekt Azure. 


1. Chcete-li vytvořit instanční objekt služby Azure, použijte [rozhraní příkazového řádku Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) nebo [webu Azure portal](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. Na řídicím panelu Jenkinse vyberte **Přihlašovací údaje** > **Systém**. Pak vyberte **Global credentials (unrestricted)** (Globální přihlašovací údaje (neomezené)).
3. Pokud chcete přidat instanční objekt Microsoft Azure, vyberte **Add Credentials** (Přidat přihlašovací údaje). Zadejte odpovídající hodnoty do polí **Subscription ID** (ID předplatného), **Client ID** (ID klienta), **Client Secret** (Tajný klíč klienta) a **OAuth 2.0 Token Endpoint** (Koncový bod tokenu OAuth 2.0). V poli **ID** nastavte hodnotu **mySp**. Toto ID použijeme v dalších krocích v tomto článku.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Konfigurace Jenkinse pro nasazení do funkce Web Apps nahráním souborů

Pokud chcete projekt nasadit do Web Apps, můžete nahráním souborů nahrát artefakty buildu. Azure App Service podporuje několik možností nasazení. Modul plug-in Jenkins pro Azure App Service to zjednodušuje a odvozuje možnost nasazení na základě typu souboru. 

* Pro aplikace Java EE se používá [nasazení přes WAR](/azure/app-service/deploy-zip#deploy-war-file).
* Pro aplikace Java SE se používá [nasazení přes ZIP](/azure/app-service/deploy-zip#deploy-zip-file).
* Pro jiné jazyky se používá [nasazení přes Git](/azure/app-service/deploy-local-git).

Před nastavením úlohy v Jenkinsu potřebujete plán služby Azure App Service a webovou aplikaci, ve kterých se aplikace v Javě spustí.


1. Pomocí [příkazu Azure CLI](/cli/azure/appservice/plan#az-appservice-plan-create) `az appservice plan create` vytvořte plán služby App Service s cenovou úrovní **FREE**. Plán služby App Service definuje fyzické prostředky používané k hostování vašich aplikací. Tyto prostředky sdílí všechny aplikace přiřazené k plánu služby App Service. Sdílené prostředky pomáhají šetřit náklady při hostování více aplikací.
2. Vytvořte webovou aplikaci. Můžete použít [Azure Portal](/azure/app-service-web/web-sites-configure) nebo následující příkaz Azure CLI `az`:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Nastavte konfiguraci modulu runtime Java podle potřeb vaší aplikace. Následující příkaz Azure CLI nakonfiguruje spouštění webové aplikace na nejnovější sadě JDK 8 a [Apache Tomcat](http://tomcat.apache.org/) verze 8.0:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Nastavení úlohy Jenkinse

1. Na řídicím panelu Jenkinse vytvořte nový projekt **freestyle** (Volný projekt).
2. V poli **Source Code Management** (Správa zdrojového kódu) nakonfigurujte použití vašeho místního forku úložiště [jednoduché webové aplikace v Javě pro Azure](https://github.com/azure-devops/javawebappsample). Zadejte hodnotu **Repository URL** (Adresa URL úložiště). Příklad: http://github.com/&lt;vaše_ID>/javawebappsample.
3. Přidáním příkazu **Execute shell** (Spustit prostředí) přidejte krok nasazení projektu pomocí Mavenu. V tomto příkladu potřebujeme další příkaz k přejmenování souboru \*.war v cílové složce na **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Výběrem možnosti **Publish an Azure Web App** (Publikovat webovou aplikaci Azure) přidejte akci po sestavení.
5. Jako instanční objekt Azure zadejte **mySP**. Tento instanční objekt jste uložili jako [přihlašovací údaje Azure](#service-principal) v předchozím kroku.
6. V části **App Configuration** (Konfigurace aplikace) zvolte skupinu prostředků a webovou aplikaci ve vašem předplatném. Modul plug-in Jenkinse automaticky rozpozná, jestli je webová aplikace založená na Windows nebo Linuxu. Pro webovou aplikaci pro Windows se zobrazí možnost **Publish Files** (Publikovat soubory).
7. Vyplňte soubory, které chcete nasadit. Pokud například používáte Javu, zadejte balíček WAR. Pomocí volitelných parametrů **Zdrojový adresář** a **Cílový adresář** můžete určit, které zdrojové a cílové složky se k nasazení souborů použijí. Webová aplikace v Javě se v Azure spouští na serveru Tomcat. Proto pro Javu, nahrajete váš balíček WAR ke složce webové aplikace. V tomto příkladu nastavte hodnotu **Source Directory** (Zdrojový adresář) na **target** a hodnotu **Target Directory** (Cílový adresář) na **webapps**.
8. Pokud chcete provést nasazení do jiného než produkčního slotu, můžete také nastavit název **slotu**.
9. Uložte projekt a sestavte ho. Po dokončení sestavení se vaše webová aplikace se nasadí do Azure.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Nasazení do funkce Web Apps nahráním souborů s využitím kanálu Jenkinse

Modul plug-in Jenkinse pro Azure App Service je připravený k použití v kanálu. Další informace najdete v následující ukázce v úložišti GitHub.

1. V rozhraní GitHubu otevřete soubor **Jenkinsfile_ftp_plugin**. Pokud chcete soubor upravit, vyberte ikonu tužky. Na řádcích 11 a 12 aktualizujte definice **resourceGroup** a **webAppName** pro vaši webovou aplikaci:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Nastavte definici **withCredentials** na řádku 14 na ID přihlašovacích údajů ve vaší instanci Jenkinse:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Vytvoření kanálu Jenkinse

1. Otevřete Jenkinse ve webovém prohlížeči. Vyberte **New Item** (Nová položka).
2. Zadejte název úlohy a vyberte **Pipeline** (Kanál). Vyberte **OK**.
3. Vyberte kartu **Pipeline** (Kanál).
4. Jako hodnotu **Definition** (Definice) vyberte **Pipeline script from SCM** (Skript kanálu z SCM).
5. Jako hodnotu **SCM** vyberte **Git**. Zadejte adresu URL vašeho forku úložiště na GitHubu. Příklad: https://&lt;váš_fork_úložiště>.git.
6. Aktualizujte hodnotu **Script Path** (Cesta ke skriptu) na **Jenkinsfile_ftp_plugin**.
7. Vyberte **Uložit**  a spusťte úlohu.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Konfigurace Jenkinse pro nasazení do funkce Web App for Containers

Web Apps on Linux podporuje nasazení s využitím Dockeru. K nasazení vaší webové aplikace s využitím Dockeru, musíte zadat soubor Dockerfile, která zabalí vaší webové aplikace s modulem runtime služby do image Dockeru. Modul plug-in Jenkinse pak image sestaví, odešle do registru Dockeru a nasadí do vaší webové aplikace.

Web App on Linux podporuje také tradiční způsoby nasazení, jako je Git a nahrání souborů, ale pouze pro integrované jazyky (.NET Core, Node.js, PHP a Ruby). V případě ostatních jazyků je potřeba kód aplikace a modul runtime služby zabalit do image Dockeru a pomocí Dockeru ji nasadit.

Před nastavením úlohy v Jenkinsu budete potřebovat webovou aplikaci v Linuxu. Budete potřebovat také registr kontejneru, ve kterém se budou ukládat a spravovat vaše privátní image kontejneru Dockeru. K vytvoření registru kontejneru můžete použít DockerHub. V tomto příkladu použijeme službu Azure Container Registry.

* [Vytvořte svou webovou aplikaci v Linuxu](../app-service/containers/quickstart-nodejs.md).
* Azure Container Registry je spravovaná služba [registrů Dockeru](https://docs.docker.com/registry/) založená na open source nástroji Docker Registry verze 2.0. [Vytvořte registr kontejneru Azure](/azure/container-registry/container-registry-get-started-azure-cli). Můžete použít také DockerHub.

### <a name="set-up-the-jenkins-job-for-docker"></a>Nastavení úlohy Jenkinse pro Docker

1. Na řídicím panelu Jenkinse vytvořte nový projekt **freestyle** (Volný projekt).
2. V poli **Source Code Management** (Správa zdrojového kódu) nakonfigurujte použití vašeho místního forku úložiště [jednoduché webové aplikace v Javě pro Azure](https://github.com/azure-devops/javawebappsample). Zadejte hodnotu **Repository URL** (Adresa URL úložiště). Příklad: http://github.com/&lt;vaše_ID>/javawebappsample.
3. Přidáním příkazu **Execute shell** (Spustit prostředí) přidejte krok nasazení projektu pomocí Mavenu. Do příkazu přidejte následující řádek:
    ```bash
    mvn clean package
    ```

4. Výběrem možnosti **Publish an Azure Web App** (Publikovat webovou aplikaci Azure) přidejte akci po sestavení.
5. Jako instanční objekt Azure zadejte **mySP**. Tento instanční objekt jste uložili jako [přihlašovací údaje Azure](#service-principal) v předchozím kroku.
6. V části **App Configuration** (Konfigurace aplikace) zvolte skupinu prostředků a webovou aplikaci pro Linux ve vašem předplatném.
7. Zvolte **Publish via Docker**(Publikovat přes Docker).
8. Vyplňte hodnotu cesty k souboru **Dockerfile**. Můžete ponechat výchozí hodnotu /Dockerfile.
Jako hodnotu **Docker registry URL** (Adresa URL registru Dockeru) zadejte adresu URL ve formátu https://&lt;váš_registr>.azurecr.io, pokud používáte službu Azure Container Registry. Pokud používáte DockerHub, ponechte tuto hodnotu prázdnou.
9. Jako hodnotu **Registry credentials** (Přihlašovací údaje registru) přidejte přihlašovací údaje k registru kontejneru. ID uživatele a heslo můžete získat spuštěním následujících příkazů v Azure CLI. První příkaz povolí účet správce:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. Hodnoty názvu image a značky Dockeru na kartě **Advanced** (Upřesnit) jsou volitelné. Ve výchozím nastavení se hodnota pro název image získá z názvu image, který jste nakonfigurovali na webu Azure Portal v nastavení **Kontejner Dockeru**. Značka se generuje z $BUILD_NUMBER.
    > [!NOTE]
    > Nezapomeňte určit název image na webu Azure Portal nebo zadat hodnotu **Docker Image** (Image Dockeru) na kartě **Advanced** (Upřesnit). V tomto příkladu nastavte hodnotu **Docker image** (Image Dockeru) na &lt;váš_registr>.azurecr.io/calculator a hodnotu **Docker Image Tag** (Značka image Dockeru) ponechte prázdnou.

11. Pokud použijete předdefinované nastavení image Dockeru, nasazení selže. V nastavení **Kontejner Dockeru** na webu Azure Portal změňte konfiguraci Dockeru tak, aby používal vlastní image. Pokud používáte předdefinovanou image, použijte k nasazení nahrání souboru.
12. Podobně jako při nahrání souboru můžete zvolit jiný název **slotu** než **production** (Produkční).
13. Uložte projekt a sestavte ho. Vaše image kontejneru se odešle do vašeho registru a webová aplikace se nasadí.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Nasazení do funkce Web App for Containers s využitím kanálu Jenkinse

1. V rozhraní GitHubu otevřete soubor **Jenkinsfile_container_plugin**. Pokud chcete soubor upravit, vyberte ikonu tužky. Na řádcích 11 a 12 aktualizujte definice **resourceGroup** a **webAppName** pro vaši webovou aplikaci:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Na řádku 13 použijte váš server registru kontejneru:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Na řádku 16 použijte ID přihlašovacích údajů ve vaší instanci Jenkinse:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Vytvoření kanálu Jenkinse    

1. Otevřete Jenkinse ve webovém prohlížeči. Vyberte **New Item** (Nová položka).
2. Zadejte název úlohy a vyberte **Pipeline** (Kanál). Vyberte **OK**.
3. Vyberte kartu **Pipeline** (Kanál).
4. Jako hodnotu **Definition** (Definice) vyberte **Pipeline script from SCM** (Skript kanálu z SCM).
5. Jako hodnotu **SCM** vyberte **Git**. Zadejte adresu URL vašeho forku úložiště na GitHubu. Příklad: https://&lt;váš_fork_úložiště>.git.
7. Aktualizujte hodnotu **Script Path** (Cesta ke skriptu) na **Jenkinsfile_container_plugin**.
8. Vyberte **Uložit**  a spusťte úlohu.

## <a name="verify-your-web-app"></a>Ověření webové aplikace

1. Pokud chcete ověřit úspěšné nasazení souboru WAR do vaší webové aplikace, otevřete webový prohlížeč.
2. Přejděte na adresu http://&lt;název_vaší_aplikace>.azurewebsites.net/api/calculator/ping. Nahraďte &lt;název_vaší_aplikace> názvem vaší webové aplikace. Zobrazí se tato zpráva:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Přejděte na adresu http://&lt;název_vaší_aplikace>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Nahraďte &lt;x> and &lt;y> libovolnými čísly, aby se zobrazil součet x + y. Kalkulačce zobrazí součet: ![Kalkulačka: Přidat](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Azure App Service v Linuxu

1. Pokud chcete ověřit svou webovou aplikaci, spusťte v Azure CLI následující příkaz:
    ```CLI
    az acr repository list -n <myRegistry> -o json
    ```
    Zobrazí se následující zpráva:
    ```CLI
    ["calculator"]
    ```
    
2. Přejděte na adresu http://&lt;název_vaší_aplikace>.azurewebsites.net/api/calculator/ping. Nahraďte &lt;název_vaší_aplikace> názvem vaší webové aplikace. Zobrazí se tato zpráva: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Přejděte na adresu http://&lt;název_vaší_aplikace>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Nahraďte &lt;x> and &lt;y> libovolnými čísly, aby se zobrazil součet x + y.
    
## <a name="troubleshooting-the-jenkins-plugin"></a>Řešení potíží s modulem plug-in Jenkinse

Pokud v modulech plug-in Jenkinse narazíte na nějaké chyby, založte problém na stránce [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste s využitím modulu plug-in Jenkinse pro Azure App Service provedli nasazení do Azure.

Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Konfigurace Jenkinse pro nasazení Azure App Service nahráním souborů 
> * Konfigurace Jenkinse pro nasazení do funkce Web App for Containers 
