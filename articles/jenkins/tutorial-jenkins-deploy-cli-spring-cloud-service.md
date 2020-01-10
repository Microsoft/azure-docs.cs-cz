---
title: Nasazení aplikací do jarního cloudu Azure pomocí Jenkinse a rozhraní příkazového řádku Azure
description: Naučte se používat Azure CLI v kanálu průběžné integrace a nasazování k nasazení mikroslužeb do Azure pramenité cloudové služby.
ms.topic: tutorial
ms.date: 01/07/2020
ms.openlocfilehash: 67ad97bb762ed302ef52c404d47c5755ea4b245b
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75734975"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Kurz: nasazení aplikací do jarního cloudu Azure pomocí Jenkinse a rozhraní příkazového řádku Azure

[Azure jaře Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) je plně spravovaný vývoj mikroslužeb s integrovanou správou zjišťování služeb a konfigurací. Služba usnadňuje nasazení mikroslužeb založených na jarních aplikacích do Azure. V tomto kurzu se dozvíte, jak můžete pomocí Azure CLI v Jenkinse automatizovat průběžnou integraci a doručování (CI/CD) pro jarní cloudy Azure.

V tomto kurzu dokončíte tyto úlohy:

> [!div class="checklist"]
> * Zřízení instance služby a spuštění aplikace v jazyce Java pružiny
> * Příprava serveru Jenkinse
> * Použití rozhraní příkazového řádku Azure v kanálu Jenkinse k sestavování a nasazování aplikací mikroslužeb 

Tento kurz předpokládá průběžné znalosti základních služeb Azure, jarního cloudu Azure, [kanálů](https://jenkins.io/doc/book/pipeline/) Jenkinse a modulů plug-in a GitHubu.

## <a name="prerequisites"></a>Požadavky

>[!Note]
> Jarní cloud Azure se teď nabízí jako verze Public Preview. Nabídky veřejné verze Preview umožňují zákazníkům experimentovat s novými funkcemi před jejich oficiální verzí.  Funkce a služby verze Public Preview nejsou určeny pro produkční použití.  Další informace o podpoře v rámci verzí Preview najdete v našich [nejčastějších dotazech](https://azure.microsoft.com/support/faq/) nebo v souboru o [support Request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) , kde se dozvíte víc.

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

* Účet GitHub. Pokud nemáte účet GitHubu, vytvořte si [bezplatný účet](https://github.com/) před tím, než začnete.

* Hlavní server Jenkins. Pokud ještě nemáte hlavní server Jenkinse, nasaďte [jenkinse](https://aka.ms/jenkins-on-azure) v Azure podle kroků v tomto [rychlém](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template)startu. V uzlu nebo agentu Jenkinse jsou vyžadovány následující požadavky (například. Server sestavení):

    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3,0 nebo vyšší](https://maven.apache.org/download.cgi)
    * Rozhraní příkazového [řádku Azure je nainstalované](/cli/azure/install-azure-cli?view=azure-cli-latest), verze 2.0.67 nebo vyšší.

    >[!TIP]
    > Nástroje, jako je git, JDK, AZ CLI a Azure plug-ius, jsou ve výchozím nastavení zahrnuty v šabloně řešení Azure Marketplace [Microsoft jenkinse](https://aka.ms/jenkins-on-azure) .
    
* [Registrace předplatného Azure](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Zřízení instance služby a spuštění aplikace v jazyce Java pružiny

[Metriky Piggy](https://github.com/Azure-Samples/piggymetrics) používáme jako ukázkovou aplikaci služby Microsoft a postupuje se stejným postupem v [rychlém startu: spuštění aplikace v jazyce Java pružiny pomocí rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) pro zřízení instance služby a nastavení aplikací. Pokud jste už prošli stejným procesem, můžete přejít k další části. V opačném případě jsou příkazy rozhraní příkazového řádku Azure CLI zahrnuté v následujících. Další informace najdete [v tématu rychlý Start: spuštění aplikace pružiny v jazyce Java pomocí rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) .

Váš místní počítač musí splňovat stejný požadavek jako server sestavení Jenkinse. Ujistěte se, že jsou nainstalované následující nástroje pro sestavování a nasazování aplikací mikroslužeb:
    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3,0 nebo vyšší](https://maven.apache.org/download.cgi)
    * Rozhraní příkazového [řádku Azure je nainstalované](/cli/azure/install-azure-cli?view=azure-cli-latest), verze 2.0.67 nebo vyšší.

1. Instalace rozšíření pro jarní cloud Azure:

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. Vytvořte skupinu prostředků, která bude obsahovat vaši jarní cloudovou službu Azure:

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. Zřízení instance Azure jaře cloudu:

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. Rozvětvení úložiště [metrik Piggy](https://github.com/Azure-Samples/piggymetrics) na vlastní účet GitHubu. V místním počítači naklonujte úložiště v adresáři s názvem `source-code`:

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. Nastavte konfigurační server. Ujistěte se, že jste nahradili &lt;ID GitHub&gt; se správnou hodnotou.

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. Sestavit projekt:

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. Vytvořte tři mikroslužby: **Brána**, **auth-Service**a **account-Service**:

    ```Azure CLI
        az spring-cloud app create --n gateway -s <service name> -g <resource group name>
        az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
        az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

8. Nasazení aplikací: 

    ```Azure CLI
        az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

9. Přiřadit veřejný koncový bod bráně:

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. Dotaz na aplikaci brány, aby získal adresu URL, abyste mohli ověřit, že je aplikace spuštěná.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    Pokud chcete spustit aplikaci PiggyMetrics, přejděte na adresu URL poskytnutou předchozím příkazem. 

## <a name="prepare-jenkins-server"></a>Příprava serveru Jenkinse

V této části připravíte Server Jenkinse pro spuštění sestavení, což je pro testování jemné. Z důvodu nebezpečnosti byste ale měli použít [agenta virtuálního počítače Azure](https://plugins.jenkins.io/azure-vm-agents) nebo [Azure Container agenta](https://plugins.jenkins.io/azure-container-agents) k aktivaci agenta v Azure za účelem spuštění vašich sestavení. Další informace najdete v článku o Jenkinsu věnovaném [bezpečnostním důsledkům sestavování na hlavním serveru](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

### <a name="install-plug-ins"></a>Nainstalovat moduly plug-in

1. Přihlaste se k serveru Jenkinse. Vyberte **Spravovat jenkinse > spravovat moduly plug-in**.
2. Na kartě **k dispozici** vyberte následující moduly plug-in:
    * [Integrace GitHubu](https://plugins.jenkins.io/github-pullrequest)
    * [Přihlašovací údaje Azure](https://plugins.jenkins.io/azure-credentials)

    Pokud se tyto moduly plug-in nezobrazí v seznamu, podívejte se na kartu **nainstalované** a zjistěte, jestli jsou už nainstalované.

3. Pokud chcete nainstalovat moduly plug-in, vyberte **Stáhnout hned a po restartování nainstalujte**.

4. Dokončete instalaci restartováním serveru Jenkinse.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Přidání přihlašovacích údajů instančního objektu do úložiště přihlašovacích údajů služby Azure Jenkinse

1. K nasazení do Azure potřebujete instanční objekt Azure. Další informace najdete v tématu [Vytvoření instančního objektu služby](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) v kurzu nasazení do Azure App Service. Výstup z `az ad sp create-for-rbac` vypadá nějak takto:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Na řídicím panelu Jenkinse vyberte **Přihlašovací údaje** > **Systém**. Pak vyberte **Global credentials (unrestricted)** (Globální přihlašovací údaje (neomezené)).

3. Vyberte **Přidat přihlašovací údaje**. 

4. Jako typ vyberte **Microsoft Azure instanční objekt** .

5. Zadejte hodnoty pro: * ID předplatného: použijte své ID předplatného Azure * ID klienta: použijte `appId` * klientský klíč klienta: použít `password` * ID tenanta: použijte `tenant` * prostředí Azure: vyberte předem nastavenou hodnotu. Použijte například **Azure** pro Azure pro globální * ID: nastaveno jako **azure_service_principal**. Toto ID použijeme v pozdějším kroku v tomto článku * Popis: je volitelné pole. Tady doporučujeme zadat smysluplnou hodnotu.

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Instalace Maven a AZ CLI jaře-Cloud Extension

Vzorový kanál používá Maven k sestavení a AZ CLI pro nasazení do instance služby. Když se Jenkinse nainstaluje, vytvoří účet správce s názvem *Jenkinse*. Ujistěte se, že uživatel *Jenkinse* má oprávnění ke spuštění rozšíření jarního cloudu.

1. Připojte se k hlavnímu serveru Jenkinse pomocí protokolu SSH. 

2. Nainstalovat Maven

    ```bash
        sudo apt-get install maven 
    ```

3. Nainstalujte Azure CLI. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Azure CLI se instaluje ve výchozím nastavení, pokud používáte [Jenkinse Master v Azure](https://aka.ms/jenkins-on-azure).

4. Přepněte na uživatele `jenkins`:

    ```bash
        sudo su jenkins
    ```

5. Přidejte rozšíření **jarního cloudu** :

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Vytvoření Jenkinsfile
1. Ve svém vlastním úložišti (https://github.com/&lt ; vaše ID GitHubu&gt; /piggymetrics) vytvořte v kořenovém adresáři **Jenkinsfile** .

2. Aktualizujte soubor následujícím způsobem. Ujistěte se, že jste nahradili hodnoty **\<název skupiny prostředků >** a **\<název služby >** . Pokud při přidání přihlašovacích údajů do Jenkinse použijete jinou hodnotu, nahraďte **azure_service_principal** správným ID. 

```groovy
    node {
      stage('init') {
        checkout scm
      }
      stage('build') {
        sh 'mvn clean package'
      }
      stage('deploy') {
        withCredentials([azureServicePrincipal('azure_service_principal')]) {
          // login to Azure
          sh '''
            az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
            az account set -s $AZURE_SUBSCRIPTION_ID
          '''  
          // Set default resource group name and service name. Replace <resource group name> and <service name> with the right values
          sh 'az configure --defaults group=<resource group name>'
          sh 'az configure --defaults spring-cloud=<service name>'
          // Deploy applications
          sh 'az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar'
          sh 'az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar'
          sh 'az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar'
          sh 'az logout'
        }
      }
    }
```

3. Uložte a potvrďte změnu.

## <a name="create-the-job"></a>Vytvoření úlohy

1. Na řídicím panelu Jenkinse klikněte na **Nová položka**.

2. Zadejte název, *Deploy-PiggyMetrics* pro úlohu a vyberte **kanál**. Klikněte na tlačítko OK.

3. Potom klikněte na kartu **Pipeline** (Kanál).

4. V části **Definition** (Definice) vyberte **Pipeline script from SCM** (Skript kanálu z SCM).

5. V části **SCM** vyberte **Git**.

6. Zadejte adresu URL GitHubu pro vaše rozvětvené úložiště: **https://github.com/&lt ; vaše ID githubu&gt; /piggymetrics.Git**

7. Ujistěte se, že **specifikátor větve (černá pro ' Any ')** je * **/Azure**

8. Zachovat **cestu ke skriptům** jako **Jenkinsfile**

7. Klikněte na **Uložit**.

## <a name="validate-and-run-the-job"></a>Ověří a spustí úlohu.

Před spuštěním úlohy budeme aktualizovat text v textovém poli přihlášení, aby se **zadalo přihlašovací ID**.

1. Ve svém vlastním úložišti otevřete `index.html` v **/Gateway/src/Main/Resources/static/**

2. Vyhledejte "zadejte své přihlašovací údaje" a aktualizujte na zadat přihlašovací ID.

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. Potvrdit změny

4. Spusťte úlohu v Jenkinse ručně. Na řídicím panelu Jenkinse klikněte na úlohu *Deploy-PiggyMetrics* a **Sestavte ji nyní**.

Po dokončení úlohy přejděte k veřejné IP adrese aplikace **brány** a ověřte, jestli je vaše aplikace aktualizovaná. 

![Aktualizované metriky Piggy](media/tutorial-jenkins-deploy-cli-spring-cloud/piggymetrics.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku:

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak pomocí rozhraní příkazového řádku Azure v Jenkinse automatizovat průběžnou integraci a doručování (CI/CD) pro Azure jaře Cloud.

Další informace o poskytovateli Azure Jenkinse najdete v tématu Jenkinse na webu Azure.

> [!div class="nextstepaction"]
> [Jenkins v Azure](/azure/jenkins/)
