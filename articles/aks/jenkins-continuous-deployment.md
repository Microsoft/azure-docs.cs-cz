---
title: Kurz – nasazení z GitHubu do služby Azure Kubernetes Service (AKS) s Jenkinse
description: Nastavení Jenkinse pro kontinuální integraci (CI) z GitHubu a průběžného nasazování (CD) do služby Azure Kubernetes Service (AKS)
services: container-service
ms.service: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: e46e2c2933ee9afda860b68b10c135ac75a5d247
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263921"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Kurz: nasazení z GitHubu do služby Azure Kubernetes Service (AKS) s Jenkinse průběžnou integrací a nasazením

Tento kurz nasadí ukázkovou aplikaci z GitHubu do clusteru [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) tím, že v Jenkinse nastaví průběžnou integraci (CI) a průběžné nasazování (CD). To znamená, že když aktualizujete aplikaci vložením potvrzení do GitHubu, Jenkinse automaticky spustí nové sestavení kontejneru, nahraje image kontejneru do Azure Container Registry (ACR) a pak aplikaci spustí v AKS. 

V tomto kurzu dokončíte tyto úlohy:

> [!div class="checklist"]
> * Nasaďte ukázkovou aplikaci pro hlasování Azure do clusteru AKS.
> * Vytvořte základní projekt Jenkinse.
> * Nastavte přihlašovací údaje pro Jenkinse k interakci s ACR.
> * Vytvořte úlohu sestavení Jenkinse a Webhook GitHub pro automatizované sestavení.
> * Otestujte kanál CI/CD, aby se aplikace v AKS aktualizovala na základě potvrzení kódu GitHubu.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu budete potřebovat tyto položky:

- Základní porozumění pro Kubernetes, Git, CI/CD a image kontejnerů

- [Cluster AKS][aks-quickstart] a `kubectl` nakonfigurovaný s [přihlašovacími údaji clusteru AKS][aks-credentials]

- [Registr Azure Container Registry (ACR)][acr-quickstart], název přihlašovacího serveru ACR a cluster AKS nakonfigurovaný k [ověřování pomocí registru ACR][acr-authentication]

- Je nainstalovaná a nakonfigurovaná verze Azure CLI 2.0.46 nebo novější. Pro nalezení verze spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

- [Docker nainstalovaný][docker-install] ve vývojovém systému

- Účet GitHubu, [osobní přístupový token GitHubu][git-access-token]a klient Git nainstalovaný ve vývojovém systému

- Pokud zadáte vlastní instanci Jenkinse, ne tento ukázkový skript pro nasazování Jenkinse, vaše instance Jenkinse potřebuje [Docker nainstalovaný a nakonfigurovaný][docker-install] a [kubectl][kubectl-install].

## <a name="prepare-your-app"></a>Příprava aplikace

V tomto článku použijete ukázkovou aplikaci Azure, která obsahuje webové rozhraní hostované v jedné nebo více luskech, a druhý pod hostujícím Redis pro dočasné úložiště dat. Před integrací Jenkinse a AKS pro automatizovaná nasazení nejprve ručně Připravte a nasaďte aplikaci pro hlasování Azure do vašeho clusteru AKS. Toto Ruční nasazení je první verze aplikace a umožňuje vám zobrazit aplikaci v akci.

> [!NOTE]
> Ukázková aplikace pro hlasování Azure používá Linux pod, který je naplánován na spuštění v uzlu Linux. Tok, který je popsaný v tomto článku, funguje taky pro Windows Server pod naplánovaným v uzlu Windows serveru.

Rozvětvení tohoto úložiště GitHub pro ukázkovou aplikaci [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis). Pokud chcete vytvořit fork úložiště do svého vlastního účtu GitHub, vyberte tlačítko **Fork** (Vytvořit fork) v pravém horním rohu.

Naklonujte rozvětvení do vývojového systému. Při klonování tohoto úložiště nezapomeňte použít adresu URL vašeho rozvětvení:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Přejděte do adresáře klonovaného rozvětvení:

```console
cd azure-voting-app-redis
```

Chcete-li vytvořit image kontejneru potřebné pro ukázkovou aplikaci, použijte soubor *Docker-tváře. yaml* s `docker-compose`:

```console
docker-compose up -d
```

Požadované základní image jsou vyžádány a kontejnery aplikace jsou sestavené. Pak můžete pomocí příkazu [Docker images][docker-images] zobrazit vytvořenou bitovou kopii. Stáhly se nebo se vytvořily tři image. Image `azure-vote-front` obsahuje aplikaci a jako základ využívá image `nginx-flask`. `redis` image se používá ke spuštění instance Redis:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Před nahráním image kontejneru *Azure-hlasování* do ACR můžete pomocí příkazu [AZ ACR list][az-acr-list] získat přihlašovací server ACR. V následujícím příkladu se načte adresa přihlašovacího serveru ACR pro registr ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Použijte příkaz [Docker tag][docker-tag] k označení image názvem přihlašovacího serveru ACR a číslem verze `v1`. Zadejte vlastní název `<acrLoginServer>`, který jste získali v předchozím kroku:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Nakonec nahrajte do registru ACR image *hlasování Azure* . Znovu nahraďte `<acrLoginServer>` názvem přihlašovacího serveru vlastního registru ACR, například `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Nasazení ukázkové aplikace do AKS

Pokud chcete nasadit ukázkovou aplikaci do clusteru AKS, můžete použít soubor manifestu Kubernetes v kořenovém adresáři úložiště hlasování Azure. Otevřete soubor manifestu *Azure-hlasování-All-in-One-Redis. yaml* s editorem, jako je `vi`. Nahraďte `microsoft` názvem přihlašovacího serveru ACR. Tato hodnota se nachází na řádku **47** souboru manifestu:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Pak použijte příkaz [kubectl Apply][kubectl-apply] k nasazení aplikace do clusteru AKS:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Je vytvořena služba Vyrovnávání zatížení Kubernetes, která zpřístupňuje aplikaci na internetu. Tento proces může trvat několik minut. Chcete-li monitorovat průběh nasazení nástroje pro vyrovnávání zatížení, použijte příkaz [kubectl Get Service][kubectl-get] s argumentem `--watch`. Jakmile se stav adresy *EXTERNAL-IP* změní ze stavu *Probíhá* na hodnotu *IP adresa*, pomocí klávesové zkratky `Control + C` zastavte sledovací proces kubectl.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Chcete-li zobrazit aplikaci v akci, otevřete webový prohlížeč na externí IP adresu vaší služby. Zobrazí se hlasovací aplikace Azure, jak je znázorněno v následujícím příkladu:

![Ukázková aplikace Azure Sample Hlasujte běžící v AKS](media/aks-jenkins/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Nasazení Jenkinse do virtuálního počítače Azure

K rychlému nasazení Jenkinse pro použití v tomto článku můžete pomocí následujícího skriptu nasadit virtuální počítač Azure, nakonfigurovat přístup k síti a dokončit základní instalaci nástroje Jenkinse. Pro ověřování mezi Jenkinse a clusterem AKS skript zkopíruje konfigurační soubor Kubernetes z vývojového systému do systému Jenkinse.

> [!WARNING]
> Tento ukázkový skript slouží jako ukázka pro účely rychlého zřízení prostředí Jenkinse, které běží na virtuálním počítači Azure. Pomocí rozšíření vlastních skriptů Azure nakonfiguruje virtuální počítač a pak zobrazí požadovaná pověření. Vaše *~/.Kube/config* se zkopíruje do virtuálního počítače Jenkinse.

Spuštěním následujících příkazů Stáhněte a spusťte skript. Před spuštěním [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh)IT byste si měli projít obsah libovolného skriptu.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Vytvoření virtuálního počítače a nasazení požadovaných komponent pro Docker a Jenkinse trvá několik minut. Po dokončení skriptu vypíše adresa pro server Jenkinse a klíč k odemknutí řídicího panelu, jak je znázorněno v následujícím příkladu výstupu:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Otevřete webový prohlížeč na zobrazené adrese URL a zadejte klíč odemčení. Podle pokynů na obrazovce dokončete konfiguraci Jenkinse:

- Zvolit **instalaci navrhovaných modulů plug-in**
- Vytvořte prvního uživatele s rolí správce. Zadejte uživatelské jméno, například *azureuser*, a zadejte vlastní zabezpečené heslo. Nakonec zadejte jméno a příjmení a e-mailovou adresu.
- Vyberte **Save and Finish** (Uložit a dokončit).
- Jakmile bude Jenkins připravený, vyberte **Start using Jenkins** (Začít používat Jenkinse).
    - Pokud se po začátku používání Jenkinse ve webovém prohlížeči zobrazí prázdná stránka, restartujte službu Jenkins. Pokud chcete službu restartovat, připojte se přes SSH k veřejné IP adrese vaší instance Jenkinse a zadejte `sudo service jenkins restart`. Jakmile se služba restartuje, aktualizujte webový prohlížeč.
- Přihlaste se k Jenkinse pomocí uživatelského jména a hesla, které jste vytvořili v procesu instalace.

## <a name="create-a-jenkins-environment-variable"></a>Vytvoření proměnné prostředí Jenkinse

Proměnná prostředí Jenkinse se používá k uchování názvu přihlašovacího serveru ACR. Tato proměnná je odkazována během úlohy sestavení Jenkinse. Chcete-li vytvořit tuto proměnnou prostředí, proveďte následující kroky:

- Na levé straně portálu Jenkinse vyberte **Spravovat jenkinse** > **Konfigurovat systém** .
- V části **globální vlastnosti**vyberte **proměnné prostředí**. Přidejte proměnnou s názvem `ACR_LOGINSERVER` a hodnotou svého přihlašovacího serveru ACR.

    ![Proměnné prostředí Jenkinse](media/aks-jenkins/env-variables.png)

- Až budete hotovi, klikněte v dolní části stránky konfigurace Jenkinse na **Uložit** .

## <a name="create-a-jenkins-credential-for-acr"></a>Vytvoření Jenkinse přihlašovacích údajů pro ACR

Pokud chcete, aby Jenkinse sestavení a následné odesílání aktualizovaných imagí kontejneru do ACR, musíte zadat přihlašovací údaje pro ACR. Toto ověřování může používat Azure Active Directory instanční objekty. V nezbytných součástech jste nakonfigurovali instanční objekt pro cluster AKS s oprávněními *čtenářů* pro váš registr ACR. Tato oprávnění umožňují clusteru AKS *vyžádat* si image z registru ACR. Během procesu CI/CD Jenkinse sestaví nové image kontejnerů na základě aktualizací aplikace a potřebuje tyto image *Vložit* do registru ACR. Pro oddělení rolí a oprávnění teď nakonfigurujte instanční objekt pro Jenkinse s oprávněním *přispěvatele* do svého registru ACR.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Vytvoření instančního objektu pro Jenkinse pro použití ACR

Nejprve vytvořte instanční objekt pomocí příkazu [AZ AD SP Create-for-RBAC][az-ad-sp-create-for-rbac] :

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

Poznamenejte si *appId* a *heslo* zobrazené ve výstupu. Tyto hodnoty se používají v následujících krocích ke konfiguraci prostředku přihlašovacích údajů v Jenkinse.

Pomocí příkazu [AZ ACR show][az-acr-show] Získejte ID prostředku vašeho registru ACR a uložte ho jako proměnnou. Zadejte název skupiny prostředků a název ACR:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Teď vytvořte přiřazení role, abyste přiřadili oprávnění *přispěvatele* instančního objektu k registru ACR. V následujícím příkladu zadejte vlastní identifikátor *appId* , který je uveden ve výstupu předchozího příkazu k vytvoření instančního objektu:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Vytvoření prostředku přihlašovacích údajů v Jenkinse pro instanční objekt ACR

S přiřazením role vytvořeným v Azure teď uložte přihlašovací údaje ACR do objektu přihlašovacích údajů Jenkinse. Tyto přihlašovací údaje jsou odkazovány během úlohy sestavení Jenkinse.

Zpátky na levé straně portálu Jenkinse klikněte na **přihlašovací údaje** ** >  > ** **globální přihlašovací údaje (neomezeno)**  > **Přidat přihlašovací údaje** .

Zajistěte, aby byl druh přihlašovacích údajů **uživatelské jméno a heslo** , a zadejte následující položky:

- **Username** – *appId* objektu služby vytvořeného pro ověřování pomocí registru ACR
- **Heslo** – *heslo* instančního objektu, který se vytvořil pro ověřování pomocí registru služby ACR.
- **ID** – identifikátor přihlašovacích údajů, například *ACR – přihlašovací údaje*

Po dokončení bude formulář přihlašovacích údajů vypadat jako v následujícím příkladu:

![Vytvoření objektu přihlašovacích údajů Jenkinse s informacemi o instančním objektu](media/aks-jenkins/acr-credentials.png)

Klikněte na **OK** a vraťte se na portál Jenkinse.

## <a name="create-a-jenkins-project"></a>Vytvoření projektu Jenkinse

Na domovské stránce portálu Jenkinse vyberte na levé straně **položku Nová položka** :

1. Jako název úlohy zadejte *Azure-Hlasujte* . Zvolte **projekt Freestyle**a pak vyberte **OK** .
1. V části **Obecné** vyberte **projekt GitHub** a zadejte adresu URL rozvětveného úložiště, například *https:\//GitHub.com/\<your-GitHub-Account\>/Azure-voting-App-Redis*
1. V části **Správa zdrojového kódu** vyberte **Git**, zadejte adresu URL rozvětveného úložiště *. Git* , třeba *https:\//github.com/\<Your-GitHub-Account\>/Azure-voting-App-Redis.Git*

1. V části **triggery sestavení** vyberte **aktivační událost vidlice GitHubu pro dotazování gitscm Polling (** .
1. V části **prostředí sestavení**vyberte možnost **použít tajné texty nebo soubory** .
1. V části **vazby**vyberte **Přidat** > **uživatelské jméno a heslo (oddělené)** .
   - Zadejte `ACR_ID` pro **proměnnou uživatelského jména**a `ACR_PASSWORD` pro **proměnnou heslo** .

     ![Vazby Jenkinse](media/aks-jenkins/bindings.png)

1. Vyberte, chcete-li přidat **krok sestavení** typu **spustit prostředí** a použít následující text. Tento skript vytvoří novou image kontejneru a odešle ji do ACR registru.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Přidejte další **krok sestavení** typu **spustit prostředí** a použijte následující text. Tento skript aktualizuje nasazení aplikace v AKS s novou imagí kontejneru z ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Po dokončení klikněte na **Uložit**.

## <a name="test-the-jenkins-build"></a>Test buildu Jenkinse

Před automatizací úlohy na základě potvrzení GitHubu nejprve otestujte sestavení Jenkinse ručně. Toto ruční sestavení ověří, že byla úloha správně nakonfigurovaná, že je nastaven správný soubor Kubernetes Authentication a že ověřování pomocí ACR funguje.

V nabídce na levé straně projektu vyberte **vytvořit nyní**.

![Jenkinse testovací sestavení](media/aks-jenkins/test-build.png)

První sestavení trvá minutu nebo dvě, protože vrstvy imagí Docker jsou načítány na server Jenkinse. Následná sestavení mohou použít vrstvy imagí v mezipaměti ke zlepšení časů sestavení.

Během procesu sestavení je úložiště GitHub naklonované na server sestavení Jenkinse. Vytvoří se nová image kontejneru, která se vloží do registru ACR. Nakonec se aplikace hlasování Azure běžící v clusteru AKS aktualizuje, aby používala nový obrázek. Vzhledem k tomu, že se v kódu aplikace neudělaly žádné změny, aplikace se nezmění, pokud si ukázkovou aplikaci zobrazíte ve webovém prohlížeči.

Po dokončení úlohy sestavení klikněte v části Historie sestavení na **sestavení #1** . Vyberte možnost **výstup z konzoly** a zobrazte výstup procesu sestavení. Poslední řádek by měl indikovat úspěšné sestavení.

## <a name="create-a-github-webhook"></a>Vytvoření Webhooku GitHubu

Po úspěšném ručním sestavení je nyní integrováno GitHub do sestavení Jenkinse. Webhook se dá použít ke spuštění úlohy sestavení Jenkinse pokaždé, když se v GitHubu provede potvrzení kódu. Pokud chcete vytvořit Webhook GitHubu, proveďte následující kroky:

1. Přejděte do rozvětvené úložiště GitHub ve webovém prohlížeči.
1. Vyberte **Nastavení**a pak na levé straně vyberte **Webhooky** .
1. Vyberte možnost **Přidat Webhook**. V poli *Adresa URL datové části*zadejte `http://<publicIp:8080>/github-webhook/`, kde `<publicIp>` je IP adresa serveru Jenkinse. Nezapomeňte zahrnout koncovou/. Ostatní výchozí hodnoty pro typ obsahu ponechte na triggeru pro *nabízené* události.
1. Vyberte **Přidat Webhook**.

    ![Vytvoření Webhooku GitHubu pro Jenkinse](media/aks-jenkins/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Otestování kompletního kanálu CI/CD

Nyní můžete testovat celý kanál CI/CD. Po vložení potvrzení kódu do GitHubu dojde k následujícím krokům:

1. Webhook GitHubu se dostane do Jenkinse.
1. Jenkinse spustí úlohu sestavení a vyžádá si nejnovější potvrzení kódu z GitHubu.
1. Sestavení Docker je spuštěno pomocí aktualizovaného kódu a nová image kontejneru je označena nejnovějším číslem sestavení.
1. Tato nová image kontejneru je vložena do Azure Container Registry.
1. Vaše aplikace nasazená do služby Azure Kubernetes se aktualizuje s nejnovější imagí kontejneru z Azure Container Registry registru.

Ve vývojovém počítači otevřete naklonované aplikace pomocí editoru kódu. V adresáři */Azure-vote/Azure-vote* otevřete soubor s názvem **config_file. cfg**. Aktualizujte hodnoty hlasů v tomto souboru na jinou než kočky a psi, jak je znázorněno v následujícím příkladu:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Po aktualizaci uložte soubor, potvrďte změny a nahrajte je do vašeho rozvětvení úložiště GitHub. Webhook GitHubu aktivuje novou úlohu sestavení v Jenkinse. Na webovém řídicím panelu Jenkinse monitorujte proces sestavení. Stažení nejnovějšího kódu, vytvoření a vložení aktualizované image a nasazení aktualizované aplikace do AKS trvá několik sekund.

Po dokončení sestavení aktualizujte webový prohlížeč ukázkové aplikace pro hlasování v Azure. Zobrazí se vaše změny, jak je znázorněno v následujícím příkladu:

![Ukázkový hlas Azure v AKS aktualizovaný úlohou sestavení Jenkinse](media/aks-jenkins/azure-vote-updated.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak používat Jenkinse jako součást řešení CI/CD. AKS se dá integrovat s dalšími řešeními CI/CD a nástroji pro automatizaci, jako je například [projekt Azure DevOps][azure-devops] , nebo [Vytvoření clusteru AKS s Ansible][aks-ansible].

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
[acr-authentication]: cluster-container-registry-integration.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[azure-devops]: ../devops-project/azure-devops-project-aks.md
[aks-ansible]: ../ansible/ansible-create-configure-aks.md
