---
title: Nasazení webové aplikace do Azure pomocí volaných | Microsoft Docs
description: Nastavte průběžnou integraci z Githubu do služby Azure App Service pro webové aplikace Java pomocí volaných a Docker.
author: rloutlaw
manager: douge
ms.service: jenkins
ms.search.scope: ''
ms.devlang: java
ms.topic: article
ms.workload: web
ms.date: 08/02/2017
ms.author: routlaw
ms.custom: Jenkins, devcenter
ms.openlocfilehash: b2606acba341d4cfbc16314048e134fa30ff8606
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852995"
---
# <a name="set-up-continuous-integration-and-deployment-to-azure-app-service-with-jenkins"></a>Nastavte průběžnou integraci a nasazení do Azure App Service pomocí volaných

V tomto kurzu nastaví průběžnou integraci a nasazení (CI/CD) ukázkovou webovou aplikaci Java vyvinuté pomocí [pružiny spouštěcí](http://projects.spring.io/spring-boot/) framework [webové aplikace Azure App Service v systému Linux](/azure/app-service/containers/app-service-linux-intro) použití volaných.

V tomto kurzu budete provádět následující úlohy:

> [!div class="checklist"]
> * Nainstalujte volaných moduly plug-in potřebné k nasazení do Azure App Service.
> * Definujte úlohu volaných k vytvoření imagí Dockeru z úložiště GitHub když se instaluje nové potvrzení.
> * Definovat nové webové aplikace Azure pro Linux a nakonfigurovat ho k nasazení bitových kopií Docker nabídnutých do kontejneru Azure registru.
> * Konfigurace modulu plug-in volaných Azure App Service.
> * Nasadíte ukázkovou aplikaci do služby Azure App Service s ruční sestavení.
> * Aktivovat build volaných a aktualizovat webovou aplikaci vynucením změny Githubu.

## <a name="before-you-begin"></a>Než začnete

Pro absolvování tohoto kurzu potřebujete:

* [Volaných](https://jenkins.io/) nástroje JDK a Maven nakonfigurované. Pokud nemáte volaných systém, vytvořte ho nyní v Azure z [šablona řešení volaných](/azure/jenkins/install-jenkins-solution-template).
* Účet [GitHub](https://github.com).
* [Azure CLI 2.0](/cli/azure), z vaší místní příkazového řádku nebo v [prostředí cloudu Azure](/azure/cloud-shell/overview)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-jenkins-plug-ins"></a>Nainstalujte volaných moduly plug-in

1. Otevřete webový prohlížeč, aby volaných webovou konzolu a vyberte **spravovat volaných** z nabídky na levé straně zvolte **Správa modulů plug-in**.
2. Vyberte **dostupné** kartě.
3. Vyhledejte a zaškrtněte políčko vedle následující moduly plug-in:   

    - [Modul Plug-in Azure App Service](https://plugins.jenkins.io/azure-app-service)
    - [Modul Plug-in zdroje větve na Githubu](https://plugins.jenkins.io/github-branch-source)

    Pokud se nezobrazí moduly plug-in, ujistěte se, již nejsou nainstalovány kontrolou **nainstalovaná** kartě.

1. Vyberte **stáhnout a nainstalovat po restartování** povolit moduly plug-in ve vaší konfiguraci volaných.

## <a name="configure-github-and-jenkins"></a>Konfigurace Githubu a volaných

Nastavení volaných pro příjem [Githubu webhooky](https://developer.github.com/webhooks/) při nové potvrzení odesílají na úložišti ve vašem účtu.

1. Vyberte **spravovat volaných**, pak **konfiguraci systému**. V **Githubu** část, zajistěte, aby **spravovat háky** je vybrána a potom vyberte **spravovat další akce Githubu** a zvolte **převést přihlášení a heslo pro token**.
2. Vyberte **z přihlašovací jméno a heslo** přepínač a zadejte uživatelské jméno Githubu a heslo. Vyberte **vytvořit token pověření** pro vytvoření nového [Githubu osobní tokenu přístupu](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   
   ![Vytvoření Githubu PAT z přihlašovací jméno a heslo](media/jenkins-java-quickstart/create_github_credentials.png)
3.  Vyberte nově vytvořenou tokenu z **pověření** rozevírací nabídku v konfiguraci serverů Githubu. Vyberte **testovací připojení** k ověření, že ověřování funguje.   
   ![Ověření připojení ke Githubu, jakmile Jan nakonfigurovaný](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> Pokud má váš účet GitHub povoleno dvoufaktorové ověřování, vytvoření tokenu na Githubu a nakonfigurujte volaných ji použít. Zkontrolujte [volaných Githubu modulu plug-in](https://wiki.jenkins.io/display/JENKINS/Github+Plugin) dokumentace pro úplné podrobnosti.

## <a name="fork-the-sample-repo-and-create-a-jenkins-job"></a>Ukázka úložiště rozvětvit a vytvořit úlohu volaných 

1. Otevřete [pružiny spuštění ukázkové aplikace úložišti](https://github.com/spring-guides/gs-spring-boot-docker) a rozvětvit k účtu GitHub výběrem **rozvětvení** v horním pravém rohu.   
    ![Rozvětvení z Githubu](media/jenkins-java-quickstart/fork_github_repo.png)
1. Ve webové konzole volaných vyberte **nová položka**, zadejte jeho název **MyJavaApp**, vyberte **volný styl projektu**, pak vyberte **OK**.   
    ![Nový projekt volný volaných styl](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. V části **Obecné** vyberte **Githubu** projektu a zadejte URL forked úložišti například https://github.com/raisa/gs-spring-boot-docker
3. V části **zdrojového kódu správu** vyberte **Git**, zadejte vaše forked úložišti `.git` adresy URL, například https://github.com/raisa/gs-spring-boot-docker.git
4. V části **Build Triggers** (Triggery sestavení) vyberte **GitHub hook trigger for GITscm polling** (Trigger webhooku GitHubu pro dotazování GITscm).
5. V části **sestavení** vyberte **přidat krok sestavení** a zvolte **vyvolání nejvyšší úrovně cíle Maven**. Zadejte `package` v **cíle** pole.
6. Vyberte **Uložit**. Úlohu můžete otestovat výběrem **sestavení teď** ze stránky projektu.

## <a name="configure-azure-app-service"></a>Konfigurace služby Azure App Service 

1. Použití Azure CLI nebo [cloudové prostředí](/azure/cloud-shell/overview), vytvořte novou [webové aplikace v systému Linux](/azure/app-service/containers/app-service-linux-intro). Název webové aplikace v tomto kurzu je `myJavaApp`, ale budete muset použít jedinečný název pro vlastní aplikace.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan --runtime "java|1.8|Tomcat|8.5"
    ```

2. Vytvoření [registru kontejner Azure](/azure/container-registry/container-registry-intro) k ukládání imagí Dockeru vytvořené volaných. Název registru kontejneru, který se používá v tomto kurzu je `jenkinsregistry`, ale budete muset použít jedinečný název pro vlastní kontejner registru. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. Konfigurovat webovou aplikaci spustit nabídnutých do registru kontejner imagí Dockeru a určit, že spuštěné v kontejneru aplikace naslouchá požadavkům na portu 8080.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## <a name="configure-the-azure-app-service-jenkins-plug-in"></a>Konfigurace modulu plug-in volaných Azure App Service

1. Ve webové konzole volaných, vyberte **MyJavaApp** úlohy, kterou jste vytvořili a pak vyberte **konfigurace** v levém dolním rohu stránky.
2. Přejděte dolů k položce **akce po sestavení**, pak vyberte **přidat akci po sestavení** a zvolte **publikování webové aplikace Azure**.
3. V části **konfiguraci profilu Azure**, vyberte **přidat** vedle **přihlašovací údaje Azure** a zvolte **volaných**.
4. V **přidat přihlašovací údaje** dialogovém okně, vyberte **Microsoft Azure Service Principal** z **druh** rozevíracího seznamu.
5. Vytvořit objekt Active Directory Service z příkazového řádku Azure nebo [cloudové prostředí](/azure/cloud-shell/overview).
    
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
6. Zadejte přihlašovací údaje z hlavní do služby **přidejte pověření** dialogové okno. Pokud si nejste jisti svoje ID předplatného Azure, můžete zadat dotaz z rozhraní příkazového řádku:
     
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

    ![Konfigurace Azure instančního objektu](media/jenkins-java-quickstart/azure_service_principal.png)
6. Ověřte ověří objekt služby Azure tak, že vyberete **ověřte instanční objekt**. 
7. Vyberte **přidat** přihlašovací údaje uložit.
8. Vyberte hlavní přihlašovací údaje služby jste právě přidali z **přihlašovací údaje Azure** rozevíracího seznamu po zpět **publikování webové aplikace Azure** konfigurace.
9. V **konfigurace aplikací**, zvolte vaší skupiny prostředků a webové aplikace název z rozevíracího seznamu.
10. Vyberte **publikovat prostřednictvím Docker** přepínač.
11. Zadejte `complete/Dockerfile` pro **cesta soubor Docker**.
12. Zadejte `https://jenkinsregistry.azurecr.io` v **Docker registru URL** pole.
13. Vyberte **přidat** vedle **registru pověření**. 
14. Zadejte uživatelské jméno správce pro registru kontejner Azure jste vytvořili pro **uživatelské jméno**.
15. Zadejte heslo pro kontejner Azure registru **heslo** pole. Uživatelské jméno a heslo můžete získat z portálu Azure nebo prostřednictvím rozhraní příkazového řádku následující příkaz:

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![Zadejte svoje přihlašovací údaje registru kontejneru](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. Vyberte **přidat** uložit přihlašovací údaje.
16. Vyberte nově vytvořené pověření z **registru pověření** rozevírací seznam v **konfigurace aplikací** panelu pro **publikování webové aplikace Azure**. Dokončení akce po sestavení by měl vypadat podobně jako na následujícím obrázku:   
    ![Konfigurace akce POST sestavení pro nasazení aplikace Azure App Service](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. Vyberte **Uložit** uložit konfiguraci úlohy.

## <a name="deploy-the-app-from-github"></a>Nasazení aplikace z webu GitHub

1. Z projektu volaných vyberte **sestavení teď** nasazení ukázkové aplikace do Azure.
2. Po sestavení se dokončí, je za provozu v Azure na jeho publikování adrese URL, například http://myjavaapp.azurewebsites.net vaší aplikace.   
   ![Zobrazení nasazené aplikace v Azure](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## <a name="push-changes-and-redeploy"></a>Doručte změny a znovu nasaďte

1. Z vaší rozvětvení Githubu, vyhledejte na webu na `complete/src/main/java/Hello/Application.java`. Vyberte **upravit tento soubor** odkaz z pravé strany rozhraní Githubu.
2. Proveďte následující změny na `home()` metoda a provést změny do hlavní větve úložišti.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. Nové sestavení se spustí v volaných, aktivovány nové potvrzení na `master` větve úložišti. Po jeho dokončení znovu načtete aplikaci v Azure.     
      ![Zobrazení nasazené aplikace v Azure](media/jenkins-java-quickstart/hello_docker_world.png)
  
## <a name="next-steps"></a>Další postup

- [Použijte virtuální počítače Azure jako agentů sestavení](/azure/jenkins/jenkins-azure-vm-agents)
- [Spravovat prostředky v úlohy a kanály pomocí Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline)
 
