---
title: Akce GitHubu & službu Azure Kubernetes (preview)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Kontrola a testování změn z žádosti o přijetí změn přímo ve službě Azure Kubernetes pomocí akcí GitHub a Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, Akce GitHub, Helm, síť služeb, směrování sítě služeb, kubectl, k8s
manager: gwallace
ms.openlocfilehash: a83da0ef3958748831eb0eeda1aa5e91efa7ef2e
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637949"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Akce GitHubu & službu Azure Kubernetes (preview)

Azure Dev Spaces poskytuje pracovní postup pomocí akce GitHub, který umožňuje testovat změny z žádosti o přijetí změn přímo v AKS před sloučením žádosti o přijetí změn do hlavní větve vašeho úložiště. Spuštění aplikace pro kontrolu změn žádosti o přijetí změn může zvýšit důvěru vývojáře i členů týmu. Tato spuštěná aplikace může také pomoci členům týmu, jako jsou produktoví manažeři a návrháři, stát se součástí procesu kontroly v raných fázích vývoje.

V tomto průvodci se naučíte:

* Nastavte Azure Dev Spaces ve spravovaném clusteru Kubernetes v Azure.
* Nasazení velké aplikace s více mikroslužeb do prostoru pro vývoj.
* Nastavte CI/CD s akcemi GitHubu.
* Otestujte jednu mikroslužbu v izolovaném vývojovém prostoru v kontextu úplné aplikace.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free).
* [Nainstalované rozhraní Azure CLI][azure-cli-installed]
* [Helm 3 nainstalován][helm-installed].
* Účet GitHub s [povolenými akcemi GitHubu][github-actions-beta-signup].
* [Ukázková aplikace Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) spuštěná v clusteru AKS.

## <a name="create-an-azure-container-registry"></a>Vytvoření služby Azure Container Registry

Vytvoření registru kontejnerů Azure (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Název, který acr musí být jedinečný v rámci Azure a obsahovat 5-50 alfanumerických znaků. Všechna písmena, která používáte, musí být malá písmena.

Uložte hodnotu *loginServer* z výstupu, protože se používá v pozdějším kroku.

## <a name="create-a-service-principal-for-authentication"></a>Vytvoření instančního objektu pro ověřování

K vytvoření instančního objektu použijte [az ad sp create-for-rbac.][az-ad-sp-create-for-rbac] Například:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Uložte výstup JSON, protože se používá v pozdějším kroku.

Pomocí [az aks show][az-aks-show] zobrazte *ID* clusteru AKS:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Pomocí [az acr show][az-acr-show] zobrazte *ID* ACR:

```azurecli
az acr show --name <acrName> --query id
```

Pomocí [vytvoření přiřazení role az][az-role-assignment-create] udělte *přispěvateli* přístup k vašemu clusteru AKS a *acrpush* přístup k vašemu ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Chcete-li poskytnout k těmto prostředkům přístup k objektu zabezpečení služby, musíte být vlastníkem clusteru AKS i ACR.

## <a name="configure-your-github-action"></a>Konfigurace akce GitHubu

> [!IMPORTANT]
> Pro vaše úložiště musíte mít povolené akce GitHubu. Chcete-li povolit akce GitHubu pro vaše úložiště, přejděte do svého úložiště na GitHubu, klikněte na kartu Akce a zvolte povolení akcí pro toto úložiště.

Přejděte do rozteč úložiště a klepněte na *nastavení*. Klikněte na *Tajemství* v levém postranním panelu. Kliknutím na *Přidat nový tajný klíč* přidáte každý nový tajný klíč níže:

1. *AZURE_CREDENTIALS*: celý výstup z vytvoření instančního objektu.
1. *RESOURCE_GROUP*: skupina prostředků pro cluster AKS, která je v tomto příkladu *MyResourceGroup*.
1. *CLUSTER_NAME*: název clusteru AKS, který je v tomto příkladu *MyAKS*.
1. *CONTAINER_REGISTRY*: *loginServer* pro ACR.
1. *HOST*: hostitel pro vaše funkce Dev Space, která má formu *<MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX>*, který je v tomto příkladu *dev.bikesharingweb.fedcab0987.eus.azds.io*.
1. *IMAGE_PULL_SECRET*: název tajného klíče, který chcete použít, například *demo-secret*.
1. *MASTER_SPACE*: název nadřazeného dev prostoru, který je v tomto *příkladu dev*.
1. *REGISTRY_USERNAME*: *ClientId* z výstupu JSON z vytvoření instančního objektu.
1. *REGISTRY_PASSWORD*: *clientSecret* z výstupu JSON z vytvoření instančního objektu.

> [!NOTE]
> Všechny tyto tajné klíče jsou používány akcí GitHub a jsou konfigurovány v [.github/workflows/bikes.yml][github-action-yaml].

Volitelně pokud chcete aktualizovat hlavní prostor po sloučení pr, přidejte *tajný* GATEWAY_HOST, který má formulář *<MASTER_SPACE>.gateway.<HOST_SUFFIX>*, který je v tomto příkladu *dev.gateway.fedcab0987.eus.azds.io*. Po sloučení změn do hlavní větve v rozvětvení se spustí další akce, která obnoví a spustí celou aplikaci v hlavním prostoru pro vývoj. V tomto příkladu je hlavní prostor *dev*. Tato akce je konfigurována v [souboru .github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

Navíc pokud chcete, aby změny v PR spustit v nouzi prostoru, aktualizujte *MASTER_SPACE* a *HOST* tajných kódů. Například pokud vaše aplikace běží ve *vývoji* s podřízeným prostoru *dev/azureuser1*, aby pr spustit v podřízeném prostoru *dev/azureuser1*:

* Aktualizace *MASTER_SPACE* podřízeného prostoru, který chcete jako nadřazený prostor, v tomto *příkladu azureuser1*.
* V tomto příklad <> <> GRANDPARENT_SPACE *<u* *dev.bikesharingweb.fedcab0987.eus.azds.io dev.bikesharingweb.fedcab0987.eus.azds.io*APP_NAME *APP_NAME APP_NAME APP_NAME HOST_SUFFIX APP_NAME>*.

## <a name="create-a-new-branch-for-code-changes"></a>Vytvoření nové větve pro změny kódu

Přejděte `BikeSharingApp/` na novou větev nazvanou *bike-images*a vytvořte ji .

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Chcete-li odebrat řádky 232 a 233, upravte [soubor Bikes/server.js:][bikes-server-js]

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Sekce by nyní měla vypadat takto:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Uložte soubor `git add` a `git commit` potom použijte a zinscenujte změny.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Posuňte změny

Použijte `git push` k přetlačení nové větve do rozvětveného úložiště:

```cmd
git push origin bike-images
```

Po dokončení nabízení přejděte do rozvětveného úložiště na GitHubu a vytvořte žádost o přijetí vzato s *hlavní* větví v rozvětveném úložišti jako základní větev ve srovnání s větví *image kola.*

Po otevření žádosti o přijetí vyžádat, přejděte na kartu *Akce.* Ověřte, zda byla zahájena nová akce a vytváří službu *Bikes.*

## <a name="view-the-child-space-with-your-changes"></a>Zobrazení podřízeného prostoru se změnami

Po dokončení akce se zobrazí komentář s adresou URL k novému podřízenému prostoru založený na změnách v žádosti o přijetí změn.

> [!div class="mx-imgBorder"]
> ![Adresa url akce GitHub](../media/github-actions/github-action-url.png)

Přejděte na službu *bikesharingweb* otevřením adresy URL z komentáře. Vyberte *Aurelia Briggs (zákazník)* jako uživatel, pak vyberte kolo k pronájmu. Ověřte, zda již nevidíte zástupný obrázek kola.

Pokud sloučíte změny do *hlavní* větve v rozvětvení, spustí se další akce, která obnoví a spustí celou aplikaci v nadřazeném prostoru pro vývoj. V tomto příkladu je nadřazená mezera *dev*. Tato akce je konfigurována v [souboru .github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Vyčištění prostředků Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Další kroky

Zjistěte, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak můžete zjednodušit vývoj spolupráce pomocí práce s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Vývoj týmu v Azure Dev Spaces][team-quickstart]

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
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md
