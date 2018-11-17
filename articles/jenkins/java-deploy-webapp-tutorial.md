---
title: Použití Jenkinse k nasazení webových aplikací do Azure
description: Nastavte pro své webové aplikace v Javě průběžnou integraci z GitHubu do služby Azure App Service s využitím Jenkinse a Dockeru.
ms.service: jenkins
keywords: jenkins, azure, devops, app service, continuous integration, ci, continuous deployment, cd
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: de1d89da041ede13741a804b3e77018fb48b4aa3
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51822054"
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Nastavení průběžné integrace a nasazování do služby Azure App Service s využitím Jenkinse

V tomto kurzu se pomocí Jenkinse nastaví průběžná integrace a nasazování (CI/CD) ukázkové webové aplikace v Javě vytvořené s využitím architektury [Spring Boot](http://projects.spring.io/spring-boot/) do služby [Azure App Service Web App on Linux](/azure/app-service/containers/app-service-linux-intro).

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Instalace modulů plug-in Jenkinse nezbytných k nasazení do služby Azure App Service
> * Definice úlohy Jenkinse pro sestavení imagí Dockeru z úložiště GitHubu při vložení nového potvrzení
> * Definice nové webové aplikace Azure pro Linux a její konfigurace pro nasazení imagí Dockeru odeslaných do registru kontejneru Azure
> * Konfigurace modulu plug-in Jenkinse pro Azure App Service
> * Nasazení ukázkové aplikace do služby Azure App Service s ručním sestavením
> * Aktivace sestavení Jenkinse a aktualizace webové aplikace nasdílením změn do GitHubu

## <a name="before-you-begin"></a>Než začnete

Pro absolvování tohoto kurzu potřebujete:

* [Jenkinse](https://jenkins.io/) s nakonfigurovanou sadou JDK a nástroji Maven. Pokud systém Jenkins nemáte, vytvořte ho teď v Azure s použitím [šablony řešení Jenkinse](/azure/jenkins/install-jenkins-solution-template).
* Účet [GitHub](https://github.com).
* [Azure CLI](/cli/azure), a to buď z místního příkazového řádku, nebo ve službě [Azure Cloud Shell](/azure/cloud-shell/overview).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>Instalace modulů plug-in Jenkinse

1. Otevřete ve webovém prohlížeči webovou konzolu Jenkinse, v levé nabídce vyberte **Manage Jenkins** (Správa Jenkinse) a pak vyberte **Manage Plugins** (Správa modulů plug-in).
2. Vyberte kartu **Available** (K dispozici).
3. Vyhledejte následující moduly plug-in a zaškrtněte políčka vedle nich:   

    - [Azure App Service Plug-in](https://plugins.jenkins.io/azure-app-service)
    - [GitHub Branch Source Plug-in](https://plugins.jenkins.io/github-branch-source)

    Pokud se tyto moduly plug-in nezobrazí, na kartě **Installed** (Nainstalované) se ujistěte, že ještě nejsou nainstalované.

1. Výběrem možnosti **Download now and install after restart** (Stáhnout teď a nainstalovat po restartování) povolte moduly plug-in ve své konfiguraci Jenkinse.

## <a name="configure-github-and-jenkins"></a>Konfigurace GitHubu a Jenkinse

Nastavte Jenkinse tak, aby při vložení nových potvrzení do úložiště ve vašem účtu přijímal [webhooky GitHubu](https://developer.github.com/webhooks/).

1. Vyberte **Manage Jenkins** (Správa Jenkinse) a pak **Configure System** (Konfigurace systému). V části **GitHub** se ujistěte, že je vybraná možnost **Manage hooks** (Spravovat webhooky), a pak vyberte **Manage additional GitHub actions** (Spravovat další akce GitHubu) a zvolte **Convert login and password to token** (Převést přihlašovací jméno a heslo na token).
2. Vyberte přepínač **From login and password** (Z uživatelského jména a hesla) a zadejte své uživatelské jméno a heslo pro GitHub. Vyberte **Create token credentials** (Vytvořit přihlašovací údaje tokenu) a vytvořte nový [token PAT GitHubu](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   
   ![Vytvoření tokenu PAT GitHubu z přihlašovacího jména a hesla](media/jenkins-java-quickstart/create_github_credentials.png)
3.  V rozevíracím seznamu **Credentials** (Přihlašovací údaje) v konfiguraci serverů GitHubu vyberte nově vytvořený token. Vyberte **Test connection** (Test připojení) a ověřte, že ověřování funguje.   
   ![Ověření připojení ke GitHubu po nakonfigurování tokenu PAT](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> Pokud má váš účet na GitHubu povolené dvoustupňové ověřování, vytvořte token na GitHubu a nakonfigurujte Jenkinse pro jeho použití. Úplné podrobnosti najdete v dokumentaci k [modulu plug-in Jenkinse pro GitHub](https://wiki.jenkins.io/display/JENKINS/Github+Plugin).

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>Vytvoření forku ukázkového úložiště a vytvoření úlohy Jenkinse 

1. Otevřete [úložiště ukázkové aplikace Spring Boot](https://github.com/spring-guides/gs-spring-boot-docker) a vytvořte jeho fork pro vlastní účet na GitHubu tím, že v pravém horním rohu vyberete **Fork** (Vytvořit fork).   
    ![Vytvoření forku na GitHubu](media/jenkins-java-quickstart/fork_github_repo.png)
1. Ve webové konzole Jenkinse vyberte **New Item** (Nová položka), pojmenujte ji **MyJavaApp**, vyberte **Freestyle project** (Volný projekt) a pak vyberte **OK**.   
    ![Nový volný projekt Jenkinse](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. V části **General** (Obecné) vyberte projekt **GitHub** a zadejte adresu URL vytvořeného forku úložiště, například https://github.com/raisa/gs-spring-boot-docker.
3. V části **Source code management** (Správa zdrojového kódu) vyberte **Git** a zadejte adresu URL vytvořeného forku úložiště `.git`, například https://github.com/raisa/gs-spring-boot-docker.git.
4. V části **Build Triggers** (Triggery sestavení) vyberte **GitHub hook trigger for GITscm polling** (Trigger webhooku GitHubu pro dotazování GITscm).
5. V části **Build** (Sestavení) vyberte **Add build step** (Přidat krok sestavení) a zvolte **Invoke top-level Maven targets** (Vyvolat cíle Maven nejvyšší úrovně). Do pole **Goals** (Cíle) zadejte `package`.
6. Vyberte **Uložit**. Úlohu můžete otestovat tak, že na stránce projektu vyberete **Build Now** (Sestavit).

## <a name="configure-azure-app-service"></a>Konfigurace služby Azure App Service 

1. Pomocí Azure CLI nebo služby [Cloud Shell](/azure/cloud-shell/overview) vytvořte novou službu [Web App on Linux](/azure/app-service/containers/app-service-linux-intro). Název webové aplikace v tomto kurzu je `myJavaApp`, ale pro svou vlastní aplikaci musíte použít jedinečný název.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan --runtime "java|1.8|Tomcat|8.5"
    ```

2. Vytvořte službu [Azure Container Registry](/azure/container-registry/container-registry-intro) pro ukládání imagí Dockeru sestavených Jenkinsem. Název registru kontejneru použitý v tomto kurzu je `jenkinsregistry`, ale pro svůj vlastní registr kontejneru musíte použít jedinečný název. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. Nakonfigurujte webovou aplikaci tak, aby spouštěla image Dockeru odeslané do registru kontejneru, a určete, že aplikace spuštěná v kontejneru naslouchá požadavkům na portu 8080.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Konfigurace modulu plug-in Jenkinse pro Azure App Service

1. Ve webové konzole Jenkinse vyberte úlohu **MyJavaApp**, kterou jste vytvořili, a pak na levé straně stránky vyberte **Configure** (Konfigurovat).
2. Přejděte dolů k části **Post-build Actions** (Akce po sestavení), vyberte**Add post-build action** (Přidat akci po sestavení) a zvolte **Publish an Azure Web App** (Publikovat webovou aplikaci Azure).
3. V části **Azure Profile Configuration** (Konfigurace profilu Azure) vyberte **Add** (Přidat) vedl položky **Azure Credentials** (Přihlašovací údaje Azure) a zvolte **Jenkins**.
4. V dialogovém okně **Add Credentials** (Přidání přihlašovacích údajů) v rozevíracím seznamu **Kind** (Druh) vyberte **Microsoft Azure Service Principal** (Instanční objekt Microsoft Azure).
5. Pomocí Azure CLI nebo služby [Cloud Shell](/azure/cloud-shell/overview) vytvořte instanční objekt Active Directory.
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
6. Zadejte přihlašovací údaje z instančního objektu do dialogového okna **Add credentials** (Přidání přihlašovacích údajů). Pokud neznáte ID vašeho předplatného Azure, můžete ho získat dotazem z rozhraní příkazového řádku:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    ![Konfigurace instančního objektu Azure](media/jenkins-java-quickstart/azure_service_principal.png)
6. Vyberte **Verify Service Principal** (Ověřit instanční objekt) a ověřte, že se instanční objekt ověřuje v Azure. 
7. Vyberte **Add** (Přidat) a uložte přihlašovací údaje.
8. Po návratu do konfigurace **Publish an Azure Web App** (Publikovat webovou aplikaci Azure), vyberte v rozevíracím seznamu **Azure Credentials** (Přihlašovací údaje Azure) přihlašovací údaje instančního objektu, který jste právě přidali.
9. V části **App Configuration** (Konfigurace aplikace) zvolte v rozevíracím seznamu skupinu prostředků a název webové aplikace.
10. Vyberte přepínač **Publish via Docker** (Publikovat přes Docker).
11. Jako **Dockerfile path** (Cesta k souboru Dockerfile) zadejte `complete/Dockerfile`.
12. Do pole **Docker registry URL** (Adresa URL registru Dockeru) zadejte `https://jenkinsregistry.azurecr.io`.
13. Vyberte **Add** (Přidat) vedle položky **Registry Credentials** (Přihlašovací údaje registru). 
14. Jako **Username** (Uživatelské jméno) zadejte uživatelské jméno správce služby Azure Container Registry, které jste vytvořili.
15. Do pole **Password** (Heslo) zadejte heslo pro službu Azure Container Registry. Uživatelské jméno a heslo můžete získat na webu Azure Portal nebo pomocí následujícího příkazu rozhraní příkazového řádku:

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![Přidání přihlašovacích údajů registru kontejneru](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. Vyberte **Add** (Přidat) a uložte přihlašovací údaje.
16. Na panelu **App Configuration** (Konfigurace aplikace) v části **Publish an Azure Web App** (Publikovat webovou aplikaci Azure) vyberte v rozevíracím seznamu **Registry credentials** (Přihlašovací údaje registru) nově vytvořené přihlašovací údaje. Dokončená akce po sestavení by měla vypadat jako na následujícím obrázku:   
    ![Konfigurace akce po sestavení pro nasazení služby Azure App Service](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. Vyberte **Save** (Uložit) a uložte konfiguraci úlohy.

## <a name="deploy-the-app-from-github"></a>Nasazení aplikace z GitHubu

1. V projektu Jenkinse vyberte **Build Now** (Sestavit) a nasaďte ukázkovou aplikaci do Azure.
2. Po dokončení sestavení bude vaše aplikace spuštěná v Azure na své adrese URL pro publikování, například http://myjavaapp.azurewebsites.net.   
   ![Zobrazení nasazené aplikace v Azure](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>Nasdílení změn a opětovné nasazení

1. Na webu vašeho forku GitHubu přejděte do `complete/src/main/java/Hello/Application.java`. V pravé části rozhraní GitHubu vyberte odkaz **Edit this file** (Upravit tento soubor).
2. Proveďte následující změnu metody `home()` a potvrďte změnu do hlavní větve úložiště.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. V Jenkinsu se spustí nové sestavení aktivované novým potvrzením ve větvi `master` úložiště. Po jeho dokončení znovu načtěte svou aplikaci v Azure.     
      ![Zobrazení nasazené aplikace v Azure](media/jenkins-java-quickstart/hello_docker_world.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Řešení potíží s modulem plug-in Jenkinse

Pokud v modulech plug-in Jenkinse narazíte na nějaké chyby, založte problém na stránce [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Použití virtuálních počítačů Azure jako agentů sestavení](/azure/jenkins/jenkins-azure-vm-agents)
