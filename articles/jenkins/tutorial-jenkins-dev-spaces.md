---
title: Pomocí modulu plug-in Azure Dev prostory pro Jenkinse pomocí služby Azure Kubenetes
description: Další informace o použití modulu plug-in Azure Dev mezery v kanálu průběžné integrace.
author: tomarchermsft
ms.author: tarcher
ms.service: jenkins
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/18/2019
ms.openlocfilehash: 8c47b8caf2d289ed17647b8003cc702156f3cddb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592077"
---
<!-- GMinchAQ, 06/18/19 -->

# <a name="tutorial-using-the-azure-dev-spaces-plugin-for-jenkins-with-azure-kubenetes-service"></a>Kurz: Pomocí modulu plug-in Azure Dev prostory pro Jenkinse pomocí služby Azure Kubenetes 

Azure Dev prostory umožňuje testování a iterativní vývoj mikroslužeb aplikace spuštěná ve službě Azure Kubernetes Service (AKS) bez nutnosti replikovat nebo napodobení závislosti. Modul plug-in Azure Dev prostory jenkinse vám pomůže používat Dev mezery v průběžnou integraci a doručování (CI/CD) kanálu.

V tomto kurzu také využívá Azure Container Registry (ACR). ACR ukládá obrázky a úlohu služby ACR sestavení Dockeru a Helm artefakty. Pomocí služby ACR a úlohu služby ACR pro generování artefakt už není nutné k instalaci dalšího softwaru, jako je Docker, na vašem serveru Jenkins. 

V tomto kurzu dokončíte tyto úlohy:

> [!div class="checklist"]
> * Vytvoření clusteru AKS povolené prostory vývoj Azure
> * Nasazení víc služeb aplikace pro AKS
> * Příprava serveru Jenkins
> * Náhled změn kódu před sloučením do projektu pomocí modulu plug-in Azure Dev mezery v kanálech Jenkinse

Tento kurz předpokládá zprostředkující znalost základních služeb Azure, AKS, ACR, Azure Dev mezery, Jenkins [kanály](https://jenkins.io/doc/book/pipeline/) a moduly plug-in a Githubu. Základní znalost podpůrné nástroje, jako je kubectl a Helm je užitečné.

## <a name="prerequisites"></a>Požadavky

* Účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Účet GitHub. Pokud ještě nemáte účet Githubu, vytvořte [bezplatný účet](https://github.com/) předtím, než začnete.

* [Visual Studio Code](https://code.visualstudio.com/download) s [Azure Dev prostory](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) nainstalované rozšíření.

* [Nainstalované Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest), verze 2.0.43 nebo vyšší.

* Hlavní server Jenkins. Pokud ještě nemáte hlavní server Jenkinse, nasaďte [Jenkins](https://aka.ms/jenkins-on-azure) v Azure pomocí následujících kroků v tomto [rychlý Start](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Jenkins server musí mít Helm a kubectl nainstalované a k dispozici pro účet Jenkinse, jak je popsáno dále v tomto kurzu.

* VS Code, terminálu VS Code nebo WSL a prostředí Bash. 


## <a name="create-azure-resources"></a>Vytvoření prostředků Azure

V této části vytvoříte prostředky Azure:

* Skupina prostředků obsahuje všechny prostředky Azure pro účely tohoto kurzu.
* [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) clusteru (AKS).
* [Registr kontejnerů Azure](https://docs.microsoft.com/azure/container-registry/) (ACR) sestavení (pomocí ACR úkoly) a ukládání imagí Dockeru.

1. Vytvořte skupinu prostředků.

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. Vytvoření clusteru AKS. Vytvoření clusteru AKS v [oblast, která podporuje vývoj prostory](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams).

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Nakonfigurujte AKS Dev prostory.

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Tento krok nainstaluje `azds` rozšíření rozhraní příkazového řádku.

4. Vytvoření registru kontejnerů.

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Nasazení ukázkové aplikace do clusteru AKS

V této části nastavit prostor vývoj a nasazení ukázkové aplikace do clusteru AKS, který jste vytvořili v předchozí části. Aplikace se skládá ze dvou částí *webfrontend* a *mywebapi*. Obě komponenty jsou nasazené v prostoru vývoj. Později v tomto kurzu budete odeslání žádosti o přijetí změn mywebapi pro aktivaci kanálu CI v Jenkinsu.

Další informace o používání prostorů Azure Dev a vývoj pro víc služeb s Azure Dev prostory, naleznete v tématu [začít používat Azure Dev prostory s Javou](https://docs.microsoft.com/azure/dev-spaces/get-started-java), a [víc služeb vývoj s Azure Dev prostory](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Tyto kurzy naleznete další informace zde nejsou zahrnuty.

1. Stáhněte si https://github.com/Azure/dev-spaces úložiště z Githubu.

2. Otevřít `samples/java/getting-started/webfrontend` složky ve VS Code. (Případné výchozí výzvy ohledně přidání prostředků ladění nebo obnovení projektu můžete ignorovat.)

3. Aktualizace `/src/main/java/com/ms/sample/webfrontend/Application.java` k vypadat nějak takto:

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. Klikněte na tlačítko **zobrazení** pak **terminálu** otevřít integrovaný terminál v nástroji VS Code.

5. Spustit `azds prep` příkaz připravit aplikace na spouštění v prostoru vývoj. Tento příkaz musí spustit z `dev-spaces/samples/java/getting-started/webfrontend` připravit vaše aplikace správně:

    ```bash
    azds prep --public
    ```

    Vývoj prostory rozhraní příkazového řádku `azds prep` příkaz vygeneruje Docker a Kubernetes prostředky s výchozím nastavením. Tyto soubory zachovat po dobu životnosti projektu a můžete je přizpůsobit:

    * `./Dockerfile` a `./Dockerfile.develop` popisují aplikace image kontejneru a jak zdrojový kód je vytvořena a běží v rámci kontejneru.
    * [Helm chart](https://helm.sh/docs/developing_charts/) v `./charts/webfrontend` popisuje, jak do Kubernetes nasadit kontejner.
    * `./azds.yaml` je konfigurační soubor Azure Dev mezery.

    Další informace najdete v tématu [jak prostory vývoj Azure funguje a je nakonfigurován](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Sestavte a spusťte aplikaci v AKS pomocí `azds up` příkaz:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Kontrola bude výstup konzoly informace o adrese URL, který byl vytvořen `up` příkazu. Bude v tomto tvaru:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Tuto adresu URL otevřít v okně prohlížeče a zobrazí se webová aplikace. Během spouštění kontejneru se výstup `stdout` a `stderr` streamuje do okna terminálu.

8. V dalším kroku vytvořte a nasaďte *mywebapi*:

    1. Změňte adresář na `dev-spaces/samples/java/getting-started/mywebapi`

    2. Spusťte

        ```bash
        azds prep
        ```

    3. Spusťte

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Příprava serveru Jenkins

V této části Příprava serveru Jenkins a spusťte ukázkový kanál CI.

* Instalace modulů plug-in
* Nainstalovat Helm a rozhraní příkazového řádku Kubernetes
* Přidání přihlašovacích údajů

### <a name="install-plugins"></a>Instalace modulů plug-in

1. Přihlaste se k serveru Jenkins. Zvolte **Jenkins Správa > Správa modulů plug-in**.
2. Na **dostupné** kartu, vyberte následující moduly plug-in:
    * [Azure Dev mezery](https://plugins.jenkins.io/azure-dev-spaces)
    * [Úlohy Azure Container Registry](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Environment Injector](https://plugins.jenkins.io/envinject)
    * [Integrace Githubu](https://plugins.jenkins.io/github-pullrequest)

    Pokud tyto moduly plug-in se v seznamu nezobrazí, zkontrolujte, **nainstalováno** kartu pro zobrazení, pokud už instalované.

3. Chcete-li nainstalovat na moduly plug-in, zvolte **stáhnout a nainstalovat po restartování**.

4. Restartujte server Jenkins k dokončení instalace.

### <a name="install-helm-and-kubectl"></a>Nainstalovat Helm a kubectl

Ukázkový kanál používá k nasazení do prostoru dev Helm a kubectl. Při instalaci Jenkinse, vytvoří účet správce s názvem *jenkins*. Příkaz Helm a kubectl musí být přístupné pro uživatele jenkinse.

1. Vytvořte připojení SSH k hlavnímu serveru Jenkins. 

2. Přepněte `jenkins` uživatele:
    ```bash
    sudo su jenkins
    ```

3. Nainstalujte Helm CLI. Další informace najdete v tématu [instalace Helm](https://helm.sh/docs/using_helm/#installing-helm).

4. Instalace kubectl. Další informace najdete v tématu [ **az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Přidat přihlašovací údaje k Jenkinsu

1. Jenkins musí instančního objektu Azure pro ověřování a přístup k prostředkům Azure. Vytvoření instančního objektu, najdete [vytvořit instanční objekt](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) části v části nasazení služby Azure App Service v tomto kurzu. Nezapomeňte uložit kopii výstup z `create-for-rbac` vzhledem k tomu, že je nutné tyto informace k dokončení dalšího kroku. Výstup bude vypadat přibližně takto:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Přidat *Microsoft Azure Service Principal* přihlašovacích údajů zadejte Jenkins, pomocí informací o instančním objektu služby z předchozího kroku. Názvy v následujícím snímku obrazovky odpovídají výstup z `create-for-rbac`.

    **ID** pole je Jenkinse název přihlašovacích údajů instančního objektu služby. V příkladu se používá hodnota `displayName` (v tomto případě `xxxxxxxjenkinssp`), ale můžete použít jakýkoli text, který chcete. Tento název přihlašovacího údaje je hodnota proměnné prostředí AZURE_CRED_ID v další části.

    ![Přidat přihlašovací údaje instančního objektu služby k Jenkinsu](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    **Popis** je volitelný. Podrobnější pokyny najdete v článku [přidat instančního objektu s Jenkinsem](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) části v části nasazení služby Azure App Service v tomto kurzu. 



3. Chcete-li zobrazit svoje přihlašovací údaje služby ACR, spusťte tento příkaz:

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    Vytvořte kopii výstupu, který by měl vypadat asi takhle nějak. JSON:

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. Přidat *uživatelské jméno s heslem* typ v Jenkinsu přihlašovacích údajů. **Uživatelské jméno** uživatelského jména z poslední krok v tomto příkladu je `acr01`. **Heslo** je hodnota pro první heslo v tomto příkladu `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. **ID** Tento přihlašovací údaj je hodnota ACR_CRED_ID.

5. Nastavení přihlašovacích údajů AKS. Přidat *konfiguraci Kubernetes (kubeconfig)* přihlašovacích údajů zadejte Jenkins (pomocí možnosti "Přímo Enter"). Chcete-li získat přístup k přihlašovací údaje pro váš cluster AKS, spusťte následující příkaz:

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   **ID** Tento přihlašovací údaj je hodnota KUBE_CONFIG_ID v další části.

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Scénář vybrané příklad profilace je založena na vzoru každodenní praxe: Žádost o přijetí změn, že aktivační události CI kanálu, který sestaví a potom nasadí navrhovaných změn do Azure dev prostoru pro testování a kontroly. V závislosti na výsledky kontroly změny jsou buď sloučení a nasazení do AKS nebo zahozeny. Nakonec se odebere mezera vývoj.

Konfigurace kanálu Jenkins a souboru Jenkinsfile definovat fáze v kanálu CI. Tento diagram ukazuje fáze zřetězení a rozhodovací body, které jsou definované souboru Jenkinsfile:

![Tok kanálu Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Stáhněte si upravenou verzi *mywebapi* projektu z https://github.com/azure-devops/mywebapi. Tento projekt obsahuje několik souborů, které jsou potřebné k vytvoření kanálu, včetně *souboru Jenkinsfile*, *soubory Dockerfile*a grafu helmu.

2. Přihlásit se do Jenkinse. V nabídce na levé straně vyberte **přidat položku**.

3. Vyberte **kanálu**a pak zadejte název do **zadejte název položky** pole. Vyberte **OK**, a pak se automaticky otevře na obrazovce pro konfiguraci kanálu.

4. Na **Obecné** kartě **Příprava prostředí pro běh**. 

5. Zkontrolujte **ponechat proměnné prostředí Jenkins** a **zachovat buildu Jenkinse proměnné**.

6. V **vlastnosti obsahu** zadejte následující proměnné prostředí:

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    Použití ukázkové hodnoty uvedené v předchozí části, seznam proměnných prostředí by měl vypadat přibližně takto:

    ![Proměnné prostředí kanálu Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Zvolte **kanálu skriptu ze Správce řízení služeb** v **kanálu > definice**.
8. V **SCM**, zvolte **Git** a potom zadejte adresu URL svého úložiště.
9. V **větev specifikátor**, zadejte `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Zadejte správce řízení služeb úložiště adresy URL a skript cestu "Souboru Jenkinsfile".
11. **Zjednodušené checkout** by měly být porovnány.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Vytvořit žádost o přijetí změn pro aktivaci kanálu

K dokončení kroku 3 v této části budete muset komentář část souboru Jenkinsfile, jinak obdržíte chybu 404, při pokusu o zobrazení novém i starém verzí vedle sebe. Ve výchozím nastavení když se rozhodnete sloučení žádosti o přijetí změn, sdílené předchozí verzi mywebapi budou odstraněna a nahrazuje nová verze. Proveďte následující změnu souboru Jenkinsfile před dokončením kroku 1:

```Groovy
    if (userInput == true) {
        stage('deploy') {
            // Apply the deployment to shared namespace in AKS using acsDeploy, Helm or kubectl   
        }
        
        stage('Verify') {
            // verify the staging environment is working properly
        }
        
        /* Comment the cleanup stage to allow side by side comparison with the new child dev space

        stage('cleanup') {
            devSpacesCleanup aksName: env.AKS_NAME, 
                azureCredentialsId: env.AZURE_CRED_ID, 
                devSpaceName: devSpaceNamespace, 
                kubeConfigId: env.KUBE_CONFIG_ID, 
                resourceGroupName: env.AKS_RES_GROUP,
                helmReleaseName: releaseName
        }
        */

    } else {
        // Send a notification
    }
```

1. Změny `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`a pak vytvořte žádost o přijetí změn. Příklad:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Přihlaste se do Jenkinse a vyberte název kanálu, klikněte na tlačítko **Build Now**. 

    Můžete také nastavit *webhooku* pro automatickou aktivaci kanálu Jenkins. Při zadání se žádost o přijetí změn, vydá GitHub příspěvek na Jenkinse, aktivovat kanál. Další informace o vytvoření webhooku, najdete v části [Jenkins připojení ke Githubu](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Porovnejte změny v aktuální sdílenou verzi:

    1. Otevřete prohlížeč a přejděte na sdílenou verzi `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT obsahuje adresu URL.

    2. Otevřete jinou kartu a potom zadejte adresu URL žádosti o přijetí změn dev místa. Budou se podobat `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. Zjistíte na odkaz v **sestavení Historie >< sestavení # >> výstup na konzole** pro úlohu Jenkinse. Stránka pro hledání `aksapp`, nebo pokud chcete zobrazit pouze předpony, vyhledejte `azdsprefix`.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Vytváření adresu URL do prostoru dev podřízené

Když soubor žádosti o přijetí změn Jenkins vytvoří prostor dev podřízené podle místa na sdíleném vývojovém týmu a spouští kód z vaší žádosti o přijetí změn v podřazených dev místa. Adresa URL pro vývoj místo podřízené má podobu `http://$env.azdsprefix.<test_endpoint>`. 

**$env.azdsprefix** je nastavený při spuštění kanálu pomocí modulu plug-in Azure Dev mezery podle **devSpacesCreate**:

```Groovy
stage('create dev space') {
    devSpacesCreate aksName: env.AKS_NAME,
        azureCredentialsId: env.AZURE_CRED_ID,
        kubeconfigId: env.KUBE_CONFIG_ID,
        resourceGroupName: env.AKS_RES_GROUP,
        sharedSpaceName: env.PARENT_DEV_SPACE,
        spaceName: devSpaceNamespace
}
```

`test_endpoint` Je adresa URL aplikace webfrontend jste předtím nasadili pomocí `azds up`v [nasazení ukázkové aplikace do clusteru AKS, krok 7](#test_endpoint). Hodnota `$env.TEST_ENDPOINT` je nastavena v konfiguraci kanálu. 

Následující fragment kódu ukazuje způsob použití adresy URL podřízené dev místa v `smoketest` fázi. Kód kontroluje, jestli místo dev podřízené TEST_ENDPOINT je k dispozici a pokud ano, soubory ke stažení pozdrav text do stdout:

```Groovy
stage('smoketest') {
    // CI testing against http://$env.azdsprefix.$env.TEST_ENDPOINT" 
    SLEEP_TIME = 30
    SITE_UP = false
    for (int i = 0; i < 10; i++) {
        sh "sleep ${SLEEP_TIME}"
        code = "0"
        try {
            code = sh returnStdout: true, script: "curl -sL -w '%{http_code}' 'http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting' -o /dev/null"
        } catch (Exception e){
            // ignore
        }
        if (code == "200") {
            sh "curl http://$env.azdsprefix.$env.TEST_ENDPOINT/greeting"
            SITE_UP = true
            break
        }
    }
    if(!SITE_UP) {
        echo "The site has not been up after five minutes"
    }
}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete mít využitím ukázkové aplikace, vyčistěte prostředky Azure tak, že odstraníte skupinu prostředků:

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak používat modul plug-in Azure Dev prostory jenkinse a modulu plug-in Azure Container Registry k sestavení kódu a nasazení do prostoru na vývoj.

Následující seznam prostředků poskytuje další informace o Azure Dev mezery, ACR úkoly a CI/CD pomocí Jenkinse.

Azure Dev mezery:
* [Jak funguje a jak je nakonfigurována služba Azure Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

Úlohy služby ACR:
* [Automatizace oprav operačního systému a architektury pomocí úloh ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)
* [Automatické sestavení na potvrzení změn kódu](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

CI/CD pomocí Jenkinse v Azure:
* [Průběžné nasazování Jenkins](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)
