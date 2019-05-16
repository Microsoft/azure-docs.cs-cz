---
title: Vývoj v týmu na použití Azure Dev prostorů Kubernetes
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 04/25/2019
ms.topic: quickstart
description: Vývoj v týmu Kubernetes s kontejnery a mikroslužby na platformě Azure
keywords: Docker, Kubernetes, Azure, AKS, službě Azure Kubernetes, kontejnery, Helm, služby sítě, směrování sítě služby, kubectl, k8s
manager: jeconnoc
ms.openlocfilehash: 437c6284c563f85e25d2c5b81734ea3bc0507701
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596996"
---
# <a name="quickstart-team-development-on-kubernetes-using-azure-dev-spaces"></a>Rychlý start: Vývoj v týmu na použití Azure Dev prostorů Kubernetes

V tomto průvodci se naučíte:

- Nastavení Azure Dev mezery na spravovaný cluster Kubernetes v Azure.
- Nasazení rozsáhlé aplikace s různými mikroslužbami do prostoru na vývoj.
- Otestujte jednotné mikroslužeb v prostoru izolované vývoj v rámci celé aplikace.

![Vývoj v týmu Azure Dev mezery](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- [Nainstalované rozhraní Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Nainstalovat Helm 2,13 nebo větší](https://github.com/helm/helm/blob/master/docs/install.md).

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru Azure Kubernetes Service

Je nutné vytvořit v clusteru AKS [podporované oblasti](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams). Následujících příkazů vytvořte skupinu prostředků s názvem *MyResourceGroup* a cluster AKS volá *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --disable-rbac --generate-ssh-keys
```

*MyAKS* také vytvoření clusteru s jedním uzlem, pomocí *Standard_DS2_v2* velikost a pomocí RBAC zakázané.

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolit Azure Dev mezery ve vašem clusteru AKS

Použití `use-dev-spaces` příkazu povolit prostory vývoj ve vašem clusteru AKS a postupujte podle zobrazených výzev. Následující příkaz povolí vývoj mezery na *MyAKS* cluster v *MyResourceGroup* seskupovat a vytvoří prostor dev volá *dev*.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>Získat kód ukázkové aplikace

V tomto článku budete používat [Azure Dev prostory kol ukázkové aplikace pro sdílení obsahu](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) demonstruje použití Azure Dev mezery.

Klonování aplikace z Githubu a přejděte do jeho adresář:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Načíst HostSuffix pro *vývoj*

Použití `azds show-context` příkazu můžete zobrazit HostSuffix pro *dev*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Aktualizujte vaše HostSuffix Helm chart

Otevřít [charts/values.yaml](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml) a nahraďte všechny výskyty `<REPLACE_ME_WITH_HOST_SUFFIX>` HostSuffix hodnotou, který jste získali dříve. Uložte změny a zavřete soubor.

## <a name="run-the-sample-application-in-kubernetes"></a>Spuštění ukázkové aplikace v Kubernetes

Příkazy pro spuštění ukázkové aplikace v Kubernetes jsou součástí existujícímu procesu a mít žádná závislost na Azure Dev prostorů nástroje. V takovém případě Helm ale není nástroje použít ke spuštění této ukázkové aplikaci, ale další nástroje lze použít k spuštění celé aplikace v oboru názvů v rámci clusteru. Příkaz Helm cílí na vývoj prostor s názvem *dev* jste vytvořili dříve, ale toto místo vývoj je také obor názvů Kubernetes. V důsledku toho vývoje prostorů mohou být cíleny dalších nástrojů, stejně jako ostatní obory názvů.

Poté, co je aplikace spuštěna v clusteru bez ohledu na použité k jejímu nasazení nástroje, můžete použít Azure Dev prostory pro vývoj v týmu.

Použití `helm init` a `helm install` příkazy pro nastavení a nainstalovat ukázkovou aplikaci v clusteru.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic --wait
```

`helm install` Příkazu může trvat několik minut. Výstup příkazu se zobrazí stav všech služeb, které se nasadí do clusteru po dokončení:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic --wait

Hang tight while we grab the latest from your chart repositories...
...
NAME               READY  UP-TO-DATE  AVAILABLE  AGE
bikes              1/1    1           1          4m32s
bikesharingweb     1/1    1           1          4m32s
billing            1/1    1           1          4m32s
gateway            1/1    1           1          4m32s
reservation        1/1    1           1          4m32s
reservationengine  1/1    1           1          4m32s
users              1/1    1           1          4m32s
```

Jako ukázka aplikace je nainstalovaná ve vašem clusteru a vzhledem k tomu, že máte povolená ve vašem clusteru prostory Dev, používat `azds list-uris` příkazu pro zobrazení adresy URL pro ukázkovou aplikaci v *dev* , který aktuálně není vybrán.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Přejděte *bikesharingweb* service tak, že otevřete veřejnou adresu URL z `azds list-uris` příkazu. V příkladu výše, veřejnou adresu URL pro *bikesharingweb* služba `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Vyberte *Aurelia Briggse (zákazníka)* jako uživatel. Ověření se zobrazí text *Hi Aurelia Briggse | Odhlásit* v horní části.

![Azure sdílení kol prostory Dev ukázkové aplikace](media/quickstart-team-development/bikeshare.png)

## <a name="create-child-dev-spaces"></a>Vytváření podřízených dev mezery

Použití `azds space select` příkaz pro vytvoření dvou podřízených mezer v rámci *dev*:

```cmd
azds space select -n dev/azureuser1 -y
azds space select -n dev/azureuser2 -y
```

Výše uvedené příkazy pro vytvoření dvou podřízených mezer v rámci *dev* s názvem *azureuser1* a *azureuser2*. Tyto dva podřízené prostory představují různé dev mezery pro vývojáře *azureuser1* a *azureuser2* používat k provádění změn na ukázkovou aplikaci.

Použití `azds space list` příkazu seznamu všechny mezery dev a potvrďte *dev/azureuser2* zaškrtnuto.

```cmd
$ azds space list
Name            Selected
--------------  --------
default         False
dev             False
dev/azureuser1  False
dev/azureuser2  True
```

Použití `azds list-uris` pro zobrazení adresy URL pro ukázkovou aplikaci v aktuálně vybraných místa, které je *dev/azureuser2*.

```cmd
$ azds list-uris
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
```

Ověřte, že adresy URL zobrazen ve `azds list-uris` příkaz mít *azureuser2.s.dev* předponu. Tato předpona potvrdí, že je aktuální místo vybrané *azureuser2*, který je podřízeným prvkem *dev*.

Přejděte *bikesharingweb* služby *dev/azureuser2* dev místo tak, že otevřete veřejnou adresu URL z `azds list-uris` příkaz. V příkladu výše, veřejnou adresu URL pro *bikesharingweb* služba `http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/`. Vyberte *Aurelia Briggse (zákazníka)* jako uživatel. Ověření se zobrazí text *Hi Aurelia Briggse | Odhlásit se* v horní části.

## <a name="update-code"></a>Aktualizace kódu

Otevřít *BikeSharingWeb/components/Header.js* v textovém editoru a změní celý text v [element span s `userSignOut` className](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/components/Header.js#L16).

```html
<span className="userSignOut">
    <Link href="/devsignin"><span tabIndex="0">Welcome {props.userName} | Sign out</span></Link>
</span>
```

Uložte změny a zavřete soubor.

## <a name="build-and-run-the-updated-bikesharingweb-service-in-the-devazureuser2-dev-space"></a>Sestavte a spusťte službu aktualizované bikesharingweb *dev/azureuser2* dev místa

Přejděte *BikeSharingWeb /* adresář a spusťte `azds up` příkazu.

```cmd
$ cd ../BikeSharingWeb/
$ azds up

Using dev space 'dev/azureuser2' with target 'MyAKS'
Synchronizing files...2s
...
Service 'bikesharingweb' port 'http' is available at http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/
Service 'bikesharingweb' port 80 (http) is available at http://localhost:54256
...
```

Tento příkaz vytvoří a spustí *bikesharingweb* službu *dev/azureuser2* dev místa. Tato služba je provozována kromě *bikesharingweb* služby spuštěné *dev* a používá se pouze pro požadavky *azureuser2.s* předponu adresy URL. Další informace o tom, jak směrování funguje mezi nadřazenými a podřízenými prostory dev, naleznete v tématu [jak prostory vývoj Azure funguje a je nakonfigurován](how-dev-spaces-works.md).

Přejděte *bikesharingweb* služby *dev/azureuser2* dev místo tak, že otevřete veřejná adresa URL zobrazí ve výstupu příkazu `azds up` příkaz. Vyberte *Aurelia Briggse (zákazníka)* jako uživatel. Ověřte, že se zobrazí aktualizovaný text v pravém horním rohu. Budete muset aktualizovat stránku nebo vymazat mezipaměť prohlížeče, pokud se nezobrazí okamžitě tuto změnu.

![Azure Dev prostory kol ukázkové aplikace pro sdílení obsahu aktualizovat](media/quickstart-team-development/bikeshare-update.png)

## <a name="verify-other-dev-spaces-are-unchanged"></a>Ověřte, že jiné prostory Dev jsou beze změny

Pokud `azds up` příkaz je stále spuštěna, stiskněte klávesu *Ctrl + c*.

```cmd
$ azds list-uris --all
Uri                                                             Status
--------------------------------------------------              ---------
http://azureuser1.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser1.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://azureuser2.s.dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://azureuser2.s.dev.gateway.fedcab0987.eus.azds.io/         Available
http://dev.bikesharingweb.fedcab0987.eus.azds.io/               Available
http://dev.gateway.fedcab0987.eus.azds.io/                      Available
```

Přejděte *dev* verzi *bikesharingweb* v prohlížeči, zvolte *Aurelia Briggse (zákazníka)* jako uživatel a ověřit se zobrazí původní text v pravém horním rohu rohu. Opakujte tyto kroky *dev/azureuser1* adresy URL. Všimněte si, že změny se použijí pouze k *dev/azureuser2* verzi *bikesharingweb*. Tato izolace změny *dev/azureuser2* umožňuje *azureuser2* provést změny, aniž by to ovlivnilo *azureuser1*.

Tyto změny projeví v *dev* a *dev/azureuser1*, měli byste postupovat podle stávajícím pracovním postupu vašeho týmu nebo kanál CI/CD. Tento pracovní postup může například zahrnovat potvrzování změn do systému správy verzí a nasazení aktualizace pomocí kanálu CI/CD nebo nástrojů, jako jsou Helm.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další postup

Zjistěte, jak vám může služba Azure Dev Spaces pomoct s vývojem složitějších aplikací používajících více kontejnerů a jak si můžete zjednodušit spolupráci na vývoji díky práci s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Práce s více kontejnery a týmový vývoj](multi-service-nodejs.md)