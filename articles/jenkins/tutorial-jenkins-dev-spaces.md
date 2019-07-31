---
title: Použití modulu plug-in Azure Dev Spaces pro Jenkinse se službou Azure Kubenetes
description: Naučte se používat modul plug-in Azure Dev Spaces v kanálu průběžné integrace.
author: tomarchermsft
ms.author: tarcher
ms.service: jenkins
ms.topic: tutorial
ms.custom: mvc
ms.date: 07/31/2019
ms.openlocfilehash: 10ff8f4645ee1e7023c96174236243a3b85de938
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679128"
---
<!-- GMinchAQ, 06/18/19 -->

# <a name="tutorial-using-the-azure-dev-spaces-plugin-for-jenkins-with-azure-kubenetes-service"></a>Kurz: Použití modulu plug-in Azure Dev Spaces pro Jenkinse se službou Azure Kubenetes 

Azure Dev Spaces umožňuje testovat a iterativní vývoj aplikace mikroslužeb běžící ve službě Azure Kubernetes Service (AKS) bez nutnosti replikovat nebo napodobovat závislosti. Modul plug-in Azure Dev Spaces pro Jenkinse pomáhá používat vývojové prostory v kanálu průběžné integrace a doručování (CI/CD).

V tomto kurzu se používá také Azure Container Registry (ACR). ACR ukládá image a úloha ACR vytvoří artefakty Docker a Helm. Použití úlohy ACR a ACR pro generování artefaktů odstraňuje nutnost instalace dalšího softwaru, jako je například Docker, na serveru Jenkinse. 

V tomto kurzu dokončíte tyto úlohy:

> [!div class="checklist"]
> * Vytvoření clusteru AKS s povoleným Azure Dev Spaces
> * Nasazení aplikace s více službami na AKS
> * Příprava serveru Jenkinse
> * Použití modulu plug-in Azure Dev Spaces v kanálu Jenkinse k zobrazení náhledu změn kódu před jejich sloučením do projektu

Tento kurz předpokládá průběžné znalosti základních služeb Azure, AKS, ACR, Azure Dev Spaces, [kanálů](https://jenkins.io/doc/book/pipeline/) Jenkinse a modulů plug-in a GitHubu. Základní znalost s podpůrnými nástroji, jako je kubectl a Helm, je užitečná.

## <a name="prerequisites"></a>Požadavky

* Účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Účet GitHub. Pokud nemáte účet GitHubu, vytvořte si [bezplatný účet](https://github.com/) před tím, než začnete.

* [Visual Studio Code](https://code.visualstudio.com/download) s nainstalovanou rozšířením [Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) .

* Rozhraní příkazového [řádku Azure je nainstalované](/cli/azure/install-azure-cli?view=azure-cli-latest), verze 2.0.43 nebo vyšší.

* Hlavní server Jenkins. Pokud ještě nemáte hlavní server Jenkinse, nasaďte [Jenkinse](https://aka.ms/jenkins-on-azure) v Azure podle kroků v tomto rychlém startu [](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Server Jenkinse musí mít nainstalovanou Helm a kubectl a k dispozici pro účet Jenkinse, jak je vysvětleno dále v tomto kurzu.

* VS Code, VS Code terminálu nebo WSL a bash. 


## <a name="create-azure-resources"></a>Vytvoření prostředků Azure

V této části vytvoříte prostředky Azure:

* Skupina prostředků, která bude obsahovat všechny prostředky Azure pro účely tohoto kurzu.
* Cluster [služby Azure Kubernetes](https://docs.microsoft.com/azure/aks/) (AKS).
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) (ACR) pro sestavení (pomocí úloh ACR) a ukládání imagí Docker.

1. Vytvořte skupinu prostředků.

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. Vytvořte cluster AKS. Vytvořte cluster AKS v [oblasti, která podporuje vývojové prostory](../dev-spaces/about.md#supported-regions-and-configurations).

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Nakonfigurujte AKS, aby používaly vývojové prostory.

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Tento krok nainstaluje rozšíření `azds` rozhraní příkazového řádku (CLI).

4. Vytvořte registr kontejnerů.

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Nasazení ukázkových aplikací do clusteru AKS

V této části nastavíte místo pro vývoj a nasadíte ukázkovou aplikaci do clusteru AKS, který jste vytvořili v poslední části. Aplikace se skládá ze dvou částí: *webendu* a *mywebapi*. Obě součásti jsou nasazeny ve vývojovém prostoru. Později v tomto kurzu odešlete žádost o přijetí změn na mywebapi pro aktivaci kanálu CI v Jenkinse.

Další informace o používání Azure Dev Spaces a vývoje více služeb pomocí Azure Dev Spaces najdete v tématu [Začínáme s Azure dev Spaces s Java](https://docs.microsoft.com/azure/dev-spaces/get-started-java)a [vývoj více služeb s](https://docs.microsoft.com/azure/dev-spaces/multi-service-java)využitím Azure dev Spaces. Tyto kurzy poskytují další informace, které tady nejsou uvedené.

1. https://github.com/Azure/dev-spaces Stáhněte úložiště z GitHubu.

2. `samples/java/getting-started/webfrontend` Otevřete složku v vs Code. (Případné výchozí výzvy ohledně přidání prostředků ladění nebo obnovení projektu můžete ignorovat.)

3. Aktualizujte `/src/main/java/com/ms/sample/webfrontend/Application.java` tak, aby vypadaly takto:

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

4. Kliknutím na **Zobrazit** a **terminál** otevřete integrovaný terminál v vs Code.

5. `azds prep` Spusťte příkaz pro přípravu aplikace tak, aby běžela ve vývojovém prostoru. Tento příkaz musí být spuštěn z `dev-spaces/samples/java/getting-started/webfrontend` , aby bylo možné správně připravit aplikaci:

    ```bash
    azds prep --public
    ```

    `azds prep` Příkaz rozhraní příkazového řádku pro vývojáře generuje Docker a Kubernetes prostředky s výchozími nastaveními. Tyto soubory jsou uchovány po dobu života projektu a lze je přizpůsobit:

    * `./Dockerfile`a `./Dockerfile.develop` popište image kontejneru aplikace a způsob sestavení a spuštění zdrojového kódu v rámci kontejneru.
    * [Helm chart](https://helm.sh/docs/developing_charts/) v `./charts/webfrontend` popisuje, jak do Kubernetes nasadit kontejner.
    * `./azds.yaml`je konfigurační soubor Azure Dev Spaces.

    Další informace najdete v tématu [jak Azure dev Spaces fungují a jsou nakonfigurované](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Sestavte a spusťte aplikaci v AKS pomocí `azds up` příkazu:

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Vyhledejte v výstupu konzoly informace o adrese URL, která byla vytvořena `up` příkazem. Bude v tomto tvaru:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Otevřete tuto adresu URL v okně prohlížeče a měli byste vidět webovou aplikaci. Během spouštění kontejneru se výstup `stdout` a `stderr` streamuje do okna terminálu.

8. V dalším kroku nastavte a nasaďte *mywebapi*:

    1. Změnit adresář na`dev-spaces/samples/java/getting-started/mywebapi`

    2. Spusťte

        ```bash
        azds prep
        ```

    3. Spusťte

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Příprava serveru Jenkinse

V této části připravíte Server Jenkinse pro spuštění ukázkového kanálu CI.

* Nainstalovat moduly plug-in
* Instalace rozhraní příkazového řádku Helm a Kubernetes
* Přidat přihlašovací údaje

### <a name="install-plug-ins"></a>Nainstalovat moduly plug-in

1. Přihlaste se k serveru Jenkinse. Vyberte **Spravovat jenkinse > spravovat moduly plug-in**.
2. Na kartě **k dispozici** vyberte následující moduly plug-in:
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Azure Container Registry úlohy](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Prostředí pro vložení prostředí](https://plugins.jenkins.io/envinject)
    * [Integrace GitHubu](https://plugins.jenkins.io/github-pullrequest)

    Pokud se tyto moduly plug-in nezobrazí v seznamu, podívejte se na kartu **nainstalované** a zjistěte, jestli jsou už nainstalované.

3. Pokud chcete nainstalovat moduly plug-in, vyberte **Stáhnout hned a po restartování nainstalujte**.

4. Dokončete instalaci restartováním serveru Jenkinse.

### <a name="install-helm-and-kubectl"></a>Instalace Helm a kubectl

Vzorový kanál používá Helm a kubectl k nasazení do vývojového prostoru. Když se Jenkinse nainstaluje, vytvoří účet správce s názvem *Jenkinse*. Helm i kubectl musí být přístupné pro uživatele Jenkinse.

1. Vytvořte připojení SSH k hlavnímu serveru Jenkinse. 

2. Přepněte na `jenkins` uživatele:
    ```bash
    sudo su jenkins
    ```

3. Nainstalujte rozhraní příkazového řádku Helm. Další informace najdete v tématu [instalace Helm](https://helm.sh/docs/using_helm/#installing-helm).

4. Nainstalujte kubectl. Další informace najdete v tématu [**AZ ACS Kubernetes Install-CLI**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Přidání přihlašovacích údajů do Jenkinse

1. Jenkinse potřebuje instanční objekt Azure pro ověřování a přístup k prostředkům Azure. Pokud chcete vytvořit instanční objekt, přečtěte si část [Vytvoření instančního objektu](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) v kurzu nasazení do Azure App Service. Nezapomeňte uložit kopii výstupu z `create-for-rbac` , protože potřebujete tyto informace k dokončení dalšího kroku. Výstup bude vypadat přibližně takto:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Do Jenkinse přidejte typ přihlašovacích údajů *instančního objektu služby* , a Microsoft Azure to pomocí informací o instančním objektu z předchozího kroku. Názvy na snímku obrazovky níže odpovídají výstupu z `create-for-rbac`.

    Pole **ID** je název přihlašovacího údaje Jenkinse pro instanční objekt. V příkladu se používá hodnota `displayName` (v této `xxxxxxxjenkinssp`instanci), ale můžete použít libovolný požadovaný text. Tento název přihlašovacích údajů je hodnota proměnné prostředí AZURE_CRED_ID v další části.

    ![Přidání přihlašovacích údajů instančního objektu do Jenkinse](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    **Popis** je volitelný. Podrobnější pokyny najdete v tématu věnovaném [Přidání instančního objektu do Jenkinse](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) v kurzu nasazení do Azure App Service. 



3. Pokud chcete zobrazit přihlašovací údaje pro ACR, spusťte tento příkaz:

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    Vytvořte kopii výstupu JSON, která by měla vypadat přibližně takto:

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

4. Do Jenkinse přidejte *uživatelské jméno s* přihlašovacími údaji k heslu. **Uživatelské jméno** je uživatelské jméno z posledního kroku v tomto příkladu `acr01`. **Heslo** je hodnota pro první heslo, v tomto příkladu `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. **ID** tohoto přihlašovacího údaje je hodnota ACR_CRED_ID.

5. Nastavte přihlašovací údaje AKS. Přidejte do Jenkinse typ přihlašovacích údajů *Konfigurace Kubernetes (kubeconfig)* (použijte možnost zadat přímo). Přihlašovací údaje pro přístup ke clusteru AKS získáte spuštěním následujícího příkazu:

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   **ID** Toto pověření je hodnota KUBE_CONFIG_ID v další části.

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Scénář vybraný pro příklad kanálu je založený na vzoru reálného světa: Žádost o přijetí změn aktivuje kanál CI, který sestaví a následně nasadí navrhované změny do vývojového prostoru Azure pro účely testování a kontroly. V závislosti na výsledku recenze se změny buď sloučí, a nasadí, aby se AKS nebo zahodily. Nakonec se odebere místo pro vývoj.

Konfigurace kanálu Jenkinse a Jenkinsfile definují fáze v kanálu CI. Tento vývojový diagram zobrazuje fáze zřetězení a body rozhodování definované Jenkinsfileem:

![Tok kanálu Jenkinse](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Stáhněte si upravenou verzi projektu *mywebapi* z https://github.com/azure-devops/mywebapi. Tento projekt obsahuje několik souborů potřebných k vytvoření kanálu, včetně grafu *Jenkinsfile*, *fázemi*a Helm.

2. Přihlaste se k Jenkinse. V nabídce na levé straně vyberte **Přidat položku**.

3. Vyberte **kanál**a potom zadejte název do pole **Zadejte název položky** . Vyberte **OK**a obrazovka konfigurace kanálu se automaticky otevře.

4. Na kartě **Obecné** ověřte, že **je pro spuštění připraveno prostředí**. 

5. **Zachovejte proměnné prostředí Jenkinse** a **Udržujte proměnné sestavení Jenkinse**.

6. Do pole **vlastnosti Content (obsah** ) zadejte následující proměnné prostředí:

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

    Pomocí ukázkových hodnot uvedených v předchozích částech by měl seznam proměnných prostředí vypadat přibližně takto:

    ![Proměnné prostředí Jenkinse kanálu](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. V **> definice kanálu**vyberte **skript kanálu z SCM** .
8. V **SCM**zvolte **Git** a pak zadejte adresu URL úložiště.
9. Do **specifikátoru větve**zadejte `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Vyplňte adresu URL úložiště SCM a cestu ke skriptu "Jenkinsfile".
11. Měla by být zaškrtnuta **odlehčená rezervace** .

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Vytvoření žádosti o přijetí změn pro aktivaci kanálu

K dokončení kroku 3 v této části budete muset komentovat část Jenkinsfile, jinak se zobrazí chyba 404, když se pokusíte zobrazit nové a staré verze vedle sebe. Ve výchozím nastavení se při zvolení sloučení žádosti o přijetí změn odebere předchozí sdílená verze mywebapi a nahradí se novou verzí. Před dokončením kroku 1 proveďte následující změnu v Jenkinsfile:

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

1. Proveďte změnu na `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`a pak vytvořte žádost o získání dat. Příklad:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Přihlaste se k Jenkinse a vyberte název kanálu a pak zvolte **sestavit hned**. 

    Můžete také nastavit Webhook, který automaticky aktivuje kanál Jenkinse. Po zadání žádosti o přijetí změn vydá GitHub příspěvek na Jenkinse, který aktivuje kanál. Další informace o nastavení Webhooku najdete v tématu [připojení Jenkinse k GitHubu](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Porovnat změny s aktuální sdílenou verzí:

    1. Otevřete prohlížeč a přejděte na sdílenou verzi `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. TEST_ENDPOINT obsahuje adresu URL.

    2. Otevřete jinou kartu a potom zadejte adresu URL vývojového prostoru žádosti o přijetí změn. Bude vypadat podobně jako `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. Odkaz najdete v **historii sestavení > < Build # > >** pro úlohu Jenkinse. Vyhledejte na stránce `aksapp`, nebo pokud chcete zobrazit pouze předponu, `azdsprefix`vyhledejte.

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Sestavení adresy URL podřízeného vývojového prostoru

Při zaznamenání žádosti o přijetí změn vytvoří Jenkinse podřízený prostor pro vývoj na základě sdíleného vývojového prostoru týmu a spustí kód z vaší žádosti o přijetí změn v podřízeném prostoru pro vývoj. Adresa URL podřízeného vývojového prostoru má formu `http://$env.azdsprefix.<test_endpoint>`. 

**$env. azdsprefix** se nastavuje během provádění kanálu modulem plug-in Azure dev Spaces podle **devSpacesCreate**:

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

Je adresa URL aplikace webendu, kterou jste předtím nasadili `azds up`pomocí nástroje v části [nasazení ukázkových aplikací do clusteru AKS, krok 7.](#test_endpoint) `test_endpoint` Hodnota `$env.TEST_ENDPOINT` je nastavena v konfiguraci kanálu. 

Následující fragment kódu ukazuje, jak se v této `smoketest` fázi používá adresa URL místa pro vývoj v podřízeném objektu. Kód zkontroluje, zda je k dispozici podřízený vývojové místo TEST_ENDPOINT, a pokud ano, stáhne text pozdravu do stdout:

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

Až budete s použitím ukázkové aplikace hotovi, vyčistěte prostředky Azure odstraněním skupiny prostředků:

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak používat modul plug-in Azure Dev Spaces pro Jenkinse a modul plug-in Azure Container Registry k vytváření kódu a nasazování do vývojového prostoru.

Následující seznam prostředků poskytuje další informace o Azure Dev Spaces, úlohách ACR a CI/CD s Jenkinse.

Azure Dev Spaces:
* [Jak funguje a jak je nakonfigurována služba Azure Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works)

ACR úlohy:
* [Automatizace oprav operačního systému a architektury pomocí úloh ACR](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)
* [Automatické sestavení při potvrzení kódu](https://docs.microsoft.com/azure/container-registry/container-registry-tasks-overview)

CI/CD s Jenkinse v Azure:
* [Průběžné nasazování Jenkinse](https://docs.microsoft.com/azure/aks/jenkins-continuous-deployment)
