---
title: Uložení Helm grafů
description: Naučte se ukládat Helm grafy pro aplikace Kubernetes pomocí úložišť v Azure Container Registry
ms.topic: article
ms.date: 06/12/2020
ms.openlocfilehash: 9897ed6e43813c16314076b0322cd263cd2ed150
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223077"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Vložení a vyžádání Helm grafů do služby Azure Container Registry

Pokud chcete rychle spravovat a nasazovat aplikace pro Kubernetes, můžete použít [Správce balíčků Open Source Helm][helm]. Pomocí Helm jsou balíčky aplikací definované jako [grafy](https://helm.sh/docs/topics/charts/), které se shromažďují a ukládají do [úložiště grafu Helm](https://helm.sh/docs/topics/chart_repository/).

V tomto článku se dozvíte, jak hostovat úložiště Helm grafů v registru kontejnerů Azure pomocí příkazů Helm 3. V mnoha scénářích byste sestavili a nahráli vlastní grafy pro aplikace, které vyvíjíte. Další informace o tom, jak vytvářet vlastní grafy Helm, najdete v [příručce pro vývojáře šablony grafu][develop-helm-charts]. Existující graf Helm můžete také uložit z jiného úložiště Helm.

> [!IMPORTANT]
> Podpora Helm grafů v Azure Container Registry je v současnosti ve verzi Preview. Verze Preview jsou k dispozici na základě podmínky, že souhlasíte s doplňkovými [podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="helm-3-or-helm-2"></a>Helm 3 nebo Helm 2?

K ukládání, správě a instalaci Helm grafů použijte klienta Helm a rozhraní příkazového řádku Helm. Hlavní verze klienta Helm zahrnují Helm 3 a Helm 2. Podrobnosti o rozdílech verze najdete v tématu [Nejčastější dotazy k verzi](https://helm.sh/docs/faq/). 

Helm 3 by se měly použít k hostování Helm grafů v Azure Container Registry. S Helm 3 jste:

* Může vytvořit jedno nebo víc úložišť Helm ve službě Azure Container Registry.
* Uložte Helm 3 grafy do registru jako [artefakty OCI](container-registry-image-formats.md#oci-artifacts). V současné době je podpora Helm 3 pro OCI *experimentální*.
* Pomocí příkazu proveďte ověření pomocí vašeho registru `helm registry login` .
* Použití `helm chart` příkazů v rozhraní příkazového řádku Helm k vložení, vyžádání a správě Helm grafů v registru
* Použijte `helm install` k instalaci grafů do clusteru Kubernetes z místní mezipaměti úložiště.
> [!NOTE]
> Od Helm 3 se nepoužívají příkazy [AZ ACR Helm][az-acr-helm] pro použití s klientem Helm 2. Před odebráním příkazu bude k dispozici pouze oznámení o třech měsících. Pokud jste již dříve nasadili grafy Helm 2, přečtěte si téma [migrace Helm v2 na V3](https://helm.sh/docs/topics/v2_v3_migration/).

## <a name="prerequisites"></a>Požadavky

Pro scénář v tomto článku jsou potřeba tyto prostředky:

- **Registr kontejnerů Azure** ve vašem předplatném Azure. V případě potřeby vytvořte registr pomocí [Azure Portal](container-registry-get-started-portal.md) nebo [Azure CLI](container-registry-get-started-azure-cli.md).
- **Helm Client verze 3.1.0 nebo novější** – spusťte `helm version` , abyste našli aktuální verzi. Další informace o tom, jak nainstalovat a upgradovat Helm, najdete v tématu [instalace Helm][helm-install].
- **Cluster Kubernetes, do** kterého budete instalovat graf Helm. V případě potřeby vytvořte [cluster služby Azure Kubernetes][aks-quickstart]. 
- Verze **Azure CLI 2.0.71 nebo novější** – spuštění, `az --version` aby se našela verze. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="enable-oci-support"></a>Povolit podporu OCI

Pomocí `helm version` příkazu ověřte, že máte nainstalovanou Helm 3:

```console
helm version
```

Nastavte následující proměnnou prostředí pro povolení podpory rozhraní OCI v klientovi Helm 3. V současné době je tato podpora experimentální. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>Vytvoření ukázkového grafu

Pomocí následujících příkazů vytvořte testovací graf:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Jako základní příklad změňte adresář na `templates` složku a nejprve odstraňte obsah:

```console
cd hello-world/templates
rm -rf *
```

Ve `templates` složce vytvořte soubor s názvem `configmap.yaml` tak, že spustíte následující příkaz:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Další informace o vytvoření a spuštění tohoto příkladu naleznete v tématu [Začínáme](https://helm.sh/docs/chart_template_guide/getting_started/) v dokumentaci Helm.

## <a name="save-chart-to-local-registry-cache"></a>Uložit graf do místní mezipaměti registru

Změňte adresář na podadresář `hello-world` . Pak spusťte příkaz `helm chart save` pro uložení kopie grafu místně a také vytvořte alias s plně kvalifikovaným názvem registru (všechny malé písmeno) a cílovým úložištěm a označením. 

V následujícím příkladu je název registru *mycontainerregistry*, cílové úložiště je *Hello World* a jmenovka cílového grafu je *v1*, ale nahrazující hodnoty pro vaše prostředí:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Spusťte příkaz `helm chart list` a potvrďte tak, že jste uložili grafy do místní mezipaměti registru. Výstup se podobá tomuto:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>Ověřování pomocí registru

Spusťte `helm registry login` příkaz v rozhraní příkazového řádku Helm 3 pro [ověření](container-registry-authentication.md) pomocí přihlašovacích údajů, které jsou vhodné pro váš scénář.

Můžete například vytvořit [instanční objekt Azure Active Directory s oprávněním Pull a push](container-registry-auth-service-principal.md#create-a-service-principal) (role AcrPush) do registru. Pak zadejte přihlašovací údaje instančního objektu do služby `helm registry login` . Následující příklad dodá heslo pomocí proměnné prostředí:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>Odeslat graf do registru

Spusťte `helm chart push` příkaz v rozhraní příkazového řádku Helm 3 a nahrajte graf do plně kvalifikovaného cílového úložiště:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Po úspěšném vložení se výstup podobá:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>Seznam grafů v úložišti

Stejně jako u imagí uložených v Azure Container Registry můžete pomocí příkazu [AZ ACR úložiště][az-acr-repository] zobrazit úložiště hostující vaše grafy a značky a manifesty grafů. 

Například spuštěním [AZ ACR úložiště show][az-acr-repository-show] zobrazíte vlastnosti úložiště, které jste vytvořili v předchozím kroku:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

Výstup se podobá tomuto:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Spuštěním příkazu [AZ ACR úložiště show-Manifests][az-acr-repository-show-manifests] zobrazíte podrobnosti o grafu, který je uložený v úložišti. Například:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Výstup, zkrácený v tomto příkladu, ukazuje na `configMediaType` `application/vnd.cncf.helm.config.v1+json` :

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

## <a name="pull-chart-to-local-cache"></a>Načíst graf do místní mezipaměti

Pro instalaci grafu Helm do Kubernetes musí být graf v místní mezipaměti. V tomto příkladu se nejdřív spustí `helm chart remove` Odebrání existujícího místního grafu s názvem `mycontainerregistry.azurecr.io/helm/hello-world:v1` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Spusťte `helm chart pull` pro stažení grafu ze služby Azure Container Registry do místní mezipaměti:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>Exportovat graf Helm

Pokud chcete s grafem pracovat dál, exportujte ho do místního adresáře pomocí `helm chart export` . Exportujte například graf, který jste vyžádali do tohoto `install` adresáře:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Chcete-li zobrazit informace pro exportovaný graf v úložišti, spusťte `helm show chart` příkaz v adresáři, do kterého jste graf exportovali.

```console
cd install
helm show chart hello-world
```

Helm vrátí podrobné informace o nejnovější verzi grafu, jak je znázorněno v následujícím ukázkovém výstupu:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>Nainstalovat graf Helm

Spusťte `helm install` pro instalaci grafu Helm, který jste si vyžádali do místní mezipaměti a exportovali. Zadejte název verze, například *myhelmtest*, nebo předejte `--generate-name` parametr. Například:

```console
helm install myhelmtest ./hello-world
```

Výstup po úspěšné instalaci grafu je podobný následujícímu:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Chcete-li ověřit instalaci, spusťte `helm get manifest` příkaz. 

```console
helm get manifest myhelmtest
```

Příkaz vrátí data YAML v `configmap.yaml` souboru šablony.

Spusťte `helm uninstall` pro odinstalaci vydaných verzí grafu ve vašem clusteru:

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Odstranit graf z registru

Chcete-li odstranit graf z registru kontejneru, použijte příkaz [AZ ACR úložiště Delete][az-acr-repository-delete] . Spusťte následující příkaz a po zobrazení výzvy potvrďte operaci:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak vytvořit a nasadit grafy Helm, najdete v tématu [vývoj Helm grafů][develop-helm-charts].
* Přečtěte si další informace o instalaci aplikací pomocí Helm ve [službě Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md).
* Grafy Helm lze použít jako součást procesu sestavení kontejneru. Další informace najdete v tématu [použití Azure Container Registry úloh][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[acr-tasks]: container-registry-tasks-overview.md
