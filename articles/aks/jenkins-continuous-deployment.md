---
title: Průběžné nasazování volaných s Kubernetes ve službě Azure Kubernetes Service
description: Jak automatizovat proces průběžné nasazování s volaných k nasazení a upgrade kontejnerizované aplikace na Kubernetes ve službě Azure Kubernetes Service
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 84baf01ce6eeed8dc569d7a856189aefba788126
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096471"
---
# <a name="continuous-deployment-with-jenkins-and-azure-kubernetes-service"></a>Průběžné nasazování pomocí volaných a Kubernetes služby Azure

Tento dokument ukazuje, jak nastavit průběžné nasazování základní pracovní postup mezi volaných a cluster Azure Kubernetes služby (AKS).

Příklad pracovního postupu zahrnuje následující kroky:

> [!div class="checklist"]
> * Nasaďte aplikaci Azure hlas ke svému clusteru Kubernetes.
> * Aktualizujte kód aplikace Azure hlas a push na Githubu úložiště, které zahájí proces průběžné nasazování.
> * Volaných provede klonování úložiště a vytvoří novou bitovou kopii kontejnerů aktualizovaným kódem.
> * Tuto bitovou kopii se posune do Azure kontejneru registru (ACR).
> * Aplikace běžící v clusteru AKS se aktualizuje s novou bitovou kopii kontejneru.

## <a name="prerequisites"></a>Požadavky

Aby bylo možné dokončit kroky v tomto článku potřebujete následující položky.

- Základní znalosti Kubernetes, Git, CI/CD a kontejner Azure registru (ACR).
- [Clusteru Azure Kubernetes služby (AKS)] [ aks-quickstart] a [AKS pověření nakonfigurované] [ aks-credentials] ve vývojovém systému.
- [Registru Azure kontejneru registru (ACR)][acr-quickstart], název ACR přihlášení serveru a [ACR pověření] [ acr-authentication] nabízení a vyžadování přístup.
- Azure CLI nainstalován ve vývojovém systému.
- Docker nainstalován ve vývojovém systému.
- Účet GitHub [Githubu osobní přístupový token][git-access-token]a Git klient nainstalován na váš vývojový systém.

## <a name="prepare-application"></a>Příprava aplikace

V tomto dokumentu aplikace Azure hlas obsahuje webové rozhraní hostované v jedné nebo více pracovními stanicemi soustředěnými kolem a druhý pod hostování Redis pro dočasná data úložiště.

Před vytvořením volaných / AKS integrace připravit a nasadit aplikaci Azure hlas AKS clusteru. Považujte jej za verze jedna aplikace.

Rozvětvení následující úložiště GitHub.

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

Po vytvoření rozvětvení naklonujte váš vývojový systém. Ujistěte se, že používáte adresu URL vašeho rozvětvení, při klonování tohoto úložiště.

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Změňte adresáře tak, abyste pracovali v naklonovaném adresáři.

```bash
cd azure-voting-app-redis
```

Spustit `docker-compose.yaml` soubor vytvořit `azure-vote-front` kontejneru image a spuštění aplikace.

```bash
docker-compose up -d
```

Po dokončení použít [imagí dockeru] [ docker-images] příkazu zobrazte vytvořené bitové kopie.

Všimněte si, že se stáhly nebo vytvořily tři image. Image `azure-vote-front` obsahuje aplikaci a jako základ využívá image `nginx-flask`. Image `redis` slouží ke spuštění instance Redis.

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Získat serveru ACR přihlášení s [az acr seznamu] [ az-acr-list] příkaz. Nezapomeňte aktualizovat název skupiny prostředků se skupinou prostředků hostování vaší ACR registru.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Použití [docker značka] [ docker-tag] příkaz k označení bitové kopie s přihlašovací jméno serveru a číslo verze `v1`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Aktualizujte hodnotu ACR přihlášení serveru se název serveru ACR přihlášení a push `azure-vote-front` bitovou kopii do registru.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>Nasaďte aplikaci do Kubernetes

Kubernetes, může být soubor manifestu v kořenovém úložišti Azure hlas a slouží k nasazení aplikací do clusteru Kubernetes.

Nejdřív aktualizovat **azure-vote-all-in-one-redis.yaml** souboru manifestu se umístění vašeho ACR registru. V každém textovém editoru otevřete soubor a nahraďte `microsoft` s název ACR přihlášení serveru. Tuto hodnotu najdete na řádku **47** souboru manifestu.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Pak pomocí [kubectl použít] [ kubectl-apply] příkaz ke spuštění aplikace. Tento příkaz analyzuje soubor manifestu a vytvoří definované objekty Kubernetes.

```bash
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

A [Kubernetes služby] [ kubernetes-service] se vytvoří vystavit aplikace k Internetu. Tento proces může trvat několik minut.

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

## <a name="deploy-jenkins-to-vm"></a>Nasazení volaných k virtuálnímu počítači

Skript byl předem vytvořený k nasazení virtuálního počítače, konfigurovat přístup k síti a dokončení základní instalace volaných. Kromě toho skript zkopíruje konfiguračního souboru Kubernetes z váš vývojový systém volaných systému. Tento soubor se používá pro ověřování mezi volaných a AKS clusteru.

Spusťte následující příkazy ke stažení a spuštění skriptu. Následující adresu URL lze také zkontrolovat obsah skriptu.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Po dokončení skriptu vypíše adresu serveru volaných jako dobře klíč k odemknutí volaných. Přejděte na adresu URL, zadejte klíč a následující na obrazovce zobrazí výzvu k dokončení konfigurace volaných.

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

## <a name="jenkins-environment-variables"></a>Proměnné prostředí volaných

Proměnné prostředí volaných slouží k uložení název serveru přihlášení Azure kontejneru registru (ACR). Tato proměnná se odkazuje během úlohu volaných průběžné nasazování.

Když na portál pro správu volaných klikněte na tlačítko **spravovat volaných** > **nakonfigurujte systém**.

V části **vlastnosti globálních**, vyberte **proměnné prostředí**a přidejte proměnná s názvem `ACR_LOGINSERVER` a hodnotu serveru ACR přihlášení.

![Proměnné prostředí volaných](media/aks-jenkins/env-variables.png)

Po dokončení klikněte na tlačítko **Uložit** na stránce konfigurace volaných.

## <a name="jenkins-credentials"></a>Přihlašovací údaje volaných

Nyní uložte přihlašovací údaje ACR objekt volaných přihlašovacích údajů. Tyto přihlašovací údaje se odkazuje během úlohu volaných sestavení.

Zpět na portál pro správu volaných, klikněte na tlačítko **pověření** > **volaných** > **globální přihlašovací údaje (neomezený)**  >   **Přidejte pověření**.

Zkontrolujte, zda je typ přihlašovacích údajů **uživatelské jméno s heslem** a zadejte následující položky:

- **Uživatelské jméno** -ID hlavní použití služby pro ověřování s registrem ACR.
- **Heslo** -tajný klíč klienta hlavní použití služby pro ověřování s registrem ACR.
- **ID** -přihlašovacích údajů, jako identifikátor `acr-credentials`.

Po dokončení by měl vypadat podobně jako tento obrázek formuláře přihlašovací údaje:

![Přihlašovací údaje ACR](media/aks-jenkins/acr-credentials.png)

Klikněte na tlačítko **OK** a vrátíte se k portálu pro správu volaných.

## <a name="create-jenkins-project"></a>Vytvoření projektu volaných

Z portálu správce volaných, klikněte na tlačítko **novou položku**.

Pojmenujte projekt, například `azure-vote`, vyberte **volný styl projektu**a klikněte na tlačítko **OK**.

![Volaných projektu](media/aks-jenkins/jenkins-project.png)

V části **Obecné**, vyberte **Githubu projektu** a zadejte adresu URL vašeho pokračovatelem projektu Azure hlas Githubu.

![GitHub projektu](media/aks-jenkins/github-project.png)

V části **správu zdrojového kódu**, vyberte **Git**, zadejte adresu URL vašeho rozvětvení úložiště GitHub hlas Azure.

Pro přihlašovací údaje, klikněte na a **přidat** > **volaných**. V části **druhu**, vyberte **tajný text** a zadejte vaše [Githubu osobní přístupový token] [ git-access-token] jako tajný klíč.

Vyberte **přidat** po dokončení.

![Přihlašovací údaje Githubu](media/aks-jenkins/github-creds.png)

V části **sestavení aktivační události**, vyberte **Githubu háku aktivační událost pro dotazování GITScm**.

![Volaných sestavení aktivační události](media/aks-jenkins/build-triggers.png)

V části **sestavení prostředí**, vyberte **použít tajný texty nebo soubory**.

![Prostředí sestavení volaných](media/aks-jenkins/build-environment.png)

V části **vazby**, vyberte **přidat** > **uživatelské jméno a heslo (oddělených)**.

Zadejte `ACR_ID` pro **proměnné uživatelského jména**, a `ACR_PASSWORD` pro **proměnnou hesla**.

![Volaných vazby](media/aks-jenkins/bindings.png)

Přidat **sestavení krok** typu **spustit prostředí** a použijte následující text. Tento skript vytvoří novou bitovou kopii kontejner a jeho nabízených oznámení do vaší ACR registru.

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

Přidejte další **sestavení krok** typu **spustit prostředí** a použijte následující text. Tento skript aktualizace Kubernetes nasazení.

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

Po dokončení klikněte na tlačítko **Uložit**.

## <a name="test-the-jenkins-build"></a>Testování volaných sestavení

Než budete pokračovat, otestujte volaných sestavení. Ověří, zda úlohu sestavení byl správně nakonfigurován, správný soubor Kubernetes ověřování je místní a aby byly zadány správné přihlašovací údaje ACR.

Klikněte na tlačítko **sestavení teď** v levé nabídce projektu.

![Volaných testování sestavení](media/aks-jenkins/test-build.png)

Během tohoto procesu je k serveru sestavení volaných klonovat úložiště GitHub. Novou bitovou kopii kontejneru je vytvořen a instaluje do registru ACR. Nakonec Azure hlas aplikace běžící v clusteru AKS je aktualizovat a použít novou bitovou kopii. Vzhledem k tomu, že byly provedeny žádné změny kódu aplikace, aplikace se nezmění.

Po dokončení procesu klikněte na **sestavení #1** v části sestavení historie a vyberte **výstup konzoly** zobrazíte všechny výstup z procesu sestavení. Na posledním řádku by měl být uveden úspěšném sestavení.

## <a name="create-github-webhook"></a>Vytvoření webhooku GitHubu

V dalším kroku připojit úložiště aplikací se serverem volaných sestavení tak, aby na všech potvrzení se aktivuje nové sestavení.

1. Přejděte do forked úložiště GitHub.
2. Vyberte **Settings** (Nastavení) a pak vyberte **Integrations & services** (Integrace a služby) na levé straně.
3. Zvolte **přidat službu**, zadejte `Jenkins (GitHub plugin)` v pole filtru a vyberte modul plug-in.
4. Pro volaných napojit adresu URL, zadejte `http://<publicIp:8080>/github-webhook/` kde `publicIp` je IP adresa serveru volaných. Nezapomeňte zahrnout koncový znak /.
5. Vyberte možnost Přidat služby.

![Webhook GitHubu](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>Testovat proces CI/CD koncová

Na vývojovém počítači otevře Klonovaná aplikace v editoru kódu.

V části **/azure-vote/azure-vote** adresáře, najít a soubor s názvem **config_file.cfg**. Aktualizujte hodnoty hlas v tomto souboru na jinou hodnotu než kočky a PSI.

Následující příklad ukazuje a aktualizovat **config_file.cfg** souboru.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Po dokončení soubor uložte, provedení změn a oznámení do vaší rozvětvení úložiště GitHub... Po dokončení potvrzení webhook Githubu aktivuje nového sestavení volaných aktualizací bitové kopie kontejneru a AKS nasazení. Monitorování procesu sestavení v konzole pro správu volaných.

Po dokončení sestavení znovu přejděte ke koncovému bodu aplikace sledovat změny.

![Azure hlas aktualizovat](media/aks-jenkins/azure-vote-updated-safari.png)

V tomto okamžiku dokončení procesu jednoduché průběžné nasazování. Kroky a konfigurace uvedené v tomto příkladu jde použít k sestavení se průběžné sestavení automatizace robustnější a produkční prostředí.

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