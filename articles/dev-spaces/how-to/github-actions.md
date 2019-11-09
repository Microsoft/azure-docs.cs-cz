---
title: Akce GitHubu & službě Azure Kubernetes
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 11/04/2019
ms.topic: conceptual
description: Zkontrolujte a otestujte změny z žádosti o přijetí změn přímo ve službě Azure Kubernetes pomocí akcí GitHubu a Azure Dev Spaces.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, akce GitHubu, Helm, síť pro služby, směrování sítě pro služby, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 09dc9440628ac5d808f90d086bd88e4f90765c28
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889736"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Akce GitHubu & službě Azure Kubernetes (Preview)

Azure Dev Spaces poskytuje pracovní postup pomocí akcí GitHubu, který umožňuje testovat změny z žádosti o přijetí změn přímo v AKS před sloučením žádosti o přijetí změn do hlavní větve vašeho úložiště. Spuštění aplikace, která bude kontrolovat změny žádosti o přijetí změn, může zvýšit spolehlivost vývojářů i členů týmu. Tato spuštěná aplikace může také pomáhat členům týmu, jako je, manažerům a návrhářům produktů, tvořit součást procesu revize během počátečních fází vývoje.

V tomto průvodci se naučíte:

* Nastavte Azure Dev Spaces ve spravovaném clusteru Kubernetes v Azure.
* Nasaďte rozsáhlou aplikaci s více mikroslužbami do vývojového prostoru.
* Nastavte CI/CD s akcemi GitHubu.
* Otestujte jednu mikroslužbu v izolovaném prostoru pro vývoj v rámci celé aplikace.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
* [Nainstalované rozhraní Azure CLI][azure-cli-installed]
* [Nainstalovaná Helm 2,13 nebo novější][helm-installed].
* Účet GitHub s [povolenými akcemi GitHubu][github-actions-beta-signup].
* [Ukázková aplikace Azure dev Spaces pro sdílení kol](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) spuštěná v clusteru AKS.

## <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry

Vytvořit Azure Container Registry (ACR):

```cmd
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Název ACR musí být v rámci Azure jedinečný a obsahovat 5-50 alfanumerických znaků. Všechna písmena, která použijete, musí být malá.

Uložte hodnotu *loginServer* z výstupu, protože se používá v pozdějším kroku.

## <a name="create-a-service-principal-for-authentication"></a>Vytvoření instančního objektu pro ověřování

K vytvoření instančního objektu použijte [příkaz AZ AD SP Create-for-RBAC][az-ad-sp-create-for-rbac] . Příklad:

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

## <a name="configure-your-github-action"></a>Konfigurace akce GitHubu

> [!IMPORTANT]
> Musíte mít povolené akce GitHubu pro vaše úložiště. Pokud chcete pro své úložiště povolit akce GitHubu, přejděte do úložiště na GitHubu, klikněte na kartu akce a vyberte, pokud chcete povolit akce pro toto úložiště.

Přejděte do rozvětvené úložiště a klikněte na *Nastavení*. Na levém bočním panelu klikněte na *tajná klíčová* okna. Kliknutím na *Přidat nový tajný klíč* přidejte všechny nové tajné klíče:

1. *AZURE_CREDENTIALS*: celý výstup z vytváření instančního objektu.
1. *RESOURCE_GROUP*: Skupina prostředků pro cluster AKS, což je v tomto příkladu *MyResourceGroup*.
1. *CLUSTER_NAME*: název clusteru AKS, který je v tomto příkladu *MyAKS*.
1. *CONTAINER_REGISTRY*: *loginServer* pro ACR.
1. *Hostitel*: hostitel pro vývojové místo, který má formu *< MASTER_SPACE >. < APP_NAME >. < HOST_SUFFIX >* , který v tomto příkladu je *dev.bikesharingweb.fedcab0987.EUS.azds.IO*.
1. *HOST_SUFFIX*: přípona hostitele pro místo pro vývoj, která je v tomto příkladu *fedcab0987.EUS.azds.IO*.
1. *IMAGE_PULL_SECRET*: název tajného klíče, který chcete použít, například *ukázkový tajný klíč*.
1. *MASTER_SPACE*: název vašeho nadřazeného vývojového prostoru, který je v tomto příkladu *vývojem*.
1. *REGISTRY_USERNAME*: *CLIENTID* z výstupu JSON z vytváření objektu služby.
1. *REGISTRY_PASSWORD*: *CLIENTSECRET* z výstupu JSON z vytváření instančního objektu.

> [!NOTE]
> Všechny tyto tajné kódy používá akce GitHub a jsou nakonfigurované v [. GitHub/Workflows/Bikes. yml][github-action-yaml].

## <a name="create-a-new-branch-for-code-changes"></a>Vytvořit novou větev pro změny kódu

Přejděte na `BikeSharingApp/` a vytvořte novou větev s názvem *Bikes-images*.

```cmd
cd dev-spaces/samples/BikeSharingApp/
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

Pokud vaše změny sloučíte do *Hlavní* větve ve svém rozvětvení, spustí se další akce, která znovu sestaví a spustí celou aplikaci v nadřazeném vývojovém prostoru. V tomto příkladu je nadřazená oblast určena pro *vývoj*. Tato akce je nakonfigurovaná ve [. GitHub/Workflows/bikesharing. yml][github-action-bikesharing-yaml].

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
