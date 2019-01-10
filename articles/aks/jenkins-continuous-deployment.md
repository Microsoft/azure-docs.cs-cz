---
title: Kurz – nasazení z Githubu do Azure Kubernetes Service (AKS) pomocí Jenkinse
description: Nastavení Jenkinse pro průběžnou integraci (CI) z webu GitHub a průběžného nasazování (CD) do Azure Kubernetes Service (AKS)
services: container-service
ms.service: container-service
author: iainfoulds
ms.author: iainfou
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: 470ba6df76741dd5c9e9eed055cd7848d341082f
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188449"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Kurz: Nasazení z Githubu do Azure Kubernetes Service (AKS) pomocí Jenkinse průběžnou integraci a nasazování

V tomto kurzu nasadí ukázkovou aplikaci z Githubu na [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) clusteru nastavením průběžné integrace (CI) a průběžného nasazování (CD) Jenkinse. Když aktualizujete aplikaci formou potvrzení Githubu, Jenkins automaticky spustí nové sestavení do kontejneru, nabízených oznámení imagí kontejneru do služby Azure Container Registry (ACR) a pak spustí vaši aplikaci ve službě AKS. 

V tomto kurzu dokončíte tyto úlohy:

> [!div class="checklist"]
> * Nasaďte ukázkovou aplikaci Azure vote do clusteru AKS.
> * Vytvoření základního projektu Jenkins.
> * Nastavení přihlašovacích údajů pro Jenkinse k interakci s ACR.
> * Vytvoření webhooku Githubu pro automatizované buildy a úloha sestavení Jenkinse.
> * Otestujte kanál CI/CD pro aktualizaci aplikace ve službě AKS na základě potvrzení Githubu kódu.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, budete potřebovat tyto položky:

- Základní znalosti o Kubernetes, Git, CI/CD a kontejneru obrázků

- [Clusteru AKS] [ aks-quickstart] a `kubectl` nakonfigurovanou [přihlašovacích údajů clusteru AKS][aks-credentials]

- [Registr Azure Container Registry (ACR)][acr-quickstart], název přihlašovacího serveru ACR a cluster AKS, který je nakonfigurovaný tak, aby [ověření pomocí registru služby ACR][acr-authentication]

- Použití Azure CLI verze 2.0.46 nebo novější nainstalován a nakonfigurován. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

- [Nainstalovaný docker] [ docker-install] ve vývojovém systému

- Účet GitHub [osobní přístupový token Githubu][git-access-token]a klient Git ve vývojovém systému nainstalovaná

- Pokud zadáte vlastní Jenkins instanci spíše než toto ukázkové skripty způsob, jak nasadit Jenkinse, váš Jenkins instance potřebám [Docker nainstalovaný a nakonfigurovaný] [ docker-install] a [kubectl][kubectl-install].

## <a name="prepare-your-app"></a>Příprava aplikace

V tomto článku použijete ukázkovou aplikaci Azure vote obsahuje webové rozhraní hostovaným v jeden nebo více podů a druhý pod hostování pro ukládání dočasných dat Redis. Před integrace Jenkinse a AKS pro automatizovaná nasazení, nejprve ručně připravit a nasadit aplikaci Azure vote ke svému clusteru AKS. Toto ruční nasazení je verze, jeden z aplikace a vám umožňují vidět aplikaci v akci.

Vytvořit fork následující úložiště GitHub pro ukázkovou aplikaci - [ https://github.com/Azure-Samples/azure-voting-app-redis ](https://github.com/Azure-Samples/azure-voting-app-redis). Pokud chcete vytvořit fork úložiště do svého vlastního účtu GitHub, vyberte tlačítko **Fork** (Vytvořit fork) v pravém horním rohu.

Ho naklonujte do vývojového systému. Ujistěte se, že použijete adresu URL vašeho forku, při klonování toto úložiště:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Přejděte do adresáře z klonovaného forku:

```console
cd azure-voting-app-redis
```

Chcete-li vytvářet Image kontejneru, které jsou potřeba pro ukázkovou aplikaci, použijte *docker compose.yaml* soubor s `docker-compose`:

```console
docker-compose up -d
```

Požadované základní Image se berou a vytvořené s kontejnery aplikací. Pak můžete použít [imagí dockeru] [ docker-images] příkazu zobrazte vytvořenou image. Stáhly se nebo se vytvořily tři image. Image `azure-vote-front` obsahuje aplikaci a jako základ využívá image `nginx-flask`. `redis` Image se použije ke spuštění Redis instance:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Než budete moct odeslat *azure-vote-front* image kontejneru do služby ACR, získáte pomocí přihlašovacího serveru ACR [az acr list] [ az-acr-list] příkazu. Následující příklad získá adresu serveru přihlášení ACR pro registr ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Použití [docker tag] [ docker-tag] příkaz k označení image se název přihlašovacího serveru ACR a číslo verze `v1`. Zadejte vlastní `<acrLoginServer>` název získaný v předchozím kroku:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Nakonec push *azure-vote-front* image do registru ACR. Znovu nahraďte `<acrLoginServer>` použijte název přihlašovacího serveru vašeho vlastního registru ACR, jako `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Nasazení ukázkové aplikace pro AKS

Pokud chcete nasadit ukázkovou aplikaci pro váš cluster AKS, můžete použít soubor manifestu Kubernetes v kořenové složce úložiště úložiště Azure vote. Otevřít *azure-vote-all-in-one-redis.yaml* soubor pomocí editoru manifestu jako `vi`. Nahraďte `microsoft` názvem přihlašovacího serveru ACR. Na řádku není nalezena tato hodnota **47** souboru manifestu:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Pak pomocí [použití kubectl] [ kubectl-apply] příkazu Nasaďte aplikaci do clusteru AKS:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Služby Vyrovnávání zatížení Kubernetes se vytvoří zveřejnit aplikaci na Internetu. Tento proces může trvat několik minut. Pokud chcete monitorovat průběh nasazení nástroje pro vyrovnávání zatížení, použijte [kubectl get service] [ kubectl-get] příkaz `--watch` argument. Jakmile se stav adresy *EXTERNAL-IP* změní ze stavu *Probíhá* na hodnotu *IP adresa*, pomocí klávesové zkratky `Control + C` zastavte sledovací proces kubectl.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Pokud chcete zobrazit aplikaci v akci, otevřete webový prohlížeč na externí IP adresu vaší služby. Zobrazí se aplikace Azure vote, jak je znázorněno v následujícím příkladu:

![Ukázkový Azure vote aplikaci spuštěnou ve službě AKS](media/aks-jenkins/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Nasazení Jenkinse na Virtuálním počítači Azure

Rychlé nasazení Jenkinse pro použití v tomto článku, můžete použít následující skript k nasazení virtuálního počítače Azure, konfigurovat přístup k síti a dokončení základní instalace Jenkinse. Skript pro ověřování mezi Jenkins a AKS cluster, zkopíruje konfigurační soubor Kubernetes z vývojového systému k systému Jenkins.

> [!WARNING]
> Tento ukázkový skript je pro účely ukázky k rychlému zřízení prostředí Jenkins, která běží na Virtuálním počítači Azure. Ke konfiguraci virtuálního počítače a pak zobrazí požadované přihlašovací údaje používá rozšíření vlastních skriptů Azure. Vaše *~/.kube/config* je zkopírován do virtuálního počítače s Jenkinsem.

Spusťte následující příkazy ke stažení a spuštění skriptu. Měli byste si přečíst obsah všech skriptů před spuštěním jej- [ https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh ](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh).

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Trvá několik minut vytvořit virtuální počítač a nasazení součásti potřebné pro Dockeru a Jenkinse. Po dokončení skriptu vypíše adresu pro Jenkins server a klíč k odemknutí řídicího panelu, jak je znázorněno v následujícím příkladu výstupu:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Otevřete webový prohlížeč na adresu URL zobrazené a zadejte klíč odemknout. Použijte na obrazovce zobrazí výzvu k dokončení konfigurace Jenkinse:

- Zvolte **nainstalovat navrhované moduly plug-in**
- Vytvořte prvního uživatele s rolí správce. Zadejte uživatelské jméno, například *azureuser*, pak zadejte vlastní bezpečné heslo. Nakonec zadejte jméno a příjmení a e-mailovou adresu.
- Vyberte **Save and Finish** (Uložit a dokončit).
- Jakmile bude Jenkins připravený, vyberte **Start using Jenkins** (Začít používat Jenkinse).
    - Pokud se po začátku používání Jenkinse ve webovém prohlížeči zobrazí prázdná stránka, restartujte službu Jenkins. Restartovat službu SSH na veřejnou IP adresu instance Jenkinse a typ `sudo service jenkins restart`. Po restartování služby, aktualizujte webový prohlížeč.
- Přihlaste se k Jenkinsu s uživatelské jméno a heslo, které jste vytvořili v procesu instalace.

## <a name="create-a-jenkins-environment-variable"></a>Vytvořte proměnnou prostředí Jenkins

Proměnná prostředí Jenkins se používá k uložení název přihlašovacího serveru ACR. Tato proměnná se odkazuje během úlohy sestavení Jenkinse. K vytvoření této proměnné prostředí, proveďte následující kroky:

- Na levé straně portálu Jenkinse vyberte **spravovat Jenkins** > **konfigurovat systém**
- V části **globální vlastnosti**vyberte **proměnné prostředí**. Přidat proměnnou s názvem `ACR_LOGINSERVER` a hodnota přihlašovacího serveru ACR.

    ![Proměnné prostředí Jenkins](media/aks-jenkins/env-variables.png)

- Jakmile budete hotovi, klikněte na tlačítko **Uložit** v dolní části na stránce konfigurace Jenkinse.

## <a name="create-a-jenkins-credential-for-acr"></a>Vytvoření přihlašovacích údajů Jenkins pro službu ACR

Povolit Jenkinse k sestavení a potom nasdílejte aktualizované kontejnerové Image do služby ACR, budete muset zadat přihlašovací údaje pro službu ACR. Toto ověření můžete použít instanční objekty Azure Active Directory. V požadavcích, jste konfigurovali instanční objekt služby pro vaše AKS cluster s *čtečky* oprávnění do registru ACR. Tato oprávnění povolit clusteru AKS *o přijetí změn* imagí z registru ACR. Během procesu CI/CD Jenkinse sestaví novou Image kontejneru na základě aktualizací aplikace a pak musí *nabízených* těchto imagí do registru ACR. K oddělení rolí a oprávnění, teď nakonfigurovat hlavní název služby pro Jenkinse s *Přispěvatel* oprávnění do registru ACR.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Vytvoření instančního objektu pro Jenkinse k použití služby ACR

Nejprve vytvořte službu objektu zabezpečení pomocí [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] příkaz:

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Poznamenejte si, *appId* a *heslo* uvedené ve výstupu. Tyto hodnoty se používají v následujícím postupu ke konfiguraci prostředků přihlašovacích údajů v Jenkinsu.

Získejte ID prostředku registru ACR pomocí [az acr show] [ az-acr-show] příkazů a uložte ho jako proměnnou. Zadejte název skupiny prostředků a název ACR:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Teď vytvořte přiřazení rolí k přiřazení objektu služby *Přispěvatel* práva do registru ACR. V následujícím příkladu, zadejte vlastní *appId* zobrazí ve výstupu předchozího příkazu k vytvoření instančního objektu služby:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Vytvořit prostředek přihlašovacích údajů v Jenkinsu pro službu ACR instanční objekt

Přiřazení role vytvořené v Azure teď uložte svoje přihlašovací údaje služby ACR objekt přihlašovacích údajů Jenkinse. Tyto přihlašovací údaje jsou odkazovány během úlohy sestavení Jenkinse.

Zpět na levé straně portálu Jenkinse, klikněte na tlačítko **pověření** > **Jenkins** > **globální přihlašovací údaje (neomezená)**  >  **Přidat přihlašovací údaje**

Ujistěte se, že typ přihlašovacích údajů **uživatelské jméno s heslem** a zadejte následující položky:

- **Uživatelské jméno** – *appId* objektu služby, které jsou vytvořené pro ověřování pomocí služby Container registry.
- **Heslo** – *heslo* objektu služby, které jsou vytvořené pro ověřování pomocí služby Container registry.
- **ID** -přihlašovacích údajů, jako identifikátor *acr-credentials*

Jakmile budete hotovi, přihlašovací údaje formulář vypadá jako v následujícím příkladu:

![Vytvořte objekt přihlašovacích údajů Jenkinse s informací o instančním objektu služby](media/aks-jenkins/acr-credentials.png)

Klikněte na tlačítko **OK** a vraťte se k portálu Jenkinse.

## <a name="create-a-jenkins-project"></a>Vytvoření projektu Jenkins

Na domovské stránce portálu Jenkinse vyberte **nová položka** na levé straně:

1. Zadejte *azure-vote* jako název úlohy. Zvolte **Freestyle project**a pak vyberte **OK**
1. V části **General** (Obecné) vyberte **GitHub project** (Projekt GitHub) a zadejte URL rozvětveného úložiště, například *https://github.com/\<your-github-account\>/azure-voting-app-redis*.
1. V části **Source code management** (Správa zdrojového kódu) vyberte **Git** a zadejte adresu URL rozvětveného úložiště *.git*, například *https://github.com/\<your-github-account\>/azure-voting-app-redis.git*.

1. V části **sestavit aktivační události** vyberte **GitHub hook trigger pro dotazování GITscm**
1. V části **sestavení prostředí**vyberte **používat skryté texty nebo soubory**
1. V části **vazby**vyberte **přidat** > **uživatelské jméno a heslo (oddělte je).**
    - Zadejte `ACR_ID` pro **proměnnou uživatelského jména**, a `ACR_PASSWORD` pro **proměnná hesla**

    ![Vazby Jenkinse](media/aks-jenkins/bindings.png)

1. Zvolte možnost Přidat **krok sestavení** typu **spustit prostředí** a použijte následující text. Tento skript vytvoří novou image kontejneru a předá je do registru ACR.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Přidejte další **krok sestavení** typu **spustit prostředí** a použijte následující text. Tento skript aktualizace s novou image kontejneru ze služby ACR nasazení aplikace ve službě AKS.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Po dokončení klikněte na tlačítko **Uložit**.

## <a name="test-the-jenkins-build"></a>Testování buildu Jenkinse

Před automatizací úlohy na základě potvrzení Githubu nejprve ručním testováním buildu Jenkinse. Ruční sestavení ověří, že úlohy není správně nakonfigurovaná, správné ověřovacího souboru Kubernetes je na místě a funkčnost ověřování pomocí ACR.

V levé nabídce Projekt vyberte **Build Now**.

![Jenkins testovací sestavení](media/aks-jenkins/test-build.png)

Sestavení první bere minutu nebo dvě jako vrstvy image Dockeru se berou k serveru Jenkins. Následující sestavení můžete zkrátit dobu sestavování vrstvy bitové kopie v mezipaměti.

Během procesu sestavování je klonovat úložiště GitHub na server sestavení Jenkinse. Novou image kontejneru je vytvořeny a nahrány do registru ACR. Nakonec se aktualizuje aplikace Azure vote spuštěné v clusteru AKS použít nové image. Vzhledem k tomu, že byly provedeny žádné změny kódu aplikace, aplikace se nezmění, pokud si zobrazit ukázkové aplikace ve webovém prohlížeči.

Po dokončení úlohy sestavení klikněte na **sestavení #1** sestavení v části historie. Vyberte **výstup na konzole** a zobrazte výstup z procesu sestavení. Na posledním řádku by měla zobrazovat úspěšné sestavení.

## <a name="create-a-github-webhook"></a>Vytvoření webhooku Githubu

S úspěšné ruční sestavení dokončeno nyní integrace Githubu do buildu Jenkinse. Webhook umožňuje spuštění úlohy sestavení Jenkinse pokaždé, když je k potvrzení změn kódu na Githubu. Pokud chcete vytvořit webhook Githubu, proveďte následující kroky:

1. Přejděte do svého rozvětveného úložiště Githubu ve webovém prohlížeči.
1. Vyberte **nastavení**a pak vyberte **Webhooky** na levé straně.
1. Zvolit **přidat webhook**. Pro *datová část adresy URL*, zadejte `http://<publicIp:8080>/github-webhook/`, kde `<publicIp>` je IP adresa serveru Jenkins. Nezapomeňte zahrnout koncový znak /. Nechte ostatní výchozí hodnoty pro typ obsahu a aktivační události na *nabízených* události.
1. Vyberte **přidat webhook**.

    ![Vytvoření webhooku Githubu pro Jenkins](media/aks-jenkins/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Testování úplný kanál CI/CD

Nyní můžete otestovat celý kanál CI/CD. Pokud potvrdíte a vložíte změny kódu do Githubu, stane se následující kroky:

1. Githubu webhook kontaktuje Jenkinse.
1. Jenkins spustí úlohu sestavení a stáhne nejnovější potvrzení změn kódu z Githubu.
1. Spuštění sestavení Dockeru použitím aktualizovaného kódu a novou image kontejneru je označené nejnovější číslo sestavení.
1. Převede se tento novou image kontejneru do služby Azure Container Registry.
1. Vaše aplikace nasazené na aktualizace služby Azure Kubernetes s použitím nejnovější image kontejneru z registru Azure Container Registry.

Na svém vývojovém počítači otevřete naklonované aplikace pomocí editoru kódu. V části */azure-vote/azure-vote* adresář, otevřete soubor s názvem **config_file.cfg**. Aktualizujte hodnoty hlasování v tomto souboru na něco jiného než koček a psů, jak je znázorněno v následujícím příkladu:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Při aktualizaci, uložte soubor, potvrdit změny a push do svého forku úložiště GitHub. Webhook Githubu aktivuje novou úlohu sestavení v Jenkinsu. Na řídicím panelu Jenkinse webové monitorování procesu sestavení. Trvá několik sekund až o přijetí změn nejnovější kód, vytvoření a nahrání aktualizované image a nasaďte aktualizovanou aplikaci ve službě AKS.

Po dokončení sestavení aktualizujte obsah webového prohlížeče ukázkové aplikace Azure vote. Vaše změny se zobrazují, jak je znázorněno v následujícím příkladu:

![Ukázka Azure hlasovat ve službě AKS aktualizoval úloha sestavení Jenkinse](media/aks-jenkins/azure-vote-updated.png)

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak používat jako součást řešení CI/CD Jenkinse. AKS může integrovat s dalšími řešeními CI/CD a nástroje pro automatizaci, jako [Azure DevOps Project] [ azure-devops] nebo [vytváření clusteru AKS pomocí Ansible] [ aks-ansible].

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[docker-install]: https://docs.docker.com/install/
[kubectl-install]: https://kubernetes.io/docs/tasks/tools/install-kubectl/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az-acr-list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md#grant-aks-access-to-acr
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[azure-devops]: ../devops-project/azure-devops-project-aks.md
[aks-ansible]: ../ansible/ansible-create-configure-aks.md
