---
title: Nasazení do služby Azure Kubernetes Service (AKS) s využitím Jenkinse a vzoru modrého/zeleného nasazení
description: Zjistěte, jak provést nasazení do služby Azure Kubernetes Service (AKS) s využitím Jenkinse a vzoru modrého/zeleného nasazení.
ms.service: jenkins
keywords: jenkins, azure, devops, kubernetes, k8s, aks, blue green deployment, continuous delivery, cd
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/11/2018
ms.openlocfilehash: 9bd601aee87ab0776069c80bfdeffb70b06c3c86
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073879"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Nasazení do služby Azure Kubernetes Service (AKS) s využitím Jenkinse a vzoru modrého/zeleného nasazení

Azure Kubernetes Service (AKS) spravuje hostované prostředí Kubernetes a umožňuje tak rychle a snadno nasazovat a spravovat kontejnerizované aplikace. Nepotřebujete k tomu žádné znalosti orchestrace kontejnerů. AKS zároveň eliminuje režii spojenou s probíhajícími operacemi a údržbou díky zřizování, upgradování a škálování prostředků na vyžádání. Nemusíte přitom odpojovat své aplikace. Další informace o AKS najdete v [dokumentaci k AKS](/azure/aks/).

Modré/zelené nasazení je vzor průběžného doručování Azure DevOps, který spoléhá na zachování živé stávající (modré) verze, zatímco probíhá nasazení nové (zelené) verze. V tomto vzoru se obvykle využívá vyrovnávání zatížení ke směrování stále většího objemu provozu do zeleného nasazení. Pokud se při monitorování zjistí incident, je možné provoz přesměrovat do modrého nasazení, které je stále aktivní. Další informace o průběžném doručování najdete v tématu [Co je průběžné doručování](/azure/devops/what-is-continuous-delivery).

V tomto kurzu zjistíte, jak provést následující úlohy:

> [!div class="checklist"]
> * Získání informací o vzoru modrého/zeleného nasazení
> * Vytvoření spravovaného clusteru Kubernetes
> * Spuštění ukázkového skriptu pro konfiguraci clusteru Kubernetes
> * Ruční konfigurace clusteru Kubernetes
> * Vytvoření a spuštění úlohy Jenkinse

## <a name="prerequisites"></a>Požadavky
- [Účet GitHub](https://github.com) : Potřebujete účet Githubu naklonujte ukázkové úložiště.
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) : Použití rozhraní příkazového řádku Azure CLI 2.0 k vytvoření clusteru Kubernetes.
- [Chocolatey](https://chocolatey.org): Správce balíčků, které používáte k instalaci kubectl.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): Rozhraní příkazového řádku, který používáte pro spuštění příkazů pro clustery Kubernetes.
- [jq](https://stedolan.github.io/jq/download/): Procesor JSON zjednodušené, příkazového řádku.

## <a name="clone-the-sample-app-from-github"></a>Naklonování ukázkové aplikace z GitHubu

V úložišti Microsoftu na GitHubu najdete ukázkovou aplikaci, která ukazuje nasazení do AKS s využitím Jenkinse a vzoru modrého/zeleného nasazení. V této části vytvoříte fork tohoto úložiště ve svém účtu GitHub a naklonujete aplikaci do místního systému.

1. Přejděte do úložiště GitHub s ukázkovou aplikací [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git).

    ![Snímek obrazovky s ukázkovou aplikací v úložišti Microsoftu na GitHubu](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Vytvořte fork úložiště tak, že v pravém horním rohu stránky vyberete **Fork** (Vytvořit fork) a podle zobrazených pokynů vytvoříte fork úložiště ve svém účtu GitHub.

    ![Snímek obrazovky GitHubu s možností vytvořit fork](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Po vytvoření forku úložiště uvidíte, že se název účtu změní na název vašeho účtu, a poznámku, která značí, ze kterého úložiště se fork vytvořil (Microsoft).

    ![Snímek obrazovky s názvem účtu GitHub a poznámkou](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Vyberte **Clone or download** (Naklonovat nebo stáhnout).

    ![Snímek obrazovky GitHubu s možností naklonovat nebo stáhnout úložiště](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. V okně **Clone with HTTPS** (Naklonovat pomocí HTTPS) vyberte ikonu **kopírování**.

    ![Snímek obrazovky GitHubu s možností zkopírovat adresu URL klonu do schránky](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Otevřete okno terminálu nebo prostředí Git Bash.

1. Přejděte do požadovaného adresáře, kam chcete uložit místní kopii (klon) úložiště.

1. Pomocí příkazu `git clone` naklonujte adresu URL, kterou jste si zkopírovali dříve.

    ![Snímek obrazovky prostředí Git Bash s příkazem git clone](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Stisknutím klávesy Enter spusťte proces klonování.

    ![Snímek obrazovky prostředí Git Bash s výsledkem příkazu git clone](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Přejděte do nově vytvořeného adresáře, který obsahuje klon zdroje aplikace.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Vytvoření a konfigurace spravovaného clusteru Kubernetes

V této části provedete následující kroky:

- Pomocí Azure CLI 2.0 vytvoříte spravovaný cluster Kubernetes.
- Zjistíte, jak pomocí instalačního skriptu nebo ručně nastavit cluster.
- Vytvoříte instanci služby Azure Container Registry.

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Vytvoření spravovaného clusteru Kubernetes pomocí Azure CLI 2.0
Pokud chcete vytvořit spravovaný cluster Kubernetes pomocí [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), ujistěte se, že používáte Azure CLI verze 2.0.25 nebo novější.

1. Přihlaste se ke svému účtu Azure. Po zadání následujícího příkazu se zobrazí pokyny vysvětlující, jak přihlášení dokončit. 
    
    ```bash
    az login
    ```

1. Po spuštění příkazu `az login` v předchozím kroku se zobrazí seznam všech vašich předplatných Azure (společně s jejich ID předplatného). V tomto kroku nastavíte výchozí předplatné Azure. Zástupný text &lt;your-subscription-id> nahraďte za ID požadovaného předplatného Azure. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Vytvořte skupinu prostředků. Zástupný text &lt;your-resource-group-name> nahraďte názvem vaší nové skupiny prostředků a zástupný text &lt;your-location> nahraďte umístěním. Příkaz `az account list-locations` zobrazí všechna umístění Azure. V AKS verze Preview nejsou k dispozici všechna umístění. Pokud zadáte umístění, které aktuálně není platné, zobrazí se chybová zpráva se seznamem dostupných umístění.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Vytvořte cluster Kubernetes. Nahraďte &lt;your-resource-group-name> názvem skupiny prostředků vytvořené v předchozím kroku a &lt;your-kubernetes-cluster-name> názvem nového clusteru. (Dokončení tohoto procesu můžete trvat i několik minut.)

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Nastavení clusteru Kubernetes

Modré/zelené nasazení můžete v AKS nastavit ručně nebo pomocí instalačního skriptu, který je součástí dříve naklonované ukázky. V této části zjistíte, jak provést obojí.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Nastavení clusteru Kubernetes přes ukázkový instalační skript
1. Upravte soubor **deploy/aks/setup/setup.sh** a nahraďte následující zástupné texty odpovídajícími hodnotami pro vaše prostředí: 

    - **&lt;your-resource-group-name>** – Název vaší skupiny prostředků
    - **&lt;your-kubernetes-cluster-name>** – Název vašeho clusteru Kubernetes
    - **&lt;your-location>** – Vaše umístění
    - **&lt;your-dns-name-suffix>** – Vaše předpona názvu DNS

    ![Snímek obrazovky prostředí Bash se skriptem s několika zvýrazněnými zástupnými texty](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Spusťte instalační skript.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Ruční nastavení clusteru Kubernetes 
1. Do složky svého profilu si stáhněte konfiguraci Kubernetes.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Přejděte do adresáře **deploy/aks/setup**. 

1. Spusťte následující příkazy **kubectl**, které nastaví služby pro veřejný koncový bod a dva testovací koncové body.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Aktualizujte název DNS pro veřejný a testovací koncové body. Při vytváření clusteru Kubernetes můžete vytvořit také [další skupinu prostředků](https://github.com/Azure/AKS/issues/3) s názvem podle následujícího vzoru: **MC_&lt;název_vaší_skupiny_prostředků>_&lt;název_vašeho_clusteru_Kubernetes>_&lt;vaše_umístění>**.

    Vyhledejte veřejné IP adresy ve skupině prostředků.

    ![Snímek obrazovky s veřejnými IP adresami ve skupině prostředků](./media/jenkins-aks-blue-green-deployment/publicip.png)

    Externí IP adresy pro jednotlivé služby zjistíte spuštěním následujícího příkazu:
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Pomocí následujícího příkazu aktualizujte název DNS pro odpovídající IP adresu:

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Zopakujte volání `todoapp-test-blue` a `todoapp-test-green`:

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Název DNS musí být jedinečný v rámci vašeho předplatného. K zajištění této jedinečnosti můžete použít `<your-dns-name-suffix>`.

### <a name="create-an-instance-of-container-registry"></a>Vytvoření instance služby Container Registry

1. Spuštěním příkazu `az acr create` vytvořte instanci služby Container Registry. V další části pak můžete použít `login server` jako adresu URL registru Dockeru.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Spuštěním příkazu `az acr credential` zobrazte své přihlašovací údaje ke službě Container Registry. Poznamenejte si uživatelské jméno a heslo k registru Dockeru, protože je budete potřebovat v další části.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Příprava serveru Jenkinse

V této části se dozvíte, jak připravit server Jenkinse na spuštění sestavení. Pro účely testování je to dostačující. Pro spouštění vlastních sestavení byste však měli pomocí [agenta virtuálního počítače Azure](https://plugins.jenkins.io/azure-vm-agents) nebo [agenta kontejneru Azure](https://plugins.jenkins.io/azure-container-agents) aktivovat agenta v Azure. Další informace najdete v článku o Jenkinsu věnovaném [bezpečnostním důsledkům sestavování na hlavním serveru](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

1. Nasaďte [hlavní server Jenkinse do Azure](https://aka.ms/jenkins-on-azure).

1. Přes SSH se připojte k serveru, na kterém spustíte své sestavení, a nainstalujte na něm nástroje pro sestavování.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Nainstalujte Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Zajistěte, aby uživatel `jenkins` měl oprávnění ke spouštění příkazů `docker`.

1. [Nainstalujte kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Stáhněte jq](https://stedolan.github.io/jq/download/).

1. Pomocí následujícího příkazu nainstalujte jq:

   ```bash
   sudo apt-get install jq
   ```
   
1. Nainstalujte moduly plug-in do Jenkinse provedením následujících kroků na řídicím panelu Jenkinse:

    1. Vyberte **Manage Jenkins -> Manage Plugins -> Available** (Správa Jenkinse -> Správa modulů plug-in -> K dispozici).
    1. Vyhledejte a nainstalujte modul plug-in služby Azure Container Service.

1. Přidejte přihlašovací údaje pro správu prostředků v Azure. Pokud ho ještě nemáte, nainstalujte modul plug-in **Azure Credentials**.

1. Přidejte své přihlašovací údaje instančního objektu Azure jako typ **Microsoft Azure Service Principal** (Instanční objekt Microsoft Azure).

1. Přidejte své uživatelské jméno a heslo k registru Azure Docker (získali jste je v části Vytvoření instance služby Container Registry) jako typ **Username with password** (Uživatelské jméno s heslem).

## <a name="edit-the-jenkinsfile"></a>Úprava souboru Jenkinsfile

1. Ve vlastním úložišti přejděte do adresáře `/deploy/aks/` a otevřete soubor `Jenkinsfile`.

2. Aktualizujte soubor následujícím způsobem:

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    Aktualizujte ID přihlašovacích údajů ke službě Container Registry:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Vytvoření úlohy
1. Přidejte novou úlohu typu **Pipeline** (Kanál).

1. Vyberte **Pipeline** > **Definition** > **Pipeline script from SCM** (Kanál -> Definice -> Skript kanálu z SCM).

1. Místo &lt;your-forked-repo> zadejte adresu URL úložiště SCM.

1. Zadejte cestu ke skriptu `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Spuštění úlohy

1. Ověřte, že můžete projekt úspěšně spustit v místním prostředí. Zde je uveden postup: [Spuštění projektu v místním počítači](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it).

1. Spusťte úlohu Jenkinse. Při prvním spuštění úlohy Jenkins nasadí aplikaci seznamu úkolů do modrého prostředí, což je výchozí neaktivní prostředí. 

1. Pokud chcete ověřit, že se úloha spustila, přejděte na následující adresy URL:
    - Veřejný koncový bod: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Modrý koncový bod: `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Zelený koncový bod: `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

Veřejný a modrý testovací koncový bod mají stejnou aktualizaci, zatímco zelený koncový bod zobrazuje výchozí obrázek serveru Tomcat. 

Pokud sestavení spustíte vícekrát, bude cyklicky probíhat v modrém a zeleném nasazení. Jinými slovy, pokud je aktuální prostředí modré, úloha se nasadí a otestuje v zeleném prostředí. Pokud testy dopadnou dobře, úloha pak aktualizuje veřejný koncový bod aplikace tak, aby směroval provoz do zeleného prostředí.

## <a name="additional-information"></a>Další informace

Další informace o nasazení bez výpadků najdete v této [šabloně pro rychlý start](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky vytvořené v tomto kurzu nepotřebujete, můžete je odstranit.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>Řešení potíží

Pokud v modulech plug-in Jenkinse narazíte na nějaké chyby, založte problém na stránce [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak provést nasazení do AKS s využitím Jenkinse a vzoru modrého/zeleného nasazení. Další informace o poskytovateli Azure Jenkins najdete na webu Jenkins v Azure.

> [!div class="nextstepaction"]
> [Jenkins v Azure](/azure/jenkins/)