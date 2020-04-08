---
title: Nasazení aplikací do Azure Spring Cloudu s využitím Jenkinse a Azure CLI
description: Zjistěte, jak používat Azure CLI v průběžné integraci a kanálu nasazení k nasazení mikroslužeb do služby Azure Spring Cloud
ms.topic: tutorial
ms.date: 01/07/2020
ms.openlocfilehash: bc48eecc9e73b95fd9112d645135409c24369e10
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810222"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Kurz: Nasazení aplikací do Azure Spring Cloud pomocí Jenkinse a Azure CLI

[Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-overview) je plně řízený vývoj mikroslužeb s integrovaným zjišťováním a správou konfigurace služeb. Služba usnadňuje nasazení aplikací mikroslužeb založených na jarním spuštění do Azure. Tento kurz ukazuje, jak můžete použít Azure CLI v Jenkinsi k automatizaci průběžné integrace a doručování (CI/CD) pro Azure Spring Cloud.

V tomto kurzu dokončíte tyto úkoly:

> [!div class="checklist"]
> * Zřízení instance služby a spuštění aplikace Java Spring
> * Příprava serveru Jenkins
> * Použití azure cli v kanálu Jenkins k sestavení a nasazení aplikací mikroslužeb 

Tento kurz předpokládá průběžné znalosti základních služeb Azure, Azure Spring Cloud, [Jenkins kanály](https://jenkins.io/doc/book/pipeline/) a moduly plug-in a GitHub.

## <a name="prerequisites"></a>Požadavky

>[!Note]
> Azure Spring Cloud se momentálně nabízí jako veřejná verze Preview. Nabídky ve verzi Public Preview umožňují zákazníkům experimentovat s novými funkcemi před jejich oficiálním vydáním.  Funkce a služby veřejné verze Preview nejsou určeny pro produkční použití.  Další informace o podpoře během náhledů najdete v [nejčastějších dotazech](https://azure.microsoft.com/support/faq/) nebo najdete [žádost o podporu,](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) kde se dozvíte další informace.

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

* Účet GitHub. Pokud nemáte účet GitHub, vytvořte si [bezplatný účet,](https://github.com/) než začnete.

* Hlavní server Jenkins. Pokud ještě nemáte jenkinsový master, nasaďte [Jenkinse](https://aka.ms/jenkins-on-azure) do Azure podle kroků v tomto [rychlém startu](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). Následující jsou požadovány na uzlu Jenkins/agent (například. server sestavení):

    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 nebo vyšší](https://maven.apache.org/download.cgi)
    * [Nainstalovaný Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest), verze 2.0.67 nebo vyšší

    >[!TIP]
    > Nástroje jako Git, JDK, Az CLI a Azure plug-ius jsou ve výchozím nastavení zahrnuty v šabloně řešení Azure Marketplace [Microsoft Jenkins.](https://aka.ms/jenkins-on-azure) 
    
* [Registrace předplatného Azure](https://azure.microsoft.com/free/)
 
## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Zřízení instance služby a spuštění aplikace Java Spring

Metriky [Prasátka](https://github.com/Azure-Samples/piggymetrics) používáme jako ukázkovou aplikaci služby Microsoftu a postupujte podle stejných kroků v [rychlém startu: Spusťte aplikaci Java Spring pomocí rozhraní příkazového příkazu Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) k zřízení instance služby a nastavení aplikací. Pokud jste již prošli stejným procesem, můžete přeskočit na další část. V opačném případě jsou zahrnuty v následujícím jsou příkazy Azure CLI. Odkazovat na [rychlý start: Spuštění aplikace Java Spring pomocí azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) získat další informace o pozadí.

Místní počítač musí splňovat stejné předpoklady jako server sestavení Jenkinse. Ujistěte se, že jsou nainstalovány následující k sestavení a nasazení aplikací mikroslužeb:
    * [Git](https://git-scm.com/)
    * [JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 nebo vyšší](https://maven.apache.org/download.cgi)
    * [Nainstalovaný Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest), verze 2.0.67 nebo vyšší

1. Nainstalujte rozšíření Azure Spring Cloud:

    ```Azure CLI
        az extension add --name spring-cloud
    ```

2. Vytvořte skupinu prostředků, která bude obsahovat vaši službu Azure Spring Cloud:

    ```Azure CLI
        az group create --location eastus --name <resource group name>
    ```

3. Zřízení instance Azure Spring Cloud:

    ```Azure CLI
        az spring-cloud create -n <service name> -g <resource group name>
    ```

4. Vyklopte úložiště [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) na svůj vlastní účet GitHub. V místním počítači klonujte úložiště v `source-code`adresáři s názvem :

    ```bash
        mkdir source-code
        git clone https://github.com/<your GitHub id>/piggymetrics
    ```

5. Nastavte konfigurační server. Ujistěte se, že jste&gt; id GitHubu nahradili &lt;správnou hodnotou.

    ```Azure CLI
        az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

6. Sestavení projektu:

    ```bash
        cd piggymetrics
        mvn clean package -D skipTests
    ```

7. Vytvořte tři mikroslužby: **brána**, **auth-service**a **account-service**:

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

9. Přiřazení veřejného koncového bodu k bráně:

    ```Azure CLI
        az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

10. Dotaz na aplikaci brány získat adresu URL, takže můžete ověřit, že aplikace je spuštěna.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
    Přejděte na adresu URL poskytovanou předchozím příkazem a spusťte aplikaci PiggyMetrics. 

## <a name="prepare-jenkins-server"></a>Příprava serveru Jenkins

V této části připravíte server Jenkins spustit sestavení, což je v pořádku pro testování. Z důvodu důsledků pro zabezpečení byste však měli použít [agenta virtuálního počítače Azure](https://plugins.jenkins.io/azure-vm-agents) nebo [agenta Azure Container](https://plugins.jenkins.io/azure-container-agents) ke spuštění agenta v Azure ke spuštění sestavení. Další informace najdete v článku o Jenkinsu věnovaném [bezpečnostním důsledkům sestavování na hlavním serveru](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

### <a name="install-plug-ins"></a>Instalace modulů plug-in

1. Přihlaste se k serveru Jenkins. Zvolte **Spravovat Jenkins > Spravovat pluginy**.
2. Na kartě **Dostupné** vyberte následující moduly plug-in:
    * [Integrace GitHubu](https://plugins.jenkins.io/github-pullrequest)
    * [Pověření Azure](https://plugins.jenkins.io/azure-credentials)

    Pokud se tyto moduly plug-in v seznamu nezobrazují, zkontrolujte kartu **Nainstalováno** a zjistěte, zda už jsou nainstalované.

3. Chcete-li moduly plug-in nainstalovat, zvolte **Stáhnout a nainstalovat po restartování**programu .

4. Restartujte server Jenkins a dokončete instalaci.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Přidání přihlašovacích údajů primárního objektu služby Azure v úložišti přihlašovacích údajů Jenkinse

1. K nasazení do Azure potřebujete hlavního povinného k poskytování služeb Azure. Další informace najdete v části [Vytvořit instanční objekt](https://docs.microsoft.com/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) v kurzu Nasazení do služby Azure App Service. Výstup z `az ad sp create-for-rbac` vypadá asi takto:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Na řídicím panelu Jenkins vyberte**Systém** **pověření** > . Pak vyberte **Global credentials (unrestricted)** (Globální přihlašovací údaje (neomezené)).

3. Vyberte **Přidat pověření**. 

4. Jako druh vyberte **Microsoft Azure Service Principal.**

5. Zadejte hodnoty pro: * ID předplatného: použijte ID `appId` předplatného Azure * `password` ID klienta: použijte * Tajný klíč klienta: použijte * ID klienta: použijte `tenant` * Prostředí Azure: vyberte přednastavenou hodnotu. Použijte například **Azure** for Azure Global * ID: nastavené jako **azure_service_principal**. Toto ID používáme v pozdějším kroku v tomto článku * Popis: je volitelné pole. Doporučujeme poskytnout smysluplnou hodnotu zde.

### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Instalace rozšíření maven a az CLI spring-cloud

Ukázkový kanál používá Maven k sestavení a Az CLI k nasazení do instance služby. Po instalaci jenkinse se vytvoří účet správce s názvem *Jenkins*. Ujistěte se, že uživatel *Jenkins* má oprávnění ke spuštění rozšíření spring-cloud.

1. Připojte se k jenkinsovu mistrovi přes SSH. 

2. Instalace Maven

    ```bash
        sudo apt-get install maven 
    ```

3. Nainstalujte Azure CLI. Další informace naleznete [v tématu Instalace příkazového příkazového příkazu k webu Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Azure CLI se nainstaluje ve výchozím nastavení, pokud používáte [Jenkins Master v Azure](https://aka.ms/jenkins-on-azure).

4. Přepněte `jenkins` na uživatele:

    ```bash
        sudo su jenkins
    ```

5. Přidejte rozšíření **spring-cloud:**

    ```bash
        az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Vytvoření jenkinsového souboru
1. Ve vlastním úložištihttps://github.com/&lt( ;vaše&gt;GitHub id /prasátko) vytvořte **jenkinsfile** v kořenovém adresáři.

2. Aktualizujte soubor následujícím způsobem. Ujistěte se, že nahradíte hodnoty ** \<>názvu skupiny prostředků** a ** \<>názvů služeb **. Pokud při přidání přihlašovacích údajů v Jenkinsu použijete jinou hodnotu, nahraďte **azure_service_principal** pravým ID. 

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

1. Na řídicím panelu Jenkins klikněte na **Nová položka**.

2. Zadejte název *Deploy-PiggyMetrics* pro úlohu a vyberte **Pipeline**. Klikněte na tlačítko OK.

3. Potom klikněte na kartu **Pipeline** (Kanál).

4. V části **Definition** (Definice) vyberte **Pipeline script from SCM** (Skript kanálu z SCM).

5. V části **SCM** vyberte **Git**.

6. Zadejte adresu URL GitHubu pro rozpůlené úložiště: ** https://github.com/&lt;vaše&gt;id GitHub /piggymetrics.git**

7. Ujistěte **se, že specifikátor větve (černý pro 'any')** je ***/Azure**

8. Zachovat **cestu skriptu** jako **Jenkinsfile**

7. Klikněte na **Uložit.**

## <a name="validate-and-run-the-job"></a>Ověřit a spustit úlohu

Před spuštěním úlohy aktualizujte text ve vstupním poli přihlášení a **zadejte přihlašovací ID**.

1. Ve vlastním repo, `index.html` otevřete v **/gateway/src/main/resources/static/**

2. Vyhledejte výraz "zadejte své přihlašovací údaje" a aktualizujte na "zadejte přihlašovací ID"

    ```HTML
        <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

3. Potvrzení změn

4. Spusťte úlohu v Jenkinsi ručně. Na řídicím panelu Jenkins klikněte na úlohu *Deploy-PiggyMetrics* a potom **na Build Now**.

Po dokončení úlohy přejděte na veřejnou IP adresu aplikace **brány** a ověřte, zda byla aplikace aktualizována. 

![Aktualizované metriky prasátka](media/tutorial-jenkins-deploy-cli-spring-cloud/piggymetrics.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, odstraňte prostředky vytvořené v tomto článku:

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak používat Azure CLI v Jenkinsi k automatizaci průběžné integrace a doručování (CI/CD) pro Azure Spring Cloud.

Další informace o poskytovateli Azure Jenkinse najdete na webu Jenkins e.

> [!div class="nextstepaction"]
> [Jenkins v Azure](/azure/jenkins/)
