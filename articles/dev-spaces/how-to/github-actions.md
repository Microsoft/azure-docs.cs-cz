---
title: Akce GitHubu & službě Azure Kubernetes
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 10/24/2019
ms.topic: conceptual
description: Zkontrolujte a otestujte změny z žádosti o přijetí změn přímo ve službě Azure Kubernetes pomocí akcí GitHubu a Azure Dev Spaces.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, akce GitHubu, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 2638fe2cd12407e43d3b3b698cdfca5231362db4
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065935"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Akce GitHubu & službě Azure Kubernetes (Preview)

Azure Dev Spaces poskytuje pracovní postup pomocí akcí GitHubu, který umožňuje testovat změny z žádosti o přijetí změn přímo v AKS před sloučením žádosti o přijetí změn do hlavní větve vašeho úložiště. Spuštění aplikace, která bude kontrolovat změny žádosti o přijetí změn, může zvýšit spolehlivost vývojářů i členů týmu. Tato spuštěná aplikace může také pomáhat členům týmu, jako je, manažerům a návrhářům produktů, tvořit součást procesu revize během počátečních fází vývoje.

V tomto průvodci se naučíte:

- Nastavte Azure Dev Spaces ve spravovaném clusteru Kubernetes v Azure.
- Nasaďte rozsáhlou aplikaci s více mikroslužbami do vývojového prostoru.
- Nastavte CI/CD s akcemi GitHubu.
- Otestujte jednu mikroslužbu v izolovaném prostoru pro vývoj v rámci celé aplikace.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
- [Nainstalované rozhraní Azure CLI][azure-cli-installed]
- [Nainstalovaná Helm 2,13 nebo novější][helm-installed].
- Účet GitHub s [povolenými akcemi GitHubu][github-actions-beta-signup].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Vytvoření clusteru služby Azure Kubernetes

Cluster AKS musíte vytvořit v [podporované oblasti][supported-regions]. Níže uvedené příkazy vytvoří skupinu prostředků s názvem *MyResourceGroup* a cluster AKS s názvem *MyAKS*.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>Povolení Azure Dev Spaces v clusteru AKS

Pomocí příkazu `use-dev-spaces` povolte v clusteru AKS vývojářské prostory a postupujte podle pokynů. Následující příkaz povolí v *MyAKS* ve skupině *MyResourceGroup* vývojářské prostory a vytvoří vývojové místo s názvem *dev*.

> [!NOTE]
> Příkaz `use-dev-spaces` nainstaluje také Azure Dev Spaces CLI, pokud ještě není nainstalovaný. Azure Dev Spaces CLI nelze nainstalovat do Azure Cloud Shell.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry

Vytvořit Azure Container Registry (ACR):

```cmd
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Název ACR musí být v rámci Azure jedinečný a obsahovat 5-50 alfanumerických znaků. Všechna písmena, která použijete, musí být malá.

Uložte hodnotu *loginServer* z výstupu, protože se používá v pozdějším kroku.

## <a name="create-a-service-principal-for-authentication"></a>Vytvoření instančního objektu pro ověřování

K vytvoření instančního objektu použijte [příkaz AZ AD SP Create-for-RBAC][az-ad-sp-create-for-rbac] . Například:

```cmd
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Výstup JSON uložte, protože se používá v pozdějším kroku.


K zobrazení *ID* clusteru AKS použijte [AZ AKS show][az-aks-show] :

```cmd
az aks show -g MyResourceGroup -n MyAKS  --query id
```

K zobrazení *ID* ACR použijte [AZ ACR show][az-acr-show] :

```cmd
az acr show --name <acrName> --query id
```

Pomocí [AZ role Assignment Create][az-role-assignment-create] můžete dát *přispěvateli* přístup ke clusteru AKS a *AcrPush* přístup k vašemu ACR.

```cmd
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Abyste měli instančnímu objektu přístup k těmto prostředkům, musíte být vlastníkem obou clusterů AKS i ACR.

## <a name="get-sample-application-code"></a>Získat ukázkový kód aplikace

V tomto článku se používá [ukázková aplikace Azure dev Spaces pro sdílení kol][bike-sharing-gh] k předvedení použití Azure dev Spaces s akcemi GitHubu.

Rozvětvení ukázkového úložiště Azure Dev Spaces a pak přejděte do rozvětvené úložiště. Klikněte na kartu *Akce* a vyberte, pokud chcete povolit akce pro toto úložiště.

Naklonujte své rozvětvené úložiště a přejděte do jeho adresáře:

```cmd
git clone https://github.com/USERNAME/dev-spaces
cd dev-spaces/samples/BikeSharingApp/
```

## <a name="retrieve-the-hostsuffix-for-dev"></a>Načtení HostSuffix pro *vývoj*

Pomocí příkazu `azds show-context` Zobrazte HostSuffix pro *vývoj*.

```cmd
$ azds show-context

Name                ResourceGroup     DevSpace  HostSuffix
------------------  ----------------  --------  -----------------------
MyAKS               MyResourceGroup   dev       fedcab0987.eus.azds.io
```

## <a name="update-the-helm-chart-with-your-hostsuffix"></a>Aktualizace grafu Helm pomocí HostSuffix

Otevřete [grafy/hodnoty. yaml][bike-sharing-values-yaml] a nahraďte všechny výskyty `<REPLACE_ME_WITH_HOST_SUFFIX>` hodnotou HostSuffix, kterou jste dříve načetli. Uložte změny a zavřete soubor.

## <a name="run-the-sample-application-in-kubernetes"></a>Spuštění ukázkové aplikace v Kubernetes

Příkazy pro spuštění ukázkové aplikace v Kubernetes jsou součástí stávajícího procesu a nemají žádné závislosti na Azure Dev Spaces nástrojů. V tomto případě je Helm nástroje, které slouží ke spuštění této ukázkové aplikace, ale jiné nástroje lze použít ke spuštění celé aplikace v oboru názvů v rámci clusteru. Příkazy Helm cílí na vývojové místo s názvem *vývoj* , který jste vytvořili dříve, ale toto vývojové místo je také oborem názvů Kubernetes. V důsledku toho mohou být vývojové prostory cíleny jinými nástroji, které jsou stejné jako jiné obory názvů.

Po spuštění aplikace v clusteru můžete použít Azure Dev Spaces pro vývoj bez ohledu na to, který nástroj používá k jeho nasazení.

K nastavení a instalaci ukázkové aplikace do clusteru použijte příkazy `helm init` a `helm install`.

```cmd
cd charts/
helm init --wait
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

> [!Note]
> **Pokud používáte cluster s podporou RBAC**, ujistěte se, že jste nakonfigurovali [účet služby pro pokladnu][tiller-rbac]. V opačném případě se příkazy `helm` nezdaří.

Dokončení příkazu `helm install` může trvat několik minut. Výstup příkazu zobrazuje stav všech služeb, které se v clusteru nasadily po dokončení:

```cmd
$ cd charts/
$ helm init --wait
...
Happy Helming!

$ helm install -n bikesharing . --dep-up --namespace dev --atomic

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

Po instalaci ukázkové aplikace do clusteru a vzhledem k tomu, že máte v clusteru povolené vývojové prostory, použijte příkaz `azds list-uris` a zobrazte tak adresy URL ukázkové aplikace v aktuálně vybraném *vývojovém* prostředí.

```cmd
$ azds list-uris
Uri                                                 Status
--------------------------------------------------  ---------
http://dev.bikesharingweb.fedcab0987.eus.azds.io/  Available
http://dev.gateway.fedcab0987.eus.azds.io/         Available
```

Přejděte do služby *bikesharingweb* tak, že otevřete veřejnou adresu URL z příkazu `azds list-uris`. Ve výše uvedeném příkladu je veřejná adresa URL pro službu *bikesharingweb* `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Jako uživatel vyberte *Aurelia Briggs (zákazník)* a pak vyberte kolo k pronajmutí. Ověřte, že se zobrazuje zástupný obrázek pro kolo.

## <a name="configure-your-github-action"></a>Konfigurace akce GitHubu

Přejděte do rozvětvené úložiště a klikněte na *Nastavení*. Na levém bočním panelu klikněte na *tajná klíčová* okna. Kliknutím na *Přidat nový tajný klíč* přidejte všechny nové tajné klíče:

1. *AZURE_CREDENTIALS*: celý výstup z vytváření objektu služby.
1. *RESOURCE_GROUP*: Skupina prostředků pro cluster AKS, což je v tomto příkladu *MyResourceGroup*.
1. *CLUSTER_NAME*: název vašeho clusteru AKS, který je v tomto příkladu *MyAKS*.
1. *CONTAINER_REGISTRY*: *loginServer* pro ACR.
1. *Hostitel*: hostitel pro vývojové místo, který má formu *< MASTER_SPACE >. < APP_NAME >. < HOST_SUFFIX >* , který je v tomto příkladu *dev.bikesharingweb.fedcab0987.EUS.azds.IO*.
1. *IMAGE_PULL_SECRET*: název tajného klíče, který chcete použít, například *ukázkový tajný klíč*.
1. *MASTER_SPACE*: název vašeho nadřazeného vývojového prostoru, který je v tomto příkladu *vývojem*.
1. *REGISTRY_USERNAME*: *CLIENTID* z výstupu JSON z vytváření objektu služby.
1. *REGISTRY_PASSWORD*: *CLIENTSECRET* z výstupu JSON z vytváření objektu služby.

> [!NOTE]
> Všechny tyto tajné kódy používá akce GitHub a jsou nakonfigurované v [. GitHub/Workflows/Bikes. yml][github-action-yaml].

## <a name="create-a-new-branch-for-code-changes"></a>Vytvořit novou větev pro změny kódu

Přejděte zpátky na `BikeSharingApp/` a vytvořte novou větev s názvem *Bikes-images*.

```cmd
cd ..
git checkout -b bike-images
```

Úpravou [Bikes/Server. js][bikes-server-js] odeberte řádky 232 a 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Oddíl by teď měl vypadat takto:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Uložte soubor a pak pomocí `git add` a `git commit` proveďte přípravu změn.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Vložení změn

Pro vložení nové větve do rozvětvené úložiště použijte `git push`:

```cmd
git push origin bike-images
```

Po dokončení nahrávání přejděte do rozvětvené úložiště na GitHubu a vytvořte žádost o přijetí změn s *Hlavní* větví ve vašem rozvětvené úložišti jako základní větev v porovnání s větví *Bike-images* .

Po otevření žádosti o přijetí změn přejděte na kartu *Akce* . Ověřte, zda byla spuštěna nová akce a sestavuje službu *Bikes* .

## <a name="view-the-child-space-with-your-changes"></a>Zobrazení podřízeného prostoru vašimi změnami

Po dokončení akce se zobrazí komentář s adresou URL nového podřízeného prostoru na základě změn v žádosti o získání dat.

> [!div class="mx-imgBorder"]
> Adresa URL akce GitHubu ![](../media/github-actions/github-action-url.png)

Přejděte do služby *bikesharingweb* tak, že otevřete adresu URL z komentáře. Jako uživatel vyberte *Aurelia Briggs (zákazník)* a pak vyberte kolo k pronajmutí. Ověřte, že se už nezobrazuje zástupný obrázek pro kolo.

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak zjednodušit vývoj díky práci s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Vývoj pro tým v Azure Dev Spaces][team-quickstart]

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[tiller-rbac]: https://helm.sh/docs/using_helm/#role-based-access-control
[supported-regions]: ../about.md#supported-regions-and-configurations
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md