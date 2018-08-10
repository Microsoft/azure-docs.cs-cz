---
title: Nasazení do Azure Kubernetes Service (AKS) pomocí Jenkinse a vzor modrozelené nasazení
description: Zjistěte, jak nasadit do Azure Kubernetes Service (AKS) pomocí Jenkinse a vzor modrozelené nasazení.
services: app-service\web
documentationcenter: ''
author: tomarcher
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 07/23/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 384681ae0ba212b485022ac81743528f96075ec8
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716453"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Nasazení do Azure Kubernetes Service (AKS) pomocí Jenkinse a vzor modrozelené nasazení

Azure Kubernetes Service (AKS) spravuje hostované prostředí Kubernetes, tak rychle a snadno nasazovat a spravovat kontejnerizované aplikace. Není nutné znalosti týkající se Orchestrace kontejnerů. AKS také odstraní starosti související s probíhajícími operacemi a údržbou, zřizování, upgradování a škálování prostředků na vyžádání. Není nutné pozvedněte své aplikace do offline režimu. Další informace o službě AKS najdete v tématu [dokumentaci ke službě AKS](/azure/aks/).

Modrozelené nasazení je vzorek Azure DevOps, průběžné doručování, ponechává stávající (modrou) verzi za provozu, nasadí novou (zelená). Obvykle tento model využívá ke směrování zvýšení provozu do zeleného nasazení služby Vyrovnávání zatížení. Pokud monitorování zjistí incident, je možné přesměrovat provoz na modrého nasazení, který je stále spuštěn. Další informace o průběžné doručování, naleznete v tématu [co je průběžné doručování](/azure/devops/what-is-continuous-delivery).

V tomto kurzu se dozvíte, jak provádět následující úlohy:

> [!div class="checklist"]
> * Další informace o vzoru modrozelené nasazení
> * Vytvoření spravovaného clusteru Kubernetes
> * Spustit ukázkový skript pro konfiguraci clusteru Kubernetes
> * Ruční konfigurace clusteru Kubernetes
> * Vytvoření a spuštění úlohy Jenkinse

## <a name="prerequisites"></a>Požadavky
- [Účet GitHub](https://github.com) : je nutné si účet GitHub a naklonujte ukázkové úložiště.
- [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) : použití rozhraní příkazového řádku Azure CLI 2.0 k vytvoření clusteru Kubernetes.
- [Chocolatey](https://chocolatey.org): Správce balíčků použijete k instalaci kubectl.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): rozhraní příkazového řádku můžete použít pro spuštění příkazů pro clustery Kubernetes.
- [jq](https://stedolan.github.io/jq/download/): jednoduchý a příkazového řádku procesor JSON.

## <a name="clone-the-sample-app-from-github"></a>Naklonujte ukázkovou aplikaci z Githubu

V úložišti Microsoft na webu GitHub najdete ukázkovou aplikaci, která ukazuje, jak pomocí Jenkinse a vzor modrozelené nasazení do AKS. V této části vytvořit fork tohoto úložiště ve vaší Githubu a naklonujte aplikaci do místního systému.

1. Přejděte do úložiště GitHub pro [todo-app-java-typu azure](https://github.com/microsoft/todo-app-java-on-azure.git) ukázkovou aplikaci.

    ![Snímek obrazovky s ukázkovou aplikaci v úložišti GitHub Microsoftu](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Vytvořit fork úložiště tak, že vyberete **Forku** v pravém horním rohu stránky a postupujte podle pokynů a vytvořit fork úložiště ve vašem účtu GitHub.

    ![Možnost snímek obrazovky z Githubu do forku](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Poté, co můžete vytvořit fork úložiště, uvidíte, že název účtu se změní na název svého účtu a označuje poznámku, od kdy byl Rozvětvená úložiště (Microsoft).

    ![Název účtu GitHub snímek obrazovky a Poznámka](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Vyberte **klonovat nebo stáhnout**.

    ![Snímek obrazovky z Githubu možnost klonovat nebo stáhnout úložiště](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. V **klonování pomocí protokolu HTTPS** okna, vyberte **kopírování** ikonu.

    ![Snímek obrazovky z Githubu možnost zkopírovat adresu URL klonování do schránky.](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Otevřete terminál nebo okno Git Bash.

1. Přejděte do požadovaného umístění, kam chcete uložit místní kopii (klonovat) úložiště.

1. Použití `git clone` příkazu, naklonujte adresu URL, které jste zkopírovali dříve.

    ![Snímek obrazovky z Git Bashe příkaz git clone](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Stiskněte klávesu Enter pro spuštění procesu klonování.

    ![Snímek obrazovky z Git Bashe výsledky příkazu klonu gitu](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Přejděte do nově vytvořeného adresáře, který obsahuje klonování zdroje aplikace.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Vytvoření a konfigurace spravovaného clusteru Kubernetes

V této části provedete následující kroky:

- Pomocí rozhraní příkazového řádku Azure CLI 2.0 k vytvoření spravovaného clusteru Kubernetes.
- Zjistěte, jak vytvořit cluster, buď pomocí instalačního skriptu, nebo ručně.
- Vytvoření instance služby Azure Container Registry.

> [!NOTE]   
> AKS je aktuálně ve verzi preview. Informace o povolení verze preview pro vaše předplatné Azure, najdete v části [rychlý start: nasazení clusteru Azure Kubernetes Service (AKS)](/azure/aks/kubernetes-walkthrough#enabling-aks-preview-for-your-azure-subscription).

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Použití rozhraní příkazového řádku Azure CLI 2.0 k vytvoření spravovaného clusteru Kubernetes
Chcete-li vytvořit spravovaný cluster Kubernetes pomocí [příkazového řádku Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), ujistěte se, že používáte Azure CLI verze 2.0.25 nebo novější.

1. Přihlaste se ke svému účtu Azure. Jakmile zadáte následující příkaz, obdržíte pokyny, které vysvětlují, jak dokončit přihlášení. 
    
    ```bash
    az login
    ```

1. Při spuštění `az login` příkaz v předchozím kroku, zobrazí se seznam všech předplatných Azure (společně s ID předplatného). V tomto kroku nastavíte výchozí předplatné Azure. Nahradit &lt;your-subscription-id > zástupný prvek s ID požadovaného předplatného Azure. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Vytvořte skupinu prostředků. Nahraďte &lt;your--název skupiny prostředků > název nové skupiny prostředků, a nahraďte zástupný symbol &lt;vaše umístění > umístěním. Příkaz `az account list-locations` zobrazí všech umístěních Azure. Během AKS ve verzi preview jsou k dispozici všechny umístění. Pokud zadáte umístění, které v tuto chvíli není platný, chybová zpráva obsahuje seznam dostupných umístění.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Vytvoření clusteru Kubernetes. Nahraďte &lt;your--název skupiny prostředků > s názvem skupiny prostředků vytvořené v předchozím kroku a nahraďte &lt;your kubernetes název clusteru-> s názvem nového clusteru. (Tento proces může trvat několik minut.)

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Nastavení clusteru Kubernetes

Můžete nastavit modrozelené nasazení ve službě AKS ručně nebo pomocí nastavení klonovat skript uvedený v ukázce výše. V této části můžete zjistit, jak provést obojí.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Nastavení clusteru Kubernetes pomocí instalačního skriptu vzorku
1. Upravit **deploy/aks/setup/setup.sh** souboru nahraďte následující zástupné symboly příslušnými hodnotami pro vaše prostředí: 

    - **&lt;your--název skupiny prostředků >**
    - **&lt;vaše kubernetes název clusteru->**
    - **&lt;vaše umístění >**
    - **&lt;your--přípony názvu dns >**

    ![Snímek obrazovky setup.sh skript v prostředí bash, se zvýrazněným několik zástupných symbolů](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Spusťte instalační skript.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Ručně nastavit Kubernetes cluster 
1. Stáhněte konfiguraci Kubernetes do složky vašeho profilu.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Změnit adresář, který má **nasazení/aks/nastavení** adresáře. 

1. Spusťte následující příkaz **kubectl** příkazy k nastavení služby pro veřejný koncový bod a dva koncové body testu.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Aktualizace názvu DNS pro veřejnost a koncových bodů testu. Při vytváření clusteru Kubernetes, můžete také vytvořit [další skupinu](https://github.com/Azure/AKS/issues/3), s vzor pojmenování spočívající v **MC_&lt;your--název skupiny prostředků >_ &lt; vaše kubernetes název clusteru->_&lt;vaše umístění >**.

    Vyhledání veřejné IP adresy ve skupině prostředků.

    ![Snímek obrazovky veřejné IP adresy ve stejné skupině prostředků](./media/jenkins-aks-blue-green-deployment/publicip.png)

    Pro každou službu najdete externí IP adresu spuštěním následujícího příkazu:
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Aktualizace názvu DNS pro příslušnou IP adresou pomocí následujícího příkazu:

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Opakujte volání pro `todoapp-test-blue` a `todoapp-test-green`:

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Název DNS musí být jedinečný v rámci vašeho předplatného. Chcete-li tuto jedinečnost zajistili, můžete použít `<your-dns-name-suffix>`.

### <a name="create-an-instance-of-container-registry"></a>Vytvořte instanci ve službě Container Registry

1. Spustit `az acr create` příkaz pro vytvoření instance z registru kontejneru. V další části, můžete pak použít `login server` jako adresa URL registru Dockeru.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Spustit `az acr credential` příkazu můžete zobrazit svoje přihlašovací údaje registru kontejneru. Všimněte si registru Dockeru uživatelské jméno a heslo, protože je budete potřebovat v další části.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Příprava serveru Jenkins

V této části naleznete v tématu Postup přípravy serveru Jenkins a spusťte sestavení, který je v pořádku pro testování. Nicméně, abyste používali [agenta virtuálního počítače Azure](https://plugins.jenkins.io/azure-vm-agents) nebo [agentů kontejneru Azure](https://plugins.jenkins.io/azure-container-agents) abyste mohli spustit agenta v Azure pro spouštění buildů. Další informace najdete v článku Jenkinse na [bezpečnostních důsledcích stavíme na hlavní](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

1. Nasazení [hlavního serveru Jenkinse v Azure](https://aka.ms/jenkins-on-azure).

1. Připojení k serveru pomocí protokolu SSH a nainstalovat nástroje sestavení na serveru, kde jste spustili sestavení.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Nainstalujte Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Ujistěte se, že uživatel `jenkins` má oprávnění ke spuštění `docker` příkazy.

1. [Instalace kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Stáhněte si jq](https://stedolan.github.io/jq/download/).

1. Nainstalujte jq pomocí následujícího příkazu:

   ```bash
   sudo apt-get install jq
   ```
   
1. Nainstalujte moduly plug-in jenkins provedením následujících kroků v řídicím panelu Jenkinse:

    1. Vyberte **Jenkins Správa > Správa modulů plug-in > k dispozici**.
    1. Vyhledejte a nainstalujte modul plug-in služby Azure Container Service.

1. Přidáte přihlašovací údaje ke správě prostředků v Azure. Pokud nemáte modul plug-in, nainstalujte **přihlašovacích údajů Azure** modulu plug-in.

1. Přidat svoje přihlašovací údaje instančního objektu Azure jako typ **Microsoft Azure Service Principal**.

1. Přidat Azure Docker registru jména a hesla (získaný v části "Vytvoření instance kontejneru registru") jako typ **uživatelské jméno s heslem**.

## <a name="edit-the-jenkinsfile"></a>Upravit souboru Jenkinsfile

1. Přejděte ve vlastním úložišti `/deploy/aks/`a otevřete `Jenkinsfile`.

2. Aktualizace souboru následujícím způsobem:

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    Aktualizace ID přihlašovacích údajů registru kontejneru:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Vytvoření úlohy
1. Přidat novou úlohu v typu **kanálu**.

1. Vyberte **kanálu** > **definice** > **kanálu skriptu ze Správce řízení služeb**.

1. Zadejte adresu URL správce řízení služeb úložiště se vaše &lt;vaše úložiště Rozvětvená >.

1. Zadejte cesta ke skriptu jako `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Spuštění úlohy

1. Ověřte, že váš projekt spustit úspěšně v místním prostředí. Tady je způsob: [spuštění projektu v místním počítači](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it).

1. Spustíte úlohu Jenkins. Při prvním spuštění úlohy Jenkins nasadí aplikaci seznamu úkolů modré prostředí, což je výchozí prostředí neaktivní. 

1. Pokud chcete ověřit, že se úloha spustila, přejdete k těmto adresám URL:
    - Veřejný koncový bod: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Modré koncový bod- `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Zelená koncový bod- `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Veřejná a modré test koncové body mají stejnou aktualizaci zelené koncový bod se zobrazí výchozí obrázek tomcat. 

Při spuštění sestavení více než jednou, procházení nasazení modrá a zelená. Jinými slovy Pokud aktuální prostředí je modrá, nasadí úlohu a testy zelené prostředí. Poté pokud jsou dobré testy, úloha aktualizuje veřejný koncový bod aplikace můžete provoz směrovat na zelené prostředí.

## <a name="additional-information"></a>Další informace

Další informace o nasazení nulovými výpadky, najdete v tomto [šablonu pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili v tomto kurzu, můžete je odstranit.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Řešení potíží

Pokud v modulech plug-in Jenkinse narazíte na nějaké chyby, založte problém na stránce [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak k nasazení do AKS pomocí Jenkinse a vzor modrozelené nasazení. Další informace o poskytovateli služeb Azure Jenkins najdete v tématu Jenkinse na webu Azure.

> [!div class="nextstepaction"]
> [Jenkins v Azure](/azure/jenkins/)