---
title: Průběžné nasazování Jenkins s využitím Kubernetes ve službě Azure Kubernetes
description: Jak automatizovat proces průběžného nasazování pomocí Jenkinse k nasazení a upgrade kontejnerizované aplikace v Kubernetes ve službě Azure Kubernetes
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 246943b7e3df955394a6a79f9b3130633fe4ec50
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186609"
---
# <a name="continuous-deployment-with-jenkins-and-azure-kubernetes-service"></a>Průběžné nasazování pomocí Jenkinse a Azure Kubernetes Service

Tento dokument ukazuje, jak nastavit průběžné nasazování základní pracovní postup mezi Jenkins a cluster Azure Kubernetes Service (AKS).

Ukázkový pracovní postup zahrnuje následující kroky:

> [!div class="checklist"]
> * Nasazení aplikace Azure vote k vašemu clusteru Kubernetes.
> * Aktualizujte kód aplikace Azure vote a vložit do úložiště GitHub, které zahájí proces průběžného nasazování.
> * Jenkins naklonuje úložiště a sestaví novou image kontejneru s aktualizovaným kódem.
> * Tato image se vloží do Azure Container Registry (ACR).
> * Aplikace spuštěné v clusteru AKS je aktualizována s novou image kontejneru.

## <a name="prerequisites"></a>Požadavky

Abyste mohli dokončit kroky v tomto článku budete potřebovat následující položky.

- Základní znalosti o Kubernetes, Git, CI/CD a Azure Container Registry (ACR).
- [Cluster Azure Kubernetes Service (AKS)] [ aks-quickstart] a [nakonfigurované přihlašovací údaje AKS] [ aks-credentials] ve vývojovém systému.
- [Registr Azure Container Registry (ACR)][acr-quickstart], název přihlašovacího serveru ACR a [přihlašovací údaje služby ACR] [ acr-authentication] push a pull přístup.
- Azure CLI nainstalované ve vývojovém systému.
- Docker nainstalovaný ve vývojovém systému.
- Účet GitHub [osobní přístupový token Githubu][git-access-token]a klient Git ve vývojovém systému nainstalovaná.

## <a name="prepare-application"></a>Příprava aplikace

V tomto dokumentu aplikace Azure vote obsahuje webové rozhraní hostovaným v jeden nebo více podů a druhý pod hostování pro ukládání dočasných dat Redis.

Před vytvořením Jenkinse / integrace AKS, přípravě a nasazení aplikace Azure vote ke svému clusteru AKS. Představte si to jako verze jedna aplikace.

Rozvětvení následující úložiště GitHub.

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

Po vytvoření forku, naklonujte ho váš vývojový systém. Ujistěte se, že používáte adresu URL vašeho forku, při klonování toto úložiště.

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Změňte adresáře tak, abyste pracovali v naklonovaném adresáři.

```bash
cd azure-voting-app-redis
```

Spustit `docker-compose.yaml` souboru se má vytvořit `azure-vote-front` image kontejneru a spuštění aplikace.

```bash
docker-compose up -d
```

Po dokončení [imagí dockeru] [ docker-images] příkazu zobrazte vytvořenou image.

Všimněte si, že se stáhly nebo vytvořily tři image. Image `azure-vote-front` obsahuje aplikaci a jako základ využívá image `nginx-flask`. Image `redis` slouží ke spuštění instance Redis.

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Získat přihlašovacího serveru ACR s [az acr list] [ az-acr-list] příkazu. Nezapomeňte aktualizovat název skupiny prostředků se skupinou prostředků, který je hostitelem vašeho registru ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Použití [docker tag] [ docker-tag] příkaz k označení image s názvem přihlašovacího serveru a číslo verze `v1`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Aktualizujte hodnotu ACR login server název přihlašovacího serveru ACR a nabízených oznámení `azure-vote-front` image do registru.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>Nasazení aplikace do Kubernetes

Může být soubor manifestu Kubernetes najít v kořenovém adresáři úložiště Azure vote a slouží k nasazení aplikace do clusteru Kubernetes.

Nejprve aktualizovat **azure-vote-all-in-one-redis.yaml** soubor manifestu se umístění vašeho registru ACR. Pomocí libovolného textového editoru otevřete soubor a nahradit `microsoft` použijte název přihlašovacího serveru ACR. Tuto hodnotu najdete na řádku **47** souboru manifestu.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Pak pomocí [použití kubectl] [ kubectl-apply] příkaz ke spuštění aplikace. Tento příkaz analyzuje soubor manifestu a vytvoří definované objekty Kubernetes.

```bash
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

A [služby Kubernetes] [ kubernetes-service] se zveřejnit aplikaci na Internetu. Tento proces může trvat několik minut.

Pomocí příkazu [kubectl get service][kubectl-get] s argumentem `--watch` můžete sledovat průběh.

```bash
kubectl get service azure-vote-front --watch
```

Zpočátku se *EXTERNAL-IP* (Externí IP adresa) pro službu *azure-vote-front* bude zobrazovat ve stavu *probíhá*.

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Jakmile se stav adresy *EXTERNAL-IP* změní ze stavu *Probíhá* na hodnotu *IP adresa*, pomocí klávesové zkratky `control+c` zastavte sledovací proces kubectl.

```
azure-vote-front   10.0.34.242   13.90.150.118   80:30676/TCP   2m
```

Pokud se chcete na aplikaci podívat, přejděte na externí IP adresu.

![Obrázek clusteru Kubernetes v Azure](media/aks-jenkins/azure-vote-safari.png)

## <a name="deploy-jenkins-to-vm"></a>K virtuálnímu počítači. nasazení Jenkinse

Skript se předem vytvořený k nasazení virtuálního počítače, konfigurovat přístup k síti a dokončení základní instalace Jenkinse. Kromě toho skript zkopíruje konfigurační soubor Kubernetes z vývojového systému k systému Jenkins. Tento soubor slouží k ověřování mezi Jenkins a clusteru AKS.

Spusťte následující příkazy ke stažení a spuštění skriptu. Níže uvedenou adresu URL je také možné zkontrolovat obsah skriptu.

> [!WARNING]
> Tento ukázkový skript je pro účely ukázky k rychlému zřízení prostředí Jenkins, která běží na Virtuálním počítači Azure. Ke konfiguraci virtuálního počítače a pak zobrazí požadované přihlašovací údaje používá rozšíření vlastních skriptů Azure. Vaše *~/.kube/config* je zkopírován do virtuálního počítače s Jenkinsem.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Po dokončení skriptu vypíše adresu pro Jenkins server jako dobře klíč k odemknutí Jenkinse. Přejděte na adresu URL, zadejte klíč a po na obrazovce zobrazí výzvu k dokončení konfigurace Jenkinse.

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

## <a name="jenkins-environment-variables"></a>Proměnné prostředí Jenkins

Proměnná prostředí Jenkins se používá k uložení název přihlašovacího serveru služby Azure Container Registry (ACR). Tato proměnná odkazuje během úlohy průběžné nasazování Jenkins.

Na portálu pro správu Jenkinse, klikněte na tlačítko **spravovat Jenkins** > **konfigurovat systém**.

V části **globální vlastnosti**vyberte **proměnné prostředí**a přidat proměnnou s názvem `ACR_LOGINSERVER` a hodnota přihlašovacího serveru ACR.

![Proměnné prostředí Jenkins](media/aks-jenkins/env-variables.png)

Jakmile budete hotovi, klikněte na tlačítko **Uložit** na stránce konfigurace Jenkinse.

## <a name="jenkins-credentials"></a>Přihlašovací údaje Jenkinse

Objekt přihlašovacích údajů Jenkins nyní uložte svoje přihlašovací údaje služby ACR. Tyto přihlašovací údaje jsou odkazovány během úlohy sestavení Jenkinse.

Zpět na portál pro správu Jenkinse, klikněte na tlačítko **pověření** > **Jenkins** > **globální přihlašovací údaje (neomezená)**  >   **Přidat přihlašovací údaje**.

Ujistěte se, že typ přihlašovacích údajů **uživatelské jméno s heslem** a zadejte následující položky:

- **Uživatelské jméno** – ID objektu zabezpečení pomocí služby pro ověřování pomocí služby Container registry.
- **Heslo** -tajný kód klienta instančního objektu pomocí služby pro ověřování pomocí služby Container registry.
- **ID** -přihlašovacích údajů, jako identifikátor `acr-credentials`.

Jakmile budete hotovi, přihlašovací údaje formulář by měl vypadat nějak takto:

![Přihlašovací údaje služby ACR](media/aks-jenkins/acr-credentials.png)

Klikněte na tlačítko **OK** a vraťte se do portálu pro správu Jenkinse.

## <a name="create-jenkins-project"></a>Vytvoření projektu Jenkins

Z portálu pro správu Jenkinse, klikněte na tlačítko **nová položka**.

Pojmenujte projekt, například `azure-vote`vyberte **volný styl projektu**a klikněte na tlačítko **OK**.

![Projekt Jenkins](media/aks-jenkins/jenkins-project.png)

V části **Obecné**vyberte **projektu z Githubu** a zadejte adresu URL do svého forku Githubu aplikace Azure vote.

![Projektu z Githubu](media/aks-jenkins/github-project.png)

V části **Správa zdrojového kódu**vyberte **Git**, zadejte adresu URL k vašemu forku úložiště Azure Vote GitHub.

Zadání přihlašovacích údajů, klikněte na a **přidat** > **Jenkins**. V části **druh**vyberte **tajných kódů text** a zadejte vaše [osobní přístupový token Githubu] [ git-access-token] jako tajný klíč.

Vyberte **přidat** až budete hotovi.

![Přihlašovací údaje pro GitHub](media/aks-jenkins/github-creds.png)

V části **sestavit aktivační události**vyberte **GitHub hook trigger pro dotazování GITScm**.

![Sestavení v Jenkinsu pro aktivační události](media/aks-jenkins/build-triggers.png)

V části **sestavení prostředí**vyberte **použijte skryté texty nebo**.

![Prostředí pro sestavení Jenkinse](media/aks-jenkins/build-environment.png)

V části **vazby**vyberte **přidat** > **uživatelské jméno a heslo (oddělené)**.

Zadejte `ACR_ID` pro **proměnnou uživatelského jména**, a `ACR_PASSWORD` pro **proměnnou hesla**.

![Vazby Jenkinse](media/aks-jenkins/bindings.png)

Přidat **krok sestavení** typu **spustit prostředí** a použijte následující text. Tento skript vytvoří novou image kontejneru a předá je do registru ACR.

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

Přidejte další **krok sestavení** typu **spustit prostředí** a použijte následující text. Tento skript aktualizace nasazení Kubernetes.

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

Po dokončení klikněte na tlačítko **Uložit**.

## <a name="test-the-jenkins-build"></a>Testování buildu Jenkinse

Než budete pokračovat, otestujte buildu Jenkinse. Tato operace ověří, že úloha sestavení není správně nakonfigurovaná, správné ověřovacího souboru Kubernetes je na místě a, že byly zadány správné přihlašovací údaje služby ACR.

Klikněte na tlačítko **Build Now** v levé nabídce projektu.

![Jenkins testovací sestavení](media/aks-jenkins/test-build.png)

Během tohoto procesu je klonovat úložiště GitHub na serveru sestavení Jenkinse. Novou image kontejneru je vytvořeny a nahrány do registru ACR. Nakonec se aktualizuje aplikace Azure vote spuštěné v clusteru AKS použít nové image. Vzhledem k tomu, že byly provedeny žádné změny kódu aplikace, aplikace se nezmění.

Po dokončení procesu klikněte na **sestavení #1** v části Vytvoření historie a vyberte **výstup na konzole** zobrazíte všechny výstup z procesu sestavení. Na posledním řádku by měla zobrazovat úspěšné sestavení.

## <a name="create-github-webhook"></a>Vytvoření webhooku GitHubu

V dalším kroku připojení úložiště aplikací na server sestavení Jenkinse, tak, aby na všech potvrzeních změn se aktivuje nové sestavení.

1. Přejděte na rozvětveného úložiště GitHub.
2. Vyberte **nastavení**a pak vyberte **Webhooky** na levé straně.
3. Zvolit **přidat webhook**. Pro *datová část adresy URL*, zadejte `http://<publicIp:8080>/github-webhook/` kde `publicIp` je IP adresa serveru Jenkins. Nezapomeňte zahrnout koncový znak /. Nechte ostatní výchozí hodnoty pro typ obsahu a aktivační události na *nabízených* události.
4. Vyberte **přidat webhook**.

    ![Webhook GitHubu](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>Testování procesu CI/CD začátku do konce

Na svém vývojovém počítači otevřete naklonované aplikace pomocí editoru kódu.

V části **/azure-vote/azure-vote** adresáře, najděte soubor s názvem **config_file.cfg**. Aktualizujte hodnoty hlasování v tomto souboru na něco jiného než koček a psů.

Následující příklad ukazuje a aktualizovat **config_file.cfg** souboru.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Jakmile budete hotovi, uložte soubor, potvrzení změn a push do svého forku úložiště Githubu... Po dokončení potvrzení webhook Githubu spustí nové sestavení Jenkinse, která aktualizuje image kontejneru a nasazení služby AKS. Monitorování procesu sestavení v konzole pro správu Jenkinse.

Po dokončení sestavení znovu přejděte na koncový bod aplikace sledovat změny.

![Aktualizace Azure vote](media/aks-jenkins/azure-vote-updated-safari.png)

V tomto okamžiku procesu jednoduché průběžné nasazování se dokončila. Postup a konfigurace v tomto příkladu lze použít k sestavení automatizace průběžné sestavování více robustní a připravené pro produkční prostředí.

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az_acr_list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli