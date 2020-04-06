---
title: Nasazení z GitHubu do AKS s Jenkinsem
titleSuffix: Azure Kubernetes Service
description: Nastavení Jenkinse pro průběžnou integraci (CI) z GitHubu a průběžného nasazení (CD) do služby Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: a93cfc77178ff7638217663c2ceda500aae4cfd7
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668638"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Kurz: Nasazení z GitHubu do služby Azure Kubernetes Service (AKS) s nepřetržitou integrací a nasazením Jenkinse

Tento kurz nasazuje ukázkovou aplikaci z GitHubu do clusteru [služby Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) nastavením průběžné integrace (CI) a průběžného nasazení (CD) v Jenkinsi. Tímto způsobem, když aktualizujete aplikaci odesláním potvrzení na GitHub, Jenkins automaticky spustí nové sestavení kontejneru, odešle ibi kontejnerů do registru kontejnerů Azure (ACR) a pak spustí vaši aplikaci v AKS. 

V tomto kurzu dokončíte tyto úkoly:

> [!div class="checklist"]
> * Nasaďte ukázkovou aplikaci Azure vote do clusteru AKS.
> * Vytvořte základní projekt Jenkinse.
> * Nastavte přihlašovací údaje pro Jenkinse pro interakci s ACR.
> * Vytvořte úlohu sestavení Jenkinse a webhook GitHub pro automatizovaná sestavení.
> * Otestujte kanál CI/CD a aktualizujte aplikaci v AKS na základě potvrzení kódu GitHub.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete tyto položky:

- Základní znalosti kubernetes, Git, CI/CD a kontejnerové obrázky

- [Cluster AKS](../aks/kubernetes-walkthrough.md) `kubectl` a nakonfigurovaný s [pověřeními clusteru AKS](/cli/azure/aks#az-aks-get-credentials).

- [Registr registru kontejnerů Azure (ACR),](../container-registry/container-registry-get-started-azure-cli.md)název přihlašovacího serveru ACR a cluster AKS nakonfigurovaný pro [ověřování pomocí registru ACR](../aks/cluster-container-registry-integration.md).

- Azure CLI verze 2.0.46 nebo novější nainstalované a nakonfigurované. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli).

- [Docker nainstalovaný](https://docs.docker.com/install/) ve vašem vývojovém systému

- Účet GitHub, [token osobního přístupu GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)u vašeho vývojového systému a klient Git nainstalovaný ve vašem vývojovém systému

- Pokud zadáte vlastní instanci Jenkinse, nikoli tento ukázkový skriptovaný způsob nasazení Jenkinse, vaše instance Jenkinse potřebuje [Docker nainstalovaný a nakonfigurovaný](https://docs.docker.com/install/) a [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## <a name="prepare-your-app"></a>Příprava aplikace

V tomto článku použijete ukázkovou aplikaci Azure vote, která obsahuje webové rozhraní hostované v jednom nebo více podech, a druhý pod hostující Redis pro dočasné úložiště dat. Než integrujete Jenkinse a AKS pro automatizovaná nasazení, nejprve ručně připravte a nasaďte aplikaci Azure vote do clusteru AKS. Toto ruční nasazení je verze jedna z aplikace a umožňuje zobrazit aplikaci v akci.

> [!NOTE]
> Ukázková aplikace Azure vote používá pod Linuxu, který je naplánován o spuštění na uzlu Linux. Tok popsaný v tomto článku funguje také pro pod systému Windows Server naplánovaný v uzlu systému Windows Server.

Rozvláštit následující úložiště GitHub pro ukázkovou aplikaci - [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis). Pokud chcete vytvořit fork úložiště do svého vlastního účtu GitHub, vyberte tlačítko **Fork** (Vytvořit fork) v pravém horním rohu.

Naklonujte vidličku do vašeho vývojového systému. Ujistěte se, že při klonování tohoto repo používáte adresu URL vidlicí:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Změna adresáře klonované vidlice:

```console
cd azure-voting-app-redis
```

Chcete-li vytvořit image kontejneru potřebné pro ukázkovou aplikaci, použijte `docker-compose`soubor *docker-compose.yaml* s :

```console
docker-compose up -d
```

Požadované základní bitové kopie jsou vytaženy a kontejnery aplikace sestaveny. Potom můžete použít příkaz [image dockeru,](https://docs.docker.com/engine/reference/commandline/images/) abyste viděli vytvořenou bitovou kopii. Stáhly se nebo se vytvořily tři image. Image `azure-vote-front` obsahuje aplikaci a jako základ využívá image `nginx-flask`. Obrázek se `redis` používá ke spuštění instance Redis:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Než budete moci push *azure-vote-front* image kontejneru acr, získejte přihlašovací server ACR s příkazem [az acr list.](/cli/azure/acr#az-acr-list) Následující příklad získá adresu přihlašovacího serveru ACR pro registr ve skupině prostředků s názvem *myResourceGroup*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Pomocí příkazu [značky dockero](https://docs.docker.com/engine/reference/commandline/tag/) označte bitovou kopii `v1`názvem přihlašovacího serveru ACR a číslem verze aplikace . Zadejte `<acrLoginServer>` své vlastní jméno získané v předchozím kroku:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Nakonec posuňte image *azure-vote-front* do registru ACR. Opět nahraďte `<acrLoginServer>` název přihlašovacího serveru vlastního registru `myacrregistry.azurecr.io`ACR, například :

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Nasazení ukázkové aplikace do AKS

Chcete-li nasadit ukázkovou aplikaci do clusteru AKS, můžete použít soubor manifestu Kubernetes v kořenovém adresáři úložiště hlasování Azure. Otevřete soubor manifestu *azure-vote-all-in-one-redis.yaml* `vi`s editorem, jako je například . Nahraďte `microsoft` názvem přihlašovacího serveru ACR. Tato hodnota je nalezena na řádku **47** souboru manifestu:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Dále použijte příkaz [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) k nasazení aplikace do clusteru AKS:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Kubernetes služba vyrovnávání zatížení je vytvořena vystavit aplikaci k internetu. Tento proces může trvat několik minut. Chcete-li sledovat průběh nasazení vyrovnávání zatížení, použijte [příkaz kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) s argumentem. `--watch` Jakmile se stav adresy *EXTERNAL-IP* změní ze stavu *Probíhá* na hodnotu *IP adresa*, pomocí klávesové zkratky `Control + C` zastavte sledovací proces kubectl.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Chcete-li aplikaci zobrazit v akci, otevřete webový prohlížeč s externí IP adresou vaší služby. Aplikace Azure hlasování se zobrazí, jak je znázorněno v následujícím příkladu:

![Ukázková aplikace Azure, spuštěná v AKS](media/jenkins-continuous-deployment/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Nasazení Jenkinse do virtuálního počítače Azure

Chcete-li rychle nasadit Jenkins pro použití v tomto článku, můžete použít následující skript k nasazení virtuálního počítače Azure, konfigurace přístupu k síti a dokončení základní instalace Jenkinse. Pro ověřování mezi Jenkinsem a clusterem AKS skript zkopíruje konfigurační soubor Kubernetes z vývojového systému do systému Jenkins.

> [!WARNING]
> Tento ukázkový skript je pro ukázkové účely rychle zřídit prostředí Jenkins, který běží na virtuálním počítači Azure. Používá rozšíření vlastního skriptu Azure ke konfiguraci virtuálního počítače a potom zobrazit požadovaná pověření. Vaše *~/.kube/config* se zkopíruje do virtuálního počítače Jenkins.

Spusťte následující příkazy ke stažení a spuštění skriptu. Před spuštěním skriptu byste si měli [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh)prohlédnout obsah libovolného skriptu - .

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Vytvoření virtuálního virtuálního zařízení a nasazení požadovaných součástí pro Docker a Jenkins trvá několik minut. Po dokončení skriptu výstupem adresu pro server Jenkins a klíč k odemknutí řídicího panelu, jak je znázorněno v následujícím příkladu výstupu:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Otevřete webový prohlížeč na zobrazenou adresu URL a zadejte klíč odemknutí. Podle pokynů na obrazovce dokončete konfiguraci Jenkinse:

- Zvolte **Instalovat navrhované pluginy.**
- Vytvořte prvního uživatele s rolí správce. Zadejte uživatelské jméno, například *azureuser*, a zadejte vlastní zabezpečené heslo. Nakonec zadejte jméno a příjmení a e-mailovou adresu.
- Vyberte **Save and Finish** (Uložit a dokončit).
- Jakmile bude Jenkins připravený, vyberte **Start using Jenkins** (Začít používat Jenkinse).
    - Pokud se po začátku používání Jenkinse ve webovém prohlížeči zobrazí prázdná stránka, restartujte službu Jenkins. Chcete-li restartovat službu, SSH na veřejnou `sudo service jenkins restart`IP adresu instance Jenkins a zadejte . Po restartování služby aktualizujte webový prohlížeč.
- Přihlaste se k Jenkinsovi pomocí uživatelského jména a hesla, které jste vytvořili v procesu instalace.

## <a name="create-a-jenkins-environment-variable"></a>Vytvoření proměnné prostředí Jenkins

Proměnná prostředí Jenkins se používá k uložení názvu přihlašovacího serveru ACR. Tato proměnná je odkazována během úlohy sestavení Jenkinse. Chcete-li vytvořit tuto proměnnou prostředí, proveďte následující kroky:

- Na levé straně portálu Jenkins vyberte **Spravovat jenkinsový** > **konfigurovat systém.**
- V části **Globální vlastnosti**vyberte **proměnné prostředí**. Přidejte proměnnou `ACR_LOGINSERVER` s názvem a hodnotou přihlašovacího serveru ACR.

    ![Proměnné prostředí Jenkins](media/jenkins-continuous-deployment/env-variables.png)

- Po dokončení klikněte na **Uložit** v dolní části konfigurační stránky Jenkinse.

## <a name="create-a-jenkins-credential-for-acr"></a>Vytvoření pověření Jenkinse pro ACR

Chcete-li povolit Jenkins vytvářet a potom push aktualizované image kontejneru acr, je třeba zadat pověření pro ACR. Toto ověřování může používat objekty zabezpečení služby Azure Active Directory. V předpokladech jste nakonfigurovali instanční objekt pro cluster AKS s oprávněními *čtečky* do registru ACR. Tato oprávnění umožňují clusteru AKS *vyžádat* bitové kopie z registru ACR. Během procesu CI/CD Jenkins vytvoří nové image kontejneru na základě aktualizací aplikací a potom musí tyto bitové kopie *převést* do registru ACR. Pro oddělení rolí a oprávnění nyní nakonfigurujte instanční objekt pro Jenkinse s *oprávněními přispěvatele* do registru ACR.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Vytvoření instančního objektu pro Jenkinse pro použití ACR

Nejprve vytvořte instanční objekt pomocí příkazu [az ad sp create-for-rbac:](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)

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

Poznamenejte si *appId* a *heslo* uvedené ve výstupu. Tyto hodnoty se používají v následujících krocích ke konfiguraci prostředku pověření v Jenkinsi.

Získejte ID prostředku registru ACR pomocí příkazu [az acr show](/cli/azure/acr#az-acr-show) a uložte jej jako proměnnou. Zadejte název skupiny prostředků a název ACR:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Nyní vytvořte přiřazení role pro přiřazení práv hlavního *přispěvatele* služby registru ACR. V následujícím příkladu zadejte vlastní *appId* zobrazené ve výstupu předchozí příkaz k vytvoření instančního objektu:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Vytvoření prostředku pověření v Jenkinsi pro instanční objekt Služby ACR

S přiřazení role vytvořené v Azure, teď ukládat přihlašovací údaje ACR v objektu přihlašovacích údajů Jenkinse. Na tato pověření se odkazuje během úlohy sestavení Jenkinse.

Zpět na levé straně portálu Jenkins klikněte na **pověření** > **Jenkins** > **globální pověření (bez omezení)** > **Přidat přihlašovací údaje**

Ujistěte se, že druh pověření je **uživatelské jméno s heslem** a zadejte následující položky:

- **Uživatelské jméno** - *Id aplikace* instančního objektu vytvořeného pro ověřování s registrem ACR.
- **Heslo** – *heslo* instančního objektu vytvořeného pro ověřování pomocí registru ACR.
- **ID** – identifikátor pověření, *například acr-credentials*

Po dokončení vypadá formulář pověření v následujícím příkladu:

![Vytvoření objektu pověření Jenkinse s informacemi o instančním objektu](media/jenkins-continuous-deployment/acr-credentials.png)

Klikněte na **OK** a vraťte se na portál Jenkins.

## <a name="create-a-jenkins-project"></a>Vytvoření projektu Jenkins

Na domovské stránce portálu Jenkins vyberte **Nová položka** na levé straně:

1. Zadejte *azure-vote* jako název úlohy. Zvolte **freestyle projekt**, pak zvolte **OK**
1. V části **Obecné** vyberte **projekt GitHub** a zadejte rozčleněnou adresu URL úložiště, například *https:\//github.com/\<váš účet github\>/azure-oting-app-redis*
1. V části **Správa zdrojového kódu** vyberte **Git**, zadejte rozpůlenou adresu URL úložiště *.git,* například *https:\/\</github.com/ váš-github-account\>/azure-oting-app-redis.git*

1. V části **Build Triggers** vyberte **aktivační událost zavěšení GitHubu pro dotazování GITscm.**
1. V části **Sestavení prostředí**vyberte Použít **tajné texty nebo soubory.**
1. V části **Vazby**vyberte **Přidat** > **uživatelské jméno a heslo (oddělené)**
   - Zadejte `ACR_ID` **proměnnou uživatelského jména**a `ACR_PASSWORD` pro **proměnnou heslo.**

     ![Jenkinsova vazba](media/jenkins-continuous-deployment/bindings.png)

1. Zvolte, zda chcete přidat **krok sestavení** typu **Spustit prostředí** a použijte následující text. Tento skript vytvoří novou image kontejneru a odešle ji do registru ACR.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Přidejte další **krok sestavení** typu **Spustit prostředí** a použijte následující text. Tento skript aktualizuje nasazení aplikace v AKS s novou image kontejneru z ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Po dokončení klepněte na tlačítko **Uložit**.

## <a name="test-the-jenkins-build"></a>Testování sestavení Jenkinse

Než budete úlohu automatizovat na základě potvrzení GitHubu, nejprve ručně otestujte sestavení Jenkinse. Toto ruční sestavení ověří, zda byla úloha správně nakonfigurována, je na místě správný ověřovací soubor Kubernetes a že ověřování pomocí acr funguje.

V levé nabídce projektu vyberte **Build Now**.

![Sestavení testu Jenkinse](media/jenkins-continuous-deployment/test-build.png)

První sestavení trvá minutu nebo dvě, protože vrstvy image Dockeru jsou staženy na server Jenkins. Následná sestavení můžete použít vrstvy obrazu uložené v mezipaměti ke zlepšení doby sestavení.

Během procesu sestavení je úložiště GitHub naklonováno na server sestavení Jenkinse. Nová image kontejneru je sestavena a zasunuta do registru ACR. Nakonec je aplikace Azure hlasování spuštěná v clusteru AKS aktualizována tak, aby používala novou bitovou kopii. Vzhledem k tomu, že nebyly provedeny žádné změny kódu aplikace, aplikace se nezmění, pokud zobrazíte ukázkovou aplikaci ve webovém prohlížeči.

Po dokončení úlohy sestavení klikněte na **#1 sestavení** v historii sestavení. Vyberte **Výstup konzoly** a zobrazte výstup z procesu sestavení. Poslední řádek by měl označovat úspěšné sestavení.

## <a name="create-a-github-webhook"></a>Vytvoření webového háčku GitHub

S dokončením úspěšného ručního sestavení teď integrujte GitHub do sestavení Jenkinse. Webhook usměrněný lze použít ke spuštění úlohy sestavení Jenkinse při každém potvrzení kódu v GitHubu. Chcete-li vytvořit webhook GitHub, proveďte následující kroky:

1. Přejděte do rozpůleného úložiště GitHub ve webovém prohlížeči.
1. Vyberte **Nastavení**a potom na levé straně vyberte **Webhooky.**
1. Zvolte **přidání webhooku**. Pro *adresu URL*datové `http://<publicIp:8080>/github-webhook/`části `<publicIp>` zadejte , kde je IP adresa serveru Jenkins. Ujistěte se, že obsahují koncové /. Ponechte ostatní výchozí hodnoty pro typ obsahu a aktivovat na *nabízených* událostech.
1. Vyberte **Přidat webhook .**

    ![Vytvoření webového háčku GitHub pro Jenkinse](media/jenkins-continuous-deployment/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Otestujte celý kanál CI/CD

Nyní můžete otestovat celý kanál CI/CD. Když na GitHub uděláte potvrzení kódu, dojde k následujícím krokům:

1. Webový hák GitHub se natahuje k Jenkinsovi.
1. Jenkins spustí úlohu sestavení a vytáhne nejnovější potvrzení kódu z GitHubu.
1. Sestavení Dockeru se spustí pomocí aktualizovaného kódu a nová image kontejneru je označena nejnovějším číslem sestavení.
1. Tato nová image kontejneru se zasouvá do registru kontejnerů Azure.
1. Vaše aplikace nasazená do aktualizace služby Azure Kubernetes s nejnovější image kontejneru z registru kontejnerů Azure.

Ve vývojovém počítači otevřete klonoci s editorem kódu. V adresáři */azure-vote/azure-vote* otevřete soubor s názvem **config_file.cfg**. Aktualizujte hodnoty hlasování v tomto souboru na něco jiného než kočky a psy, jak je znázorněno v následujícím příkladu:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Po aktualizaci uložte soubor, potvrďte změny a zatlačte je do rozložky úložiště GitHub. Webhook GitHub aktivuje novou úlohu sestavení v Jenkinsi. Na webovém řídicím panelu Jenkinse sledujte proces sestavení. Trvá několik sekund vytáhnout nejnovější kód, vytvořit a push aktualizovanou bitovou kopii a nasadit aktualizovanou aplikaci v AKS.

Po dokončení sestavení aktualizujte webový prohlížeč ukázkové aplikace Azure vote. Změny se zobrazí tak, jak je znázorněno v následujícím příkladu:

![Ukázkové hlasování Azure v AKS aktualizované úlohou sestavení Jenkinse](media/jenkins-continuous-deployment/azure-vote-updated.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Jenkins v Azure](/azure/jenkins)