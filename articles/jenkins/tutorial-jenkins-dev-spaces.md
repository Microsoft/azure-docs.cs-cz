---
title: Použití modulu plug-in Azure Dev Spaces pro Jenkinse se službou Azure Kubernetes
description: Zjistěte, jak používat modul plug-in Azure Dev Spaces v kanálu průběžné integrace.
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 281565cec5ee947781ab8ee9f62a00e01f9ababb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79037027"
---
# <a name="tutorial-using-the-azure-dev-spaces-plug-in-for-jenkins-with-azure-kubernetes-service"></a>Kurz: Použití modulu plug-in Azure Dev Spaces pro Jenkinse se službou Azure Kubernetes 

Azure Dev Spaces umožňuje testovat a iterativně vyvíjet aplikaci mikroslužeb spuštěnou ve službě Azure Kubernetes Service (AKS) bez nutnosti replikovat nebo zesměšňovat závislosti. Modul plug-in Azure Dev Spaces pro Jenkins vám pomůže používat dev spaces v kanálu průběžné integrace a doručování (CI/CD).

Tento kurz také používá Azure Container Registry (ACR). ACR ukládá image a úloha ACR vytváří artefakty Dockeru a Helmu. Použití Úlohy ACR a ACR pro generování artefaktů odstraňuje potřebu instalace dalšího softwaru, jako je docker, na server Jenkinse. 

V tomto kurzu dokončíte tyto úkoly:

> [!div class="checklist"]
> * Vytvoření clusteru AKS s povoleným Azure Dev Spaces
> * Nasazení víceúčelové aplikace do Služby AKS
> * Příprava serveru Jenkins
> * Použití modulu plug-in Azure Dev Spaces v kanálu Jenkinse k zobrazení náhledu změn kódu před jejich sloučením do projektu

Tento kurz předpokládá průběžné znalosti základních služeb Azure, AKS, ACR, Azure Dev Spaces, [Jenkins kanály](https://jenkins.io/doc/book/pipeline/) a moduly plug-in a GitHub. Základní znalost podpůrných nástrojů, jako je kubectl a Helm, je užitečná.

## <a name="prerequisites"></a>Požadavky

* Účet Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

* Účet GitHub. Pokud nemáte účet GitHub, vytvořte si [bezplatný účet,](https://github.com/) než začnete.

* [Visual Studio Code](https://code.visualstudio.com/download) s nainstalovanou rozšíření [Azure Dev Spaces.](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)

* [Azure CLI nainstalován](/cli/azure/install-azure-cli?view=azure-cli-latest), verze 2.0.43 nebo vyšší.

* Hlavní server Jenkins. Pokud ještě nemáte jenkinsový master, nasaďte [Jenkinse](https://aka.ms/jenkins-on-azure) do Azure podle kroků v tomto [rychlém startu](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Server Jenkins musí mít helm a kubectl nainstalován a k dispozici pro účet Jenkins, jak je vysvětleno dále v tomto kurzu.

* VS Kód, Terminál kódu VS nebo WSL a Bash. 


## <a name="create-azure-resources"></a>Vytvoření prostředků Azure

V této části vytvoříte prostředky Azure:

* Skupina prostředků obsahující všechny prostředky Azure pro tento kurz.
* Cluster [služby Azure Kubernetes](https://docs.microsoft.com/azure/aks/) (AKS).
* [Registr kontejnerů Azure](https://docs.microsoft.com/azure/container-registry/) (ACR) k sestavení (pomocí úloh ACR) a ukládání ibi Dockeru.

1. Vytvořte skupinu prostředků.

    ```azurecli
    az group create --name MyResourceGroup --location westus2
    ```

2. Vytvořte cluster AKS. Vytvořte cluster AKS v [oblasti, která podporuje dev spaces](../dev-spaces/about.md#supported-regions-and-configurations).

    ```azurecli
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Nakonfigurujte Službu AKS tak, aby používala funkce Dev Spaces.

    ```azurecli
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```
    Tento krok nainstaluje rozšíření `azds` CLI.

4. Vytvořte registr kontejnerů.

    ```azurecli
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## <a name="deploy-sample-apps-to-the-aks-cluster"></a>Nasazení ukázkových aplikací do clusteru AKS

V této části nastavíte dev prostor a nasadíte ukázkovou aplikaci do clusteru AKS, který jste vytvořili v poslední části. Aplikace se skládá ze dvou částí, *webfrontend* a *mywebapi*. Obě součásti jsou nasazeny v prostoru pro dev. Později v tomto kurzu odešlete žádost o přijetí vyhozavky proti mywebapi k aktivaci kanálu CI v Jenkinsi.

Další informace o používání Azure Dev Spaces a vývoji více služeb s Azure Dev Spaces najdete [v tématu Začínáme v Azure Dev Spaces s Javou](https://docs.microsoft.com/azure/dev-spaces/get-started-java)a [vývoj více služeb s Azure Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/multi-service-java). Tyto kurzy poskytují další základní informace, které zde nejsou zahrnuty.

1. Stáhněte https://github.com/Azure/dev-spaces si úložiště z GitHubu.

2. Otevřete `samples/java/getting-started/webfrontend` složku v kódu VS. (Případné výchozí výzvy ohledně přidání prostředků ladění nebo obnovení projektu můžete ignorovat.)

3. Aktualizace `/src/main/java/com/ms/sample/webfrontend/Application.java` vypadat takto:

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

4. Kliknutím na **Zobrazit** **terminál** otevřete integrovaný terminál v kódu VS.

5. Spusťte `azds prep` příkaz a připravte aplikaci na spuštění v prostoru pro vývoj. Tento příkaz musí `dev-spaces/samples/java/getting-started/webfrontend` být spuštěn z připravit aplikaci správně:

    ```bash
    azds prep --public
    ```

    `azds prep` Příkaz Příkazcli pro dev spaces generuje datové zdroje Dockeru a Kubernetes s výchozím nastavením. Tyto soubory přetrvávají po celou dobu životnosti projektu a lze je přizpůsobit:

    * `./Dockerfile`a `./Dockerfile.develop` popište image kontejneru aplikace a jak je zdrojový kód sestaven a spuštěn v kontejneru.
    * [Helm chart](https://helm.sh/docs/topics/charts/) v `./charts/webfrontend` popisuje, jak do Kubernetes nasadit kontejner.
    * `./azds.yaml`je konfigurační soubor Azure Dev Spaces.

    Další informace najdete v tématu [Jak Azure Dev Spaces funguje a je nakonfigurovaný](https://docs.microsoft.com/azure/dev-spaces/how-dev-spaces-works).

6. Sestavení a spuštění aplikace v AKS pomocí příkazu: `azds up`

    ```bash
    azds up
    ```
    <a name="test_endpoint"></a>Naskenujte výstup konzoly a naleznete informace `up` o adrese URL, která byla vytvořena příkazem. Bude v tomto tvaru:

    ```bash
    (pending registration) Service 'webfrontend' port 'http' will be available at '<url>'
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```
    Otevřete tuto adresu URL v okně prohlížeče a měli byste vidět webovou aplikaci. Během spouštění kontejneru se výstup `stdout` a `stderr` streamuje do okna terminálu.

8. Dále nastavte a nasaďte *mywebapi*:

    1. Změnit adresář na`dev-spaces/samples/java/getting-started/mywebapi`

    2. Spusťte

        ```bash
        azds prep
        ```

    3. Spusťte

        ```bash
        azds up -d
        ```

## <a name="prepare-jenkins-server"></a>Příprava serveru Jenkins

V této části připravíte server Jenkins pro spuštění ukázkového kanálu CI.

* Instalace modulů plug-in
* Instalace cli helmu a Kubernetes
* Přidání přihlašovacích údajů

### <a name="install-plug-ins"></a>Instalace modulů plug-in

1. Přihlaste se k serveru Jenkins. Zvolte **Spravovat Jenkins > Spravovat pluginy**.
2. Na kartě **Dostupné** vyberte následující moduly plug-in:
    * [Azure Dev Spaces](https://plugins.jenkins.io/azure-dev-spaces)
    * [Úlohy Azure Container Registry](https://plugins.jenkins.io/azure-container-registry-tasks)
    * [Vstřikovač prostředí](https://plugins.jenkins.io/envinject)
    * [Integrace GitHubu](https://plugins.jenkins.io/github-pullrequest)

    Pokud se tyto moduly plug-in v seznamu nezobrazují, zkontrolujte kartu **Nainstalováno** a zjistěte, zda už jsou nainstalované.

3. Chcete-li moduly plug-in nainstalovat, zvolte **Stáhnout a nainstalovat po restartování**programu .

4. Restartujte server Jenkins a dokončete instalaci.

### <a name="install-helm-and-kubectl"></a>Instalace Helm a kubectl

Ukázkový kanál používá Helm a kubectl k nasazení do dev prostoru. Po instalaci jenkinse se vytvoří účet správce s názvem *Jenkins*. Helm i kubectl musí být přístupné uživateli Jenkinse.

1. Navažte s Jenkinsovým mistrem SSH. 

2. Přepněte `jenkins` na uživatele:
    ```bash
    sudo su jenkins
    ```

3. Nainstalujte cli helmu. Další informace naleznete v [tématu Instalace helmu](https://helm.sh/docs/using_helm/#installing-helm).

4. Nainstalujte kubectl. Další informace naleznete [**v tématu az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### <a name="add-credentials-to-jenkins"></a>Přidání přihlašovacích údajů do Jenkinse

1. Jenkins potřebuje instanční objekt Azure pro ověřování a přístup k prostředkům Azure. Chcete-li vytvořit instanční objekt, naleznete v části [Vytvořit instanční objekt](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) v kurzu Nasazení do služby Azure App Service. Nezapomeňte uložit kopii výstupu z, `create-for-rbac` protože potřebujete, aby tyto informace k dokončení dalšího kroku. Výstup bude vypadat zhruba takto:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Přidejte typ pověření *Microsoft Azure Service Principal* v Jenkinsi pomocí informací o instančním objektu z předchozího kroku. Názvy na následujícím snímku obrazovky `create-for-rbac`odpovídají výstupu z .

    Pole **ID** je název pověření Jenkinse pro váš instanční objekt. Příklad používá hodnotu `displayName` (v této `xxxxxxxjenkinssp`instanci ), ale můžete použít libovolný text, který chcete. Tento název pověření je hodnota pro proměnnou prostředí AZURE_CRED_ID v další části.

    ![Přidání pověření hlavního povinného servisu do Jenkinse](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

    **Popis** je volitelný. Podrobnější pokyny najdete v [tématu Přidání instančního objektu do Jenkinse](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) v kurzu Nasazení do služby Azure App Service. 



3. Chcete-li zobrazit pověření ACR, spusťte tento příkaz:

    ```azurecli
    az acr credential show -n <yourRegistryName>
    ```

    Vytvořte kopii výstupu JSON, který by měl vypadat nějak takto:

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

4. Přidejte *uživatelské jméno s* typem pověření hesla v Jenkinsi. **Uživatelské jméno** je uživatelské jméno z posledního `acr01`kroku v tomto příkladu . **Heslo** je hodnota prvního hesla v `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`tomto příkladu . **ID** tohoto pověření je hodnota ACR_CRED_ID.

5. Nastavte pověření AKS. Přidejte typ pověření *konfigurace Kubernetes (kubeconfig)* v Jenkinsi (použijte možnost "Enter directly"). Chcete-li získat přístupová pověření pro cluster AKS, spusťte následující příkaz:

    ```azurecli
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   **ID** toto pověření je hodnota KUBE_CONFIG_ID v další části.

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Scénář vybraný pro ukázkový kanál je založen na skutečném vzoru: Požadavek na přijetí změn aktivuje kanál CI, který vytvoří a pak nasadí navrhované změny do prostoru Azure pro vývoj pro testování a kontrolu. V závislosti na výsledku kontroly jsou změny sloučeny a nasazeny do AKS nebo zahozeny. Nakonec je dev prostor odebrán.

Konfigurace kanálu Jenkins a Jenkinsfile definovat fáze v kanálu CI. Tento vývojový diagram znázorňuje fáze kanálu a rozhodovací body definované jenkinsovým souborem:

![Tok potrubí Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

1. Stáhněte si upravenou verzi projektu [https://github.com/azure-devops/mywebapi](https://github.com/azure-devops/mywebapi) *mywebapi* z aplikace . Tento projekt obsahuje několik souborů potřebných k vytvoření kanálu, včetně *Jenkinsfile*, *Dockerfiles*a Helm grafu.

2. Přihlaste se k Jenkinsovi. V nabídce vlevo vyberte **Přidat položku**.

3. Vyberte **Kanál**a zadejte název do pole **Zadejte název položky.** Vyberte **OK**a pak se automaticky otevře obrazovka konfigurace kanálu.

4. Na kartě **Obecné** **zaškrtněte políčko Připravit prostředí pro spuštění**. 

5. Zkontrolujte **zachovat proměnné prostředí Jenkins** a zachovat proměnné sestavení **jenkinse**.

6. Do pole **Obsah vlastností** zadejte následující proměnné prostředí:

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

    ![Proměnné prostředí kanálu Jenkins](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

7. Zvolte **Skript kanálu z scm** v **definici > kanálu**.
8. V **SCM**zvolte **Git** a zadejte adresu URL úložiště.
9. Do **pole Specifikátor větví**zadejte `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
10. Vyplňte adresu URL pro repo scm a cestu skriptu "Jenkinsfile".
11. **Zjednodušená pokladna** by měla být zkontrolována.

## <a name="create-a-pull-request-to-trigger-the-pipeline"></a>Vytvoření žádosti o přijetí žádosti o přijetí k opětovnému stažení k aktivaci kanálu

Chcete-li dokončit krok 3 v této části, budete muset komentovat část Jenkinsfile, jinak se zobrazí chyba 404 při pokusu o zobrazení nové a staré verze vedle sebe. Ve výchozím nastavení, pokud se rozhodnete sloučit PR, předchozí sdílená verze mywebapi budou odebrány a nahrazeny novou verzí. Před dokončením kroku 1 proveďte v Jenkinsově souboru následující změnu:

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

1. Proveďte změnu `mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java`a vytvořte žádost o přijetí změn. Například:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. Přihlaste se k Jenkinsovi a vyberte název kanálu a pak zvolte **Build Now**. 

    Můžete také nastavit *webhooku* pro automatické spuštění kanálu Jenkins. Po zadání žádosti o přijetí práv github vydá službu POST Jenkinsovi, čímž aktivuje kanál. Další informace o nastavení webového háčku najdete v [tématu Připojení Jenkinse k GitHubu](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Porovnejte změny s aktuální sdílenou verzí:

    1. Otevřete prohlížeč a přejděte `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`na sdílenou verzi . TEST_ENDPOINT obsahuje adresu URL.

    2. Otevřete další kartu a zadejte adresu URL místa pro dev. Bude to podobné `https://<yourdevspacename>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`jako . Odkaz najdete v **historii sestavení > <sestavení#> > konzolový výstup** pro úlohu Jenkinse. Vyhledejte na `aksapp`stránce , nebo chcete-li `azdsprefix`zobrazit pouze předponu, vyhledejte .

 

### <a name="constructing-the-url-to-the-child-dev-space"></a>Vytvoření adresy URL pro podřízený dev prostor

Když podáte žádost o přijetí informací, Jenkins vytvoří podřízený dev prostor na základě sdíleného dev prostoru týmu a spustí kód z vašeho žádosti o přijetí v tomto podřízeném vývojovém prostoru. Adresa URL podřízeného prostoru pro `http://$env.azdsprefix.<test_endpoint>`vývoj má formu . 

**$env.azdsprefix** je nastavenběhem spuštění kanálu pomocí modulu plug-in Azure Dev Spaces by **devSpacesCreate**:

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

Jedná `test_endpoint` se o adresu URL aplikace webfrontend, kterou jste dříve nasadili `azds up`v [aplikaci Deploy sample apps to the AKS cluster, step 7](#test_endpoint). Hodnota `$env.TEST_ENDPOINT` je nastavena v konfiguraci kanálu. 

Následující fragment kódu ukazuje, jak se ve `smoketest` fázi používá podřízená adresa URL dev prostoru. Kód zkontroluje, zda je k dispozici podřízený TEST_ENDPOINT prostoru, a pokud ano, stáhne text s pozdravem do stdout:

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

Po dokončení používání ukázkové aplikace vyčistěte prostředky Azure odstraněním skupiny prostředků:

```azurecli
az group delete -y --no-wait -n MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [CI/CD s Jenkinsem v Azure](jenkins-continuous-deployment.md)
