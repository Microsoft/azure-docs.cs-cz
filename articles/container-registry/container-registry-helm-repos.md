---
title: Uložit grafy helmu
description: Zjistěte, jak ukládat grafy Helm pro vaše aplikace Kubernetes pomocí úložišť v registru kontejnerů Azure
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 6304486ac493e235ed74f26ab4be4f843ef52513
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131484"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Nabízení a vytahováno grafy Helm do registru kontejnerů Azure

Chcete-li rychle spravovat a nasazovat aplikace pro Kubernetes, můžete použít [správce balíčků Helm s otevřeným zdrojovým kódem][helm]. S Helm, balíčky aplikací jsou [definovány](https://helm.sh/docs/topics/charts/)jako grafy , které jsou shromažďovány a uloženy v [úložišti grafu Helm](https://helm.sh/docs/topics/chart_repository/).

Tento článek ukazuje, jak hostovat úložiště grafů Helm v registru kontejnerů Azure pomocí instalace Helm 3 nebo Helm 2. V mnoha scénářích byste vytvořit a nahrát vlastní grafy pro aplikace, které vyvíjíte. Další informace o tom, jak vytvořit vlastní grafy helmu, naleznete v [průvodci pro vývojáře šablon grafu][develop-helm-charts]. Můžete také uložit existující graf helmu z jiného úložiště helmu.

> [!IMPORTANT]
> Podpora grafů Helm v Azure Container Registry je momentálně ve verzi Preview. Náhledy jsou vám k dispozici pod podmínkou, že souhlasíte s [doplňkovými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="helm-3-or-helm-2"></a>Kormidlo 3 nebo Kormidlo 2?

Chcete-li ukládat, spravovat a instalovat helm grafy, použijte helm klienta a helm ucli. Mezi hlavní verze helmu klienta patří Helm 3 a Helm 2. Helm 3 podporuje nový formát grafu a již neinstaluje součást na straně serveru Tiller. Podrobnosti o rozdílech verzí naleznete v [nejčastějších dotazech](https://helm.sh/docs/faq/)k verzi . Pokud jste dříve nasadili grafy Helm 2, přečtěte si informace [o migraci helmu v2 na 3](https://helm.sh/docs/topics/v2_v3_migration/).

Helm 3 nebo Helm 2 můžete použít k hostování grafů helmu v Azure Container Registry, s pracovními postupy specifickými pro každou verzi:

* [Helm 3](#use-the-helm-3-client) klient `helm chart` - použití příkazů v Helm CLI pro správu grafů v registru jako [artefakty OCI](container-registry-image-formats.md#oci-artifacts)
* [Helm 2 client](#use-the-helm-2-client) – použití příkazů [az acr helm][az-acr-helm] v azure CLI přidat a spravovat registr kontejnerů jako úložiště grafu Helm

### <a name="additional-information"></a>Další informace

* Pro většinu scénářů doporučujeme použít pracovní `helm chart` postup Helm 3 s nativními příkazy pro správu grafů jako artefaktů OCI.
* Můžete použít starší [az acr helm][az-acr-helm] azure cli příkazy a pracovní postup s Helm 3 klienta a grafy. Některé příkazy však `az acr helm list` nejsou kompatibilní s grafy Helm 3.
* Od Helm 3 jsou příkazy [az acr helm][az-acr-helm] podporovány hlavně pro kompatibilitu s klientem Helm 2 a formátem grafu. Budoucí vývoj těchto příkazů není v současné době plánováno.

## <a name="use-the-helm-3-client"></a>Použití klienta Helm 3

### <a name="prerequisites"></a>Požadavky

- **Registr kontejnerů Azure** ve vašem předplatném Azure. V případě potřeby vytvořte registr pomocí [portálu Azure nebo](container-registry-get-started-portal.md) [azure cli](container-registry-get-started-azure-cli.md).
- **Helm klient verze 3.1.0** `helm version` nebo novější - Spustit najít aktuální verzi. Další informace o instalaci a upgradu helmu naleznete v [tématu Instalace helmu][helm-install].
- **Kubernetes cluster,** kde budete instalovat Helm graf. V případě potřeby vytvořte [cluster služby Azure Kubernetes][aks-quickstart]. 
- **Azure CLI verze 2.0.71** `az --version` nebo novější – Spuštěním najdete verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

### <a name="high-level-workflow"></a>Pracovní postup na vysoké úrovni

S **helmem 3:**

* Můžete vytvořit jeden nebo více úložišť Helm v registru kontejnerů Azure
* Uložte grafy Helm 3 do registru jako [artefakty OCI](container-registry-image-formats.md#oci-artifacts). V současné době je helm 3 podpora pro OCI *experimentální*.
* Ověřte pomocí `helm registry login` registru pomocí příkazu.
* Použití `helm chart` příkazů v příkazovém příkazu helmu k nabízení, vytahování a správě grafů helmu v registru
* Slouží `helm install` k instalaci grafů do clusteru Kubernetes z mezipaměti místního úložiště.

Příklady naleznete v následujících částech.

### <a name="enable-oci-support"></a>Povolení podpory OCI

Nastavte následující proměnnou prostředí, abyste povolili podporu OCI v klientovi Helm 3. V současné době je tato podpora experimentální. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>Vytvoření ukázkového grafu

Vytvořte testovací graf pomocí následujících příkazů:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Jako základní příklad změňte `templates` adresář do složky a nejprve odstraňte obsah:

```console
rm -rf *
```

Ve `templates` složce vytvořte `configmap.yaml` soubor s následujícím obsahem:

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
```

Další informace o vytváření a spouštění tohoto příkladu najdete v [tématu Začínáme](https://helm.sh/docs/chart_template_guide/getting_started/) v dokumentech helmu.

### <a name="save-chart-to-local-registry-cache"></a>Uložení grafu do mezipaměti místního registru

Změňte adresář `hello-world` do podadresáře. Potom spusťte `helm chart save` uložit kopii grafu místně a také vytvořit alias s plně kvalifikovaný název registru (všechna malá písmena) a cílové úložiště a značky. 

V následujícím příkladu je název registru *mycontainerregistry*, cílové ukládání je *hello-world*a cílová značka grafu je *v1*, ale nahradit hodnoty pro vaše prostředí:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Spuštěním `helm chart list` potvrďte, že jste grafy uložili do mezipaměti místního registru. Výstup se podobá tomuto:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>Ověření pomocí registru

Spusťte `helm registry login` příkaz v helmu 3 CLI k [ověření pomocí registru](container-registry-authentication.md) pomocí pověření vhodných pro váš scénář.

Můžete například vytvořit objekt zabezpečení služby Azure Active Directory [s oprávněními pro vyžádat a vyžádat](container-registry-auth-service-principal.md#create-a-service-principal) (role AcrPush) do registru. Potom zadejte pověření zaregistrovaný objekt služby . `helm registry login` Následující příklad zadá heslo pomocí proměnné prostředí:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Nabízený graf do registru kontejnerů Azure

Spusťte `helm chart push` příkaz v příkazu příkazu příkazu Helm 3 pro posun grafu do plně kvalifikovaného cílového úložiště:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Po úspěšném push, výstup je podobný:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>Seznam grafů v úložišti

Stejně jako u bitových kopií uložených v registru kontejnerů Azure můžete pomocí příkazů [úložiště az acr][az-acr-repository] zobrazit úložiště hostující vaše grafy a značky grafů a manifesty. 

Chcete-li například zobrazit vlastnosti úložiště, které jste vytvořili v předchozím kroku, spusťte [az acr repo:][az-acr-repository-show]

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

Spusťte příkaz [az acr repository show-manifests,][az-acr-repository-show-manifests] abyste viděli podrobnosti o grafu uloženém v úložišti. Například:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Výstup, zkráceně v tomto příkladu, `configMediaType` `application/vnd.cncf.helm.config.v1+json`ukazuje a z :

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

### <a name="pull-chart-to-local-cache"></a>Vyžádat graf do místní mezipaměti

Chcete-li nainstalovat graf Helm do Kubernetes, musí být graf v místní mezipaměti. V tomto příkladu `helm chart remove` nejprve spusťte `mycontainerregistry.azurecr.io/helm/hello-world:v1`odebrání existujícího místního grafu s názvem :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Spuštěním `helm chart pull` stáhnete graf z registru kontejnerů Azure do místní mezipaměti:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>Graf kormene exportu

Chcete-li s grafem dále pracovat, `helm chart export`exportujte jej do místního adresáře pomocí aplikace . Exportujte například vytažený graf `install` do adresáře:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Chcete-li zobrazit informace pro exportovaný graf `helm show chart` v repo, spusťte příkaz v adresáři, ve kterém jste graf exportovali.

```console
cd install
helm show chart hello-world
```

Helm vrátí podrobné informace o nejnovější verzi grafu, jak je znázorněno na následujícím ukázkovém výstupu:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>Instalace grafu helmu

Spusťte `helm install` instalaci grafu Helm, který jste vytáhli do místní mezipaměti a exportovali. Zadejte název verze, například *myhelmtest*, nebo předajte `--generate-name` parametr. Například:

```console
helm install myhelmtest ./hello-world
```

Výstup po úspěšné instalaci grafu je podobný:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Chcete-li ověřit instalaci, spusťte `helm get manifest` příkaz. Příkaz vrátí data YAML `configmap.yaml` v souboru šablony.

Spuštěním `helm uninstall` odinstalace verze grafu v clusteru:

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Odstranění grafu Helm z úložiště

Chcete-li odstranit graf z úložiště, použijte příkaz [delete úložiště az acr.][az-acr-repository-delete] Spusťte následující příkaz a po zobrazení výzvy potvrďte operaci:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>Použití klienta Helm 2

### <a name="prerequisites"></a>Požadavky

- **Registr kontejnerů Azure** ve vašem předplatném Azure. V případě potřeby vytvořte registr pomocí [portálu Azure nebo](container-registry-get-started-portal.md) [azure cli](container-registry-get-started-azure-cli.md).
- **Helm klient verze 2.11.0 (není RC verze) nebo novější** - Spustit `helm version` najít aktuální verzi. Potřebujete také server Helm (Tiller) inicializovaný v rámci clusteru Kubernetes. V případě potřeby vytvořte [cluster služby Azure Kubernetes][aks-quickstart]. Další informace o instalaci a upgradu helmu naleznete v [tématu Instalace helmu][helm-install-v2].
- **Azure CLI verze 2.0.46** `az --version` nebo novější – Spuštěním najdete verzi. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

### <a name="high-level-workflow"></a>Pracovní postup na vysoké úrovni

S **Helm2** můžete:

* Nakonfigurujte registr kontejnerů Azure jako *jedno* úložiště grafů Helm. Azure Container Registry spravuje definici indexu při přidávání a odebírání grafů do úložiště.
* Ověřte pomocí registru kontejnerů Azure pomocí příkazového příkazu k příkazu Azure, který pak automaticky aktualizuje klienta Helm pomocí identifikátoru URI registru a přihlašovacích údajů. Není nutné ručně zadat tyto informace registru, takže pověření nejsou vystaveny v historii příkazů.
* Pomocí příkazů [az acr helm][az-acr-helm] v Azure CLI přidejte registr kontejnerů Azure jako úložiště grafů Helm a nabízení a spravovat grafy. Tyto příkazy Azure CLI zabalit Helm 2 klientské příkazy.
* Přidejte úložiště grafů v registru kontejnerů Azure do místního indexu úložiště helmy, který podporuje vyhledávání v grafu.
* Slouží `helm install` k instalaci grafů do clusteru Kubernetes z mezipaměti místního úložiště.

Příklady naleznete v následujících částech.

### <a name="add-repository-to-helm-client"></a>Přidání úložiště do klienta Helm

Přidejte úložiště grafu Helm registru registru Azure do klienta Helm pomocí příkazu [az acr helm repo add.][az-acr-helm-repo-add] Tento příkaz získá ověřovací token pro váš registr kontejneru Azure, který používá klient Helm. Ověřovací token je platný po dobu 3 hodin. Podobně `docker login`jako v aplikaci můžete tento příkaz spustit v budoucích relacích příkazového příkazu k ověření klienta Helm pomocí úložiště grafu Helm registru registru Azure:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>Přidání ukázkového grafu do úložiště

Za prvé, vytvořit místní adresář na *~ / acr-helm*, pak stáhnout stávající *stabilní / wordpress* graf:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Zadejte `ls` seznam staženého grafu a všimněte si verze Wordpress obsažené v názvu souboru. Příkaz `helm fetch stable/wordpress` nespecifikoval konkrétní verzi, takže byla načtena *nejnovější* verze. V následujícím příkladu výstupu, Wordpress graf je verze *8.1.0*:

```output
wordpress-8.1.0.tgz
```

Posuňte graf do úložiště grafu Helm v azure kontejneru registru pomocí příkazu [az acr helm push][az-acr-helm-push] v Azure CLI. Zadejte název grafu Helm staženého v předchozím kroku, například *wordpress-8.1.0.tgz*:

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Po několika okamžicích azure CLI hlásí, že váš graf je uložen, jak je znázorněno na následujícím příkladu výstupu:

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Seznam grafů v úložišti

Chcete-li použít graf nahraný v předchozím kroku, musí být aktualizován místní index úložiště helmy. Můžete přeindexovat úložiště v klientovi Helm nebo použít Azure CLI k aktualizaci indexu úložiště. Při každém přidání grafu do úložiště je nutné tento krok dokončit:

```azurecli
az acr helm repo add --name mycontainerregistry
```

S grafem uloženým v úložišti a aktualizovaným indexem, který je k dispozici místně, můžete k vyhledávání nebo instalaci použít běžné klientské příkazy Helm. Chcete-li zobrazit všechny grafy v `helm search` úložišti, použijte příkaz a zadejte vlastní název registru kontejnerů Azure:

```console
helm search mycontainerregistry
```

Tabulka Wordpress posunutá v předchozím kroku je uvedena, jak je znázorněno v následujícím příkladu výstupu:

```output
NAME                  CHART VERSION    APP VERSION    DESCRIPTION
helmdocs/wordpress    8.1.0           5.3.2          Web publishing platform for building blogs and websites.
```

Grafy můžete také uvést pomocí příkazového příkazu k příkazu Kont [akreditivu Azure pomocí seznamu kormidelník az acr][az-acr-helm-list]:

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Zobrazení informací pro graf helmu

Chcete-li zobrazit informace pro konkrétní graf v `helm inspect` repo, můžete použít příkaz.

```console
helm inspect mycontainerregistry/wordpress
```

Pokud není k dispozici žádné číslo verze, použije se *nejnovější* verze. Helm vrátí podrobné informace o grafu, jak je znázorněno na následujícím výstupu zkráceného příkladu:

```output
apiVersion: v1
appVersion: 5.3.2
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
[...]
```

Můžete také zobrazit informace pro graf pomocí příkazu Azure CLI [az az helm show.][az-acr-helm-show] Ve výchozím nastavení je vrácena *nejnovější* verze grafu. Můžete připojit `--version` k seznamu konkrétní verzi grafu, například *8.1.0*:

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Instalace grafu Helm z úložiště

Graf Helm ve vašem úložišti se nainstaluje zadáním názvu úložiště a názvu grafu. K instalaci grafu Wordpress použijte klienta Helm:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Pokud předáte do úložiště grafu Správce registru registru Azure a později se vrátíte v nové relaci příkazového příkazu, váš místní klient Helmu potřebuje aktualizovaný ověřovací token. Chcete-li získat nový ověřovací token, použijte příkaz [add add helmu az acr.][az-acr-helm-repo-add]

Během procesu instalace jsou dokončeny následující kroky:

- Klient Helm prohledává index místního úložiště.
- Odpovídající graf se stáhne z úložiště registru kontejnerů Azure.
- Graf se nasazuje pomocí tilleru v clusteru Kubernetes.

Při instalaci postupujte podle pokynů ve výstupu příkazu a podívejte se na adresy URL a přihlašovací údaje worpressu. Příkaz můžete spustit `kubectl get pods` také a zobrazit prostředky Kubernetes nasazené prostřednictvím grafu Helm:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Odstranění grafu Helm z úložiště

Chcete-li odstranit graf z úložiště, použijte příkaz [az acr helm delete.][az-acr-helm-delete] Zadejte název grafu, například *wordpress*a verzi, kterou chcete odstranit, například *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Pokud chcete odstranit všechny verze pojmenovaného grafu, `--version` vynechejte parametr.

Graf bude nadále vrácen `helm search`při spuštění . Opět klient Helm automaticky neaktualizuje seznam dostupných grafů v úložišti. Chcete-li aktualizovat index repo klienta Helm, použijte příkaz [add-po repo helmu az acr][az-acr-helm-repo-add] znovu:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření a nasazování kormidelních grafů naleznete v [tématu Vývoj grafů helmu][develop-helm-charts].
* Další informace o instalaci aplikací pomocí helmu ve [službě Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md).
* Grafy souřízení lze použít jako součást procesu sestavení kontejneru. Další informace najdete [v tématu Použití úloh registru kontejnerů Azure][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-v2]: https://v2.helm.sh/docs/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[semver2]: https://semver.org/
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
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
