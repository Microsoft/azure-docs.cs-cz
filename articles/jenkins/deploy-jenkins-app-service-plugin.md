---
title: Nasazení do služby Azure App Service pomocí modulu plug-in Jenkinse
description: Další informace o použití modulu plug-in Jenkinse pro Azure App Service k nasazení webové aplikace v Javě do Azure v Jenkinsu
ms.topic: article
ms.author: tarcher
author: tomarcher
manager: jpconnock
ms.service: devops
ms.custom: jenkins
ms.date: 07/25/2018
ms.openlocfilehash: 407ec2bbb145e73b1a903886204b660aadc9a65f
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284410"
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Nasazení do služby Azure App Service pomocí modulu plug-in Jenkinse 

Pokud chcete nasadit webové aplikace v Javě do Azure, můžete použít rozhraní příkazového řádku Azure v [kanálu Jenkins](/azure/jenkins/execute-cli-jenkins-pipeline) nebo můžete použít [modulu plug-in Jenkinse pro Azure App Service](https://plugins.jenkins.io/azure-app-service). Jenkins modul plug-in verze 1.0 podporuje průběžné nasazování pomocí funkce Web Apps služby Azure App Service prostřednictvím:
* Git a FTP.
* Docker for Web Apps v Linuxu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Konfigurace Jenkinse k nasazování webových aplikací pomocí Gitu nebo FTP.
> * Konfigurace Jenkinse k nasazení aplikace Web App for Containers.

## <a name="create-and-configure-a-jenkins-instance"></a>Vytvoření a konfigurace instance Jenkinse

Pokud ještě nemáte hlavní server Jenkinse, začněte tématem [šablonu řešení](install-jenkins-solution-template.md), což zahrnuje Java Development Kit (JDK) 8 verzí a požadované moduly plug-in Jenkins:

* [Modul plug-in Jenkinse Git klienta](https://plugins.jenkins.io/git-client) verze 2.4.6 
* [Modul plug-in docker Commons](https://plugins.jenkins.io/docker-commons) verze 1.4.0
* [Přihlašovací údaje Azure](https://plugins.jenkins.io/azure-credentials) verze 1.2
* [Azure App Service](https://plugins.jenkins.io/azure-app-service) verze 0.1

Modul plug-in Jenkinse můžete použít k nasazení webové aplikace v jakémkoli jazyce, který podporuje webové aplikace, například C#, PHP, Java nebo Node.js. V tomto kurzu používáme [jednoduché webové aplikace v Javě pro Azure](https://github.com/azure-devops/javawebappsample). Chcete-li vytvořit fork úložiště do vlastního účtu Githubu, vyberte **Forku** tlačítko v pravém horním rohu rozhraní Githubu.  
> [!NOTE]
> Java JDK a Maven jsou nutné k vytvoření projektu jazyka Java. Tyto součásti nainstalujte, nebo na hlavního serveru Jenkinse na agenta virtuálního počítače používáte agenta pro průběžnou integraci. 

Pokud chcete nainstalovat součásti, připojte se k instance Jenkinse pomocí protokolu SSH a spusťte následující příkazy:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Nasazení do Web App for Containers, nainstalujte Docker na hlavního serveru Jenkinse nebo agenta virtuálního počítače, který se používá pro sestavení. Pokyny najdete v tématu [nainstalovat Docker v Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

##<a name="service-principal"></a> Přidání instančního objektu Azure k přihlašovacím údajům Jenkinse

Potřebujete instanční objekt Azure k nasazení do Azure. 


1. Chcete-li vytvořit instanční objekt služby Azure, použijte [rozhraní příkazového řádku Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) nebo [webu Azure portal](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. Na řídicím panelu Jenkinse vyberte **pověření** > **systému**. Vyberte **globální credentials(unrestricted)**.
3. Chcete-li přidat hlavní název služby Microsoft Azure, **přidat přihlašovací údaje**. Zadejte hodnoty pro **ID předplatného**, **ID klienta**, **tajný kód klienta**, a **koncový bod tokenu OAuth 2.0** pole. Nastavte **ID** pole **mySp**. Toto ID použít v dalších krocích v tomto článku.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Konfigurace Jenkinse k nasazování webových aplikací tím, že nahraje soubory

Nasazení projektu do webové aplikace, můžete nahrát artefakty sestavení (například soubor WAR v Javě) pomocí Gitu nebo FTP.

Před nastavením úlohy v Jenkinsovi, potřebujete plán služby Azure App Service a webovou aplikaci spusťte aplikaci v Javě.


1. Vytvoření plánu služby Azure App Service s **FREE** cenovou úroveň s využitím `az appservice plan create` [rozhraní příkazového řádku Azure](/cli/azure/appservice/plan#az_appservice_plan_create). Plán služby App Service definuje fyzické prostředky, které se používají k hostování vašich aplikací. Všechny aplikace, které jsou přiřazené k plánu služby App Service sdílí tyto prostředky. Sdílené prostředky můžete uložit na nákladů při hostování více aplikací.
2. Vytvořte webovou aplikaci. Můžete použít [webu Azure portal](/azure/app-service-web/web-sites-configure) nebo následující `az` rozhraní příkazového řádku Azure:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Nastavte konfiguraci modulu runtime Java podle potřeb vaší aplikace. Následující příkaz rozhraní příkazového řádku Azure nakonfiguruje ve webové aplikaci spouštět na posledních 8 JDK a [Apache Tomcat](http://tomcat.apache.org/) verze 8.0:
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Nastavení úlohy Jenkinse

1. Vytvořte nový **volný styl** projektu na řídicím panelu Jenkinse.
2. Konfigurace **Správa zdrojového kódu** pole, které chcete použít místní rozvětvení [jednoduché webové aplikace v Javě pro Azure](https://github.com/azure-devops/javawebappsample). Zadejte **adresa URL úložiště** hodnotu. Příklad: http://github.com/&lt; your_ID > / javawebappsample.
3. Přidejte krok sestavení projektu pomocí nástroje Maven tak, že přidáte **spustit prostředí** příkazu. V tomto příkladu budeme potřebovat další příkaz a přejmenujte \*soubor .war v cílové složce pro **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Přidání akce po sestavení tak, že vyberete **publikovat webovou aplikaci Azure**.
5. Zadat **mySp** jako objekt zabezpečení služby Azure. Tento objekt zabezpečení byl uložen jako [přihlašovací údaje Azure](#service-principal) v předchozím kroku.
6. V **konfigurace aplikace** zvolte prostředek skupiny a webovou aplikaci ve vašem předplatném. Modul plug-in Jenkinse automaticky zjišťuje, zda webová aplikace je založena na Windows nebo Linux. Pro webovou aplikaci Windows **publikovat soubory** možnost se zobrazí.
7. Zadejte soubory, které chcete nasadit. Například určete balíček WAR, pokud používáte Javu. Použít nepovinný **zdrojový adresář** a **cílový adresář** parametrů určíte zdrojovou a cílovou složku pro nahrání souboru. Webová aplikace Java v Azure běží na serveru Tomcat. Proto pro Javu, nahrajete váš balíček WAR ke složce webové aplikace. V tomto příkladu nastavte **zdrojový adresář** hodnota, která se **cílové** a **cílový adresář** hodnota, která se **webapps**.
8. Pokud chcete nasadit jiné než produkční slot, můžete také nastavit **slotu** název.
9. Uložení projektu a sestavte ho. Webové aplikace se nasadí do Azure, po dokončení sestavení.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Nasazení webové aplikace díky nahrávání souborů s využitím kanálu Jenkins

Modul plug-in Jenkinse pro Azure App Service je připravený pro kanál. Najdete v následujícím příkladu v úložišti na Githubu.

1. V rozhraní Githubu otevřete **Jenkinsfile_ftp_plugin** souboru. Chcete-li upravit soubor, vyberte ikonu tužky. Aktualizace **resourceGroup** a **webAppName** definice pro vaši webovou aplikaci na řádky 11 a 12, v uvedeném pořadí:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Nastavte **withCredentials** definice na řádku 14 pro ID přihlašovacích údajů do instance Jenkinse:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Vytvoření kanálu Jenkins

1. Otevřete Jenkinse ve webovém prohlížeči. Vyberte **New Item** (Nová položka).
2. Zadejte název projektu a vyberte **kanálu**. Vyberte **OK**.
3. Vyberte **kanálu** kartu.
4. Pro **definice** hodnotu, vyberte **kanálu skriptu ze Správce řízení služeb**.
5. Pro **SCM** hodnotu, vyberte **Git**. Zadejte adresu URL GitHub vašeho rozvětveného úložiště. Příklad: https://&lt;your_forked_repo > .git.
6. Aktualizace **cesta ke skriptu** hodnota, která se **Jenkinsfile_ftp_plugin**.
7. Vyberte **Uložit** a spusťte úlohu.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Konfigurace Jenkinse k nasazení aplikace Web App for Containers

Web Apps v Linuxu podporuje nasazení s využitím Dockeru. K nasazení vaší webové aplikace s využitím Dockeru, musíte zadat soubor Dockerfile, která zabalí vaší webové aplikace s modulem runtime služby do image Dockeru. Modul plug-in Jenkinse potom sestaví image, nasdílí do registru Dockeru a nasadí image do webové aplikace.

Web Apps v Linuxu podporuje také metody tradiční nasazení, jako je Git a FTP, ale pouze pro integrované jazyků (.NET Core, Node.js, PHP a Ruby). Pro ostatní jazyky se budete muset balíček spuštění kódu a služby vaší aplikace společně do image Dockeru a nasadit pomocí Dockeru.

Před nastavením úlohy v Jenkinsovi, musíte webové aplikace v Linuxu. Budete také potřebovat registr kontejnerů k ukládání a správě privátních imagí kontejnerů Dockeru. Dockerhubu slouží k vytvoření registru kontejneru. V tomto příkladu používáme Azure Container Registry.

* [Vytvoření webové aplikace v Linuxu](../app-service/containers/quickstart-nodejs.md).
* Služba Azure Container Registry je spravovaná [registru Dockeru](https://docs.docker.com/registry/) služba, která je založená na open source registru Dockeru verze 2.0. [Vytvoření služby Azure container registry](/azure/container-registry/container-registry-get-started-azure-cli). Můžete také použít Dockerhubu.

### <a name="set-up-the-jenkins-job-for-docker"></a>Nastavení úlohy Jenkinse pro Docker

1. Vytvořte nový **volný styl** projektu na řídicím panelu Jenkinse.
2. Konfigurace **Správa zdrojového kódu** pole, které chcete použít místní rozvětvení [jednoduché webové aplikace v Javě pro Azure](https://github.com/azure-devops/javawebappsample). Zadejte **adresa URL úložiště** hodnotu. Příklad: http://github.com/&lt; your_ID > / javawebappsample.
3. Přidejte krok sestavení projektu pomocí nástroje Maven tak, že přidáte **spustit prostředí** příkazu. V příkazu, přidejte následující řádek:
    ```bash
    mvn clean package
    ```

4. Přidání akce po sestavení tak, že vyberete **publikovat webovou aplikaci Azure**.
5. Zadat **mySp** jako objekt zabezpečení služby Azure. Tento objekt zabezpečení byl uložen jako [přihlašovací údaje Azure](#service-principal) v předchozím kroku.
6. V **konfigurace aplikace** zvolte skupinu prostředků a Linuxovou webovou aplikaci ve vašem předplatném.
7. Zvolte **publikování přes Docker**.
8. Vyplňte **soubor Dockerfile** hodnota cesty. Abyste mohli /Dockerfile výchozí hodnotu.
Pro **adresa URL registru Dockeru** hodnoty, zadejte adresu URL pomocí formátu https://&lt;yourRegistry >. azurecr.io použijete Azure Container Registry. Pokud používáte Dockerhubu, ponechte hodnotu prázdný.
9. Pro **přihlašovacích údajů registru** hodnotu, přidejte přihlašovací údaje pro registr kontejneru. Spuštěním následujících příkazů v rozhraní příkazového řádku Azure můžete získat ID uživatele a heslo. První příkaz povolí správce účtu:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. Docker název a značku hodnotu obrázku v **Upřesnit** kartě jsou volitelné. Ve výchozím nastavení, je hodnota pro název image získané název image, kterou jste nakonfigurovali na webu Azure Portal v **kontejneru Dockeru** nastavení. Značka se generuje z $BUILD_NUMBER.
    > [!NOTE]
    > Nezapomeňte zadat název image na webu Azure Portal nebo zadat **Image Dockeru** hodnotu **Upřesnit** kartu. V tomto příkladu nastavte **image Dockeru** hodnota, která se &lt;your_Registry >.azurecr.io/calculator a nechte **značce Image Dockeru** prázdnou hodnotu.

11. Nasazení se nezdaří, pokud použijete předdefinované nastavení image Dockeru. Změnit konfiguraci Dockeru na použití vlastní image v **kontejneru Dockeru** nastavení na portálu Azure portal. Pro integrované image použijte k nasazení přístupu nahrávání souboru.
12. Podobný postup nahrání souboru můžete použít jiný **slotu** název jiného než **produkční**.
13. Uložit a sestavte projekt. Svou image kontejneru je nahrány do vašeho registru a webová aplikace nasazená.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Nasazení aplikace Web App for Containers s využitím kanálu Jenkins

1. V rozhraní Githubu otevřete **Jenkinsfile_container_plugin** souboru. Chcete-li upravit soubor, vyberte ikonu tužky. Aktualizace **resourceGroup** a **webAppName** definice pro vaši webovou aplikaci na řádky 11 a 12, v uvedeném pořadí:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Změňte řádek 13 k serveru registru kontejneru:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Změňte řádek 16 použijte ID přihlašovacích údajů do instance Jenkinse:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Vytvoření kanálu Jenkins    

1. Otevřete Jenkinse ve webovém prohlížeči. Vyberte **New Item** (Nová položka).
2. Zadejte název projektu a vyberte **kanálu**. Vyberte **OK**.
3. Vyberte **kanálu** kartu.
4. Pro **definice** hodnotu, vyberte **kanálu skriptu ze Správce řízení služeb**.
5. Pro **SCM** hodnotu, vyberte **Git**. Zadejte adresu URL GitHub vašeho rozvětveného úložiště. Příklad: https://&lt;your_forked_repo > .git.
7. Aktualizace **cesta ke skriptu** hodnota, která se **Jenkinsfile_container_plugin**.
8. Vyberte **Uložit** a spusťte úlohu.

## <a name="verify-your-web-app"></a>Ověřte svou webovou aplikaci

1. Pokud chcete ověřit úspěšné nasazení souboru WAR do vaší webové aplikace, otevřete webový prohlížeč.
2. Přejděte na http://&lt;your_app_name >.azurewebsites.net/api/calculator/ping. Nahraďte &lt;your_app_name > s názvem vaší webové aplikace. Zobrazí se zpráva:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Přejděte na http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Nahraďte &lt;x > a &lt;y > s všechna čísla zobrazíte součet hodnot x + y. Kalkulačce zobrazí součet: ![Kalkulačka: Přidat](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Pro službu Azure App Service v Linuxu

1. Pokud chcete ověřit vaši webovou aplikaci, spusťte následující příkaz v rozhraní příkazového řádku Azure:
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

3. Přejděte na http://&lt;your_app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y >. Nahraďte &lt;x > a &lt;y > s všechna čísla zobrazíte součet hodnot x + y.
    
## <a name="troubleshooting"></a>Řešení potíží

Pokud narazíte na jakékoli chyby s moduly plug-in Jenkins, založte problém v [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="next-steps"></a>Další postup

V tomto kurzu se používá modul plug-in Jenkinse pro Azure App Service nasadit do Azure.

Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Konfigurace Jenkinse k nasazení služby Azure App Service pomocí FTP 
> * Konfigurace Jenkinse k nasazení do Web App for Containers 
