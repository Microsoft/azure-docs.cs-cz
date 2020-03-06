---
title: Uložení Helm grafů
description: Naučte se ukládat Helm grafy pro aplikace Kubernetes pomocí úložišť v Azure Container Registry
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: 7969efe37558fffb26b983131c56ae11f3ef9368
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78398975"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Vložení a vyžádání Helm grafů do služby Azure Container Registry

Pokud chcete rychle spravovat a nasazovat aplikace pro Kubernetes, můžete použít [Správce balíčků Open Source Helm][helm]. Pomocí Helm jsou balíčky aplikací definované jako [grafy](https://helm.sh/docs/topics/charts/), které se shromažďují a ukládají do [úložiště grafu Helm](https://helm.sh/docs/topics/chart_repository/).

V tomto článku se dozvíte, jak hostovat grafy Helm v úložištích ve službě Azure Container Registry, a to pomocí instalace Helm 3 nebo Helm 2. V tomto příkladu uložíte stávající Helm graf z úložiště *veřejného Helm.* V mnoha scénářích byste sestavili a nahráli vlastní grafy pro aplikace, které vyvíjíte. Další informace o tom, jak vytvářet vlastní grafy Helm, najdete v [příručce pro vývojáře šablony grafu][develop-helm-charts].

> [!IMPORTANT]
> Podpora Helm grafů v Azure Container Registry je v současnosti ve verzi Preview. Verze Preview jsou k dispozici na základě podmínky, že souhlasíte s doplňkovými [podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="helm-3-or-helm-2"></a>Helm 3 nebo Helm 2?

K ukládání, správě a instalaci Helm grafů použijte klienta Helm a rozhraní příkazového řádku Helm. Hlavní verze klienta Helm zahrnují Helm 3 a Helm 2. Helm 3 podporuje nový formát grafu a už neinstaluje serverovou komponentu do pokladny na straně serveru. Podrobnosti o rozdílech verze najdete v tématu [Nejčastější dotazy k verzi](https://helm.sh/docs/faq/). Pokud jste již dříve nasadili grafy Helm 2, přečtěte si téma [migrace Helm v2 na V3](https://helm.sh/docs/topics/v2_v3_migration/).

Pro hostování Helm grafů v Azure Container Registry můžete použít buď Helm 3 nebo Helm 2, a pracovní postupy specifické pro jednotlivé verze:

* [Helm 3 klient](#use-the-helm-3-client) – pomocí `helm chart`ch příkazů můžete spravovat grafy v registru jako [artefakty OCI](container-registry-image-formats.md#oci-artifacts) .
* [Klient Helm 2](#use-the-helm-2-client) – pomocí příkazu [AZ ACR Helm][az-acr-helm] v rozhraní příkazového řádku Azure můžete přidat a spravovat registr kontejnerů jako úložiště grafů Helm.

### <a name="additional-information"></a>Další informace

* Pro správu grafů jako artefaktů OCI doporučujeme použít pracovní postup Helm 3 s nativními příkazy `helm chart`.
* Můžete použít starší příkazy [AZ ACR Helm][az-acr-helm] Azure CLI a pracovní postup s klientem a grafy Helm 3. Některé příkazy, jako je `az acr helm list`, nejsou ale kompatibilní s Helm 3 grafy.
* Od Helm 3 jsou podporovány příkazy [AZ ACR Helm][az-acr-helm] hlavně pro zajištění kompatibility s klientem Helm 2 a formátem grafu. Budoucí vývoj těchto příkazů není aktuálně plánován.

## <a name="use-the-helm-3-client"></a>Použití klienta Helm 3

### <a name="prerequisites"></a>Předpoklady

- **Registr kontejnerů Azure** ve vašem předplatném Azure. V případě potřeby vytvořte registr pomocí [Azure Portal](container-registry-get-started-portal.md) nebo [Azure CLI](container-registry-get-started-azure-cli.md).
- **Helm Client verze 3.0.0 nebo novější** – spusťte `helm version`, abyste našli aktuální verzi. Další informace o tom, jak nainstalovat a upgradovat Helm, najdete v tématu [instalace Helm][helm-install].
- **Cluster Kubernetes, do** kterého budete instalovat graf Helm. V případě potřeby vytvořte [cluster služby Azure Kubernetes][aks-quickstart]. 
- **Azure CLI verze 2.0.71 nebo novější** – spusťte `az --version` k nalezení verze. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

### <a name="high-level-workflow"></a>Pracovní postup na vysoké úrovni

S **Helm 3** jste:

* Může vytvořit jedno nebo víc úložišť Helm ve službě Azure Container Registry.
* Uložte Helm 3 grafy do registru jako [artefakty OCI](container-registry-image-formats.md#oci-artifacts). V současné době se podpora Helm 3 pro OCI považuje za *experimentální*.
* Použití `helm chart`ch příkazů přímo z rozhraní příkazového řádku Helm k vložení, vyžádání a správě Helm grafů v registru
* Pomocí rozhraní příkazového řádku Azure, který pak automaticky aktualizuje klienta Helm s identifikátorem URI a přihlašovacími údaji registru, proveďte ověření u svého registru. Tyto informace registru nemusíte zadávat ručně, takže přihlašovací údaje nejsou k dispozici v historii příkazů.
* Pomocí `helm install` můžete nainstalovat grafy do clusteru Kubernetes z místní mezipaměti úložiště.

Příklady najdete v následujících oddílech.

### <a name="enable-oci-support"></a>Povolit podporu OCI

Nastavte následující proměnnou prostředí pro povolení podpory rozhraní OCI v klientovi Helm 3. V současné době je tato podpora experimentální. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="pull-an-existing-helm-package"></a>Stažení existujícího balíčku Helm

Pokud jste ještě nepřidali úložiště grafu `stable` Helm, spusťte příkaz `helm repo add`:

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com
```

Vyžádat balíček grafu z úložiště `stable` místně. Například vytvořte místní adresář, například *~/ACR-Helm*, a pak stáhněte stávající balíček grafu *stabilní/WordPress* . (Tento příklad a další příkazy v tomto článku jsou naformátované pro prostředí bash.)

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm pull stable/wordpress --untar
```

Příkaz `helm pull stable/wordpress` nezadal konkrétní verzi, proto byla *Poslední* verze v podadresáři `wordpress` načtena a dekomprimována.

### <a name="save-chart-to-local-registry-cache"></a>Uložit graf do místní mezipaměti registru

Změňte adresář na podadresář `wordpress`, který obsahuje soubory grafu Helm. Potom spusťte `helm chart save` a uložte kopii grafu místně a také vytvořte alias s plně kvalifikovaným názvem registru a cílovým úložištěm a označením. 

V následujícím příkladu je název registru *mycontainerregistry*, cílové úložiště je *WordPress*a cílová značka grafu je *nejnovější*, ale nahrazující hodnoty pro vaše prostředí:

```console
cd wordpress
helm chart save . wordpress:latest
helm chart save . mycontainerregistry.azurecr.io/helm/wordpress:latest
```

Spusťte `helm chart list` a potvrďte tak, že jste uložili grafy do místní mezipaměti registru. Výstup se podobá tomuto:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
wordpress:latest                                         wordpress       8.1.0   5899db0 29.1 KiB        1 day 
mycontainerregistry.azurecr.io/helm/wordpress:latest     wordpress       8.1.0   5899db0 29.1 KiB        1 day 
```

### <a name="push-chart-to-azure-container-registry"></a>Vložení grafu do Azure Container Registry

Spuštěním příkazu `helm chart push` v rozhraní příkazového řádku Helm 3 nahrajte graf Helm do úložiště ve službě Azure Container Registry. Pokud neexistuje, úložiště se vytvoří.

Nejprve použijte příkaz Azure CLI [AZ ACR Login][az-acr-login] k ověření v registru:

```azurecli
az acr login --name mycontainerregistry
```

Nahrajte graf do plně kvalifikovaného cílového úložiště:

```console
helm chart push mycontainerregistry.azurecr.io/helm/wordpress:latest
```

Po úspěšném vložení se výstup podobá:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/wordpress]
ref:     mycontainerregistry.azurecr.io/helm/wordpress:latest
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    29.1 KiB
name:    wordpress
version: 8.1.0
```

### <a name="list-charts-in-the-repository"></a>Seznam grafů v úložišti

Stejně jako u imagí uložených v Azure Container Registry můžete pomocí příkazu [AZ ACR úložiště][az-acr-repository] zobrazit úložiště hostující vaše grafy a značky a manifesty grafů. 

Například spuštěním [AZ ACR úložiště show][az-acr-repository-show] zobrazíte vlastnosti úložiště, které jste vytvořili v předchozím kroku:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/wordpress
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
  "createdTime": "2020-01-29T16:54:30.1514833Z",
  "imageName": "helm/wordpress",
  "lastUpdateTime": "2020-01-29T16:54:30.4992247Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Spuštěním příkazu [AZ ACR úložiště show-Manifests][az-acr-repository-show-manifests] zobrazíte podrobnosti o grafu, který je uložený v úložišti. Příklad:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/wordpress --detail
```

Výstup, zkrácený v tomto příkladu, zobrazuje `configMediaType` `application/vnd.cncf.helm.config.v1+json`:

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-01-29T16:54:30.2382436Z",
    "digest": "sha256:xxxxxxxx51bc0807bfa97cb647e493ac381b96c1f18749b7388c24bbxxxxxxxxx",
    "imageSize": 29995,
    "lastUpdateTime": "2020-01-29T16:54:30.3492436Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "latest"
    ]
  }
]
```

### <a name="pull-chart-to-local-cache"></a>Načíst graf do místní mezipaměti

Pro instalaci grafu Helm do Kubernetes musí být graf v místní mezipaměti. V tomto příkladu nejprve spusťte `helm chart remove` pro odebrání existujícího místního grafu s názvem `mycontainerregistry.azurecr.io/helm/wordpress:latest`:

```console
helm chart remove mycontainerregistry.azurecr.io/helm/wordpress:latest
```

Spusťte `helm chart pull` ke stažení grafu ze služby Azure Container Registry do místní mezipaměti:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/wordpress:latest
```

### <a name="export-helm-chart"></a>Exportovat graf Helm

Pokud chcete s grafem pracovat dál, exportujte ho do místního adresáře pomocí `helm chart export`. Exportujte například graf, který jste vyžádali do adresáře `install`:

```console
helm chart export mycontainerregistry.azurecr.io/helm/wordpress:latest --destination ./install
```

Chcete-li zobrazit informace pro exportovaný graf v úložišti, spusťte příkaz `helm inspect chart` v adresáři, do kterého jste graf exportovali.

```console
cd install
helm inspect chart wordpress
```

Pokud není k dispozici žádné číslo verze, použije se *nejnovější* verze. Helm vrátí podrobné informace o grafu, jak je znázorněno v následujícím zhuštěném výstupu:

```output
apiVersion: v1
appVersion: 5.3.2
dependencies:
- condition: mariadb.enabled
  name: mariadb
  repository: https://kubernetes-charts.storage.googleapis.com/
  tags:
  - wordpress-database
  version: 7.x.x
description: Web publishing platform for building blogs and websites.
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
```

### <a name="install-helm-chart"></a>Nainstalovat graf Helm

Spusťte `helm install` pro instalaci grafu Helm, který jste si vyžádali do místní mezipaměti a exportovali. Zadejte název verze nebo předejte parametr `--generate-name`. Příklad:

```console
helm install wordpress --generate-name
```

Jak instalace pokračuje, postupujte podle pokynů ve výstupu příkazu a zobrazte si adresy URL a přihlašovací údaje WorPress. Můžete také spustit příkaz `kubectl get pods` pro zobrazení prostředků Kubernetes nasazených pomocí grafu Helm:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Odstranění grafu Helm z úložiště

Pokud chcete graf odstranit z úložiště, použijte příkaz [AZ ACR úložiště Delete][az-acr-repository-delete] . Spusťte následující příkaz a po zobrazení výzvy potvrďte operaci:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/wordpress:latest
```

## <a name="use-the-helm-2-client"></a>Použití klienta Helm 2

### <a name="prerequisites"></a>Předpoklady

- **Registr kontejnerů Azure** ve vašem předplatném Azure. V případě potřeby vytvořte registr pomocí [Azure Portal](container-registry-get-started-portal.md) nebo [Azure CLI](container-registry-get-started-azure-cli.md).
- **Helm Client verze 2.11.0 (ne verze RC) nebo novější** – spusťte `helm version`, abyste našli aktuální verzi. Také budete potřebovat server Helm (do pokladny) inicializovaný v rámci clusteru Kubernetes. V případě potřeby vytvořte [cluster služby Azure Kubernetes][aks-quickstart]. Další informace o tom, jak nainstalovat a upgradovat Helm, najdete v tématu [instalace Helm][helm-install-v2].
- **Azure CLI verze 2.0.46 nebo novější** – spusťte `az --version` k nalezení verze. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

### <a name="high-level-workflow"></a>Pracovní postup na vysoké úrovni

S **Helm 2** jste:

* Nakonfigurujte službu Azure Container Registry jako *jedno* úložiště Helm grafů. Azure Container Registry spravuje definici indexu při přidávání a odebírání grafů do úložiště.
* Pomocí příkazů [AZ ACR Helm][az-acr-helm] v Azure CLI přidejte svůj registr kontejneru Azure jako úložiště grafů Helm a nahrajte a spravujte grafy. Tyto příkazy rozhraní příkazového řádku Azure zabalí Helm 2 klientské příkazy.
* Přidání úložiště grafu do služby Azure Container Registry do místního indexu úložiště Helm a podpora vyhledávání grafů
* Proveďte ověření pomocí Azure Container Registry prostřednictvím rozhraní příkazového řádku Azure, které pak automaticky aktualizuje klienta Helm s identifikátorem URI a přihlašovacími údaji registru. Tyto informace registru nemusíte zadávat ručně, takže přihlašovací údaje nejsou k dispozici v historii příkazů.
* Pomocí `helm install` můžete nainstalovat grafy do clusteru Kubernetes z místní mezipaměti úložiště.

Příklady najdete v následujících oddílech.

### <a name="add-repository-to-helm-client"></a>Přidat úložiště do klienta Helm

Pomocí příkazu [AZ ACR Helm úložiště][az-acr-helm-repo-add] Add přidejte do svého klienta Helm své Azure Container Registry úložiště grafu Helm. Tento příkaz načte ověřovací token pro službu Azure Container Registry, kterou používá klient Helm. Ověřovací token je platný 3 hodiny. Podobně jako u `docker login`můžete tento příkaz Spustit v budoucích relacích rozhraní příkazového řádku a ověřit klienta Helm pomocí úložiště Azure Container Registry Helm grafu:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-chart-to-the-repository"></a>Přidání grafu do úložiště

Nejprve vytvořte místní adresář na adrese *~/ACR-Helm*a pak stáhněte stávající graf *stabilní/WordPress* :

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Zadejte `ls` pro výpis staženého grafu a poznamenejte si verzi WordPress obsaženou v názvu souboru. Příkaz `helm fetch stable/wordpress` nezadal konkrétní verzi, takže se načetla *nejnovější* verze. V následujícím příkladu výstupu je graf WordPress verze *8.1.0*:

```output
wordpress-8.1.0.tgz
```

Nahrajte graf do úložiště grafu Helm v Azure Container Registry pomocí příkazu [AZ ACR Helm push][az-acr-helm-push] v rozhraní příkazového řádku Azure CLI. Zadejte název vašeho grafu Helm, který jste stáhli v předchozím kroku, například *WordPress-8.1.0. tgz*:

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Po chvíli Azure CLI hlásí, že je váš graf uložený, jak je znázorněno v následujícím příkladu výstupu:

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Seznam grafů v úložišti

Chcete-li použít graf nahraný v předchozím kroku, je nutné aktualizovat index místního úložiště Helm. Úložiště můžete znovu indexovat v klientovi Helm nebo pomocí Azure CLI aktualizovat index úložiště. Pokaždé, když přidáte graf do úložiště, tento krok je potřeba provést:

```azurecli
az acr helm repo add --name mycontainerregistry
```

Pomocí grafu uloženého v úložišti a aktualizovaného indexu, který je k dispozici místně, můžete k hledání nebo instalaci použít regulární příkazy klienta Helm. Pokud chcete zobrazit všechny grafy v úložišti, použijte příkaz `helm search`, který poskytuje vlastní název Azure Container Registry:

```console
helm search mycontainerregistry
```

V seznamu je uveden graf WordPress nabízený v předchozím kroku, jak je znázorněno v následujícím příkladu výstupu:

```output
NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  8.1.0           5.3.2       Web publishing platform for building blogs and websites.
```

Grafy můžete také zobrazit pomocí Azure CLI pomocí příkaz [AZ ACR Helm list][az-acr-helm-list]:

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Zobrazit informace o Helm grafu

Chcete-li zobrazit informace o konkrétním grafu v úložišti, můžete použít příkaz `helm inspect`.

```console
helm inspect mycontainerregistry/wordpress
```

Pokud není k dispozici žádné číslo verze, použije se *nejnovější* verze. Helm vrátí podrobné informace o grafu, jak je znázorněno v následujícím zhuštěném příkladu výstupu:

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

Můžete také zobrazit informace o grafu pomocí příkazu Azure CLI [AZ ACR Helm show][az-acr-helm-show] . Ve výchozím nastavení se vrátí *nejnovější* verze grafu. Můžete připojit `--version` k vypsání konkrétní verze grafu, jako je například *8.1.0*:

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Instalace grafu Helm z úložiště

Graf Helm v úložišti se nainstaluje zadáním názvu úložiště a názvu grafu. K instalaci grafu WordPress použijte klienta Helm:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Pokud přejdete do úložiště grafu Azure Container Registry Helm a později se vrátíte do nové relace CLI, váš místní klient Helm potřebuje aktualizovaný ověřovací token. Pokud chcete získat nový ověřovací token, použijte příkaz [AZ ACR Helm úložiště Add][az-acr-helm-repo-add] .

Během procesu instalace se dokončí tyto kroky:

- Klient Helm vyhledá v místním indexu úložiště.
- Odpovídající graf se stáhne z Azure Container Registryho úložiště.
- Graf se nasadí pomocí do pokladny v clusteru Kubernetes.

Jak instalace pokračuje, postupujte podle pokynů ve výstupu příkazu a zobrazte si adresy URL a přihlašovací údaje WorPress. Můžete také spustit příkaz `kubectl get pods` pro zobrazení prostředků Kubernetes nasazených pomocí grafu Helm:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Odstranění grafu Helm z úložiště

Pokud chcete graf odstranit z úložiště, použijte příkaz [AZ ACR Helm Delete][az-acr-helm-delete] . Zadejte název grafu, například *WordPress*, a verzi, kterou chcete odstranit, například *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Pokud chcete odstranit všechny verze pojmenovaného grafu, ponechte parametr `--version`.

Po spuštění `helm search`bude graf nadále vrácen. Klient Helm nebude automaticky aktualizovat seznam dostupných grafů v úložišti. K aktualizaci indexu úložiště klienta Helm použijte příkaz [AZ ACR Helm úložiště Add][az-acr-helm-repo-add] :

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Další kroky

Tento článek použil existující graf Helm z veřejného úložiště s veřejnou *stabilituí* . Další informace o tom, jak vytvořit a nasadit grafy Helm, najdete v tématu [vývoj Helm grafů][develop-helm-charts].

Grafy Helm lze použít jako součást procesu sestavení kontejneru. Další informace najdete v tématu [použití Azure Container Registry úloh][acr-tasks].

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
