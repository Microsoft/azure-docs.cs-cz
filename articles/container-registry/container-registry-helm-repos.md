---
title: Pomocí helmu úložiště ve službě Azure Container Registry
description: Další informace o použití úložiště helmu pomocí Azure Container Registry k uložení grafy pro vaše aplikace
services: container-registry
author: iainfoulds
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: 140d663524696f19844fea8906b181f468b7d157
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646998"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Použití Azure Container Registry jako úložiště helmu pro vaše aplikace grafy

Chcete-li rychle spravovat a nasazovat aplikace pro Kubernetes, můžete použít [Správce balíčků Helm open source][helm]. S nástrojem Helm, aplikace jsou definovány jako *grafy* , která jsou uložena v úložišti grafu helmu. Tyto grafy definovat konfigurace a závislostí a může být označené verzí v průběhu životního cyklu aplikací. Služba Azure Container Registry slouží jako hostitel pro úložiště grafu Helm.

S Azure Container Registry budete mít privátní, zabezpečené Helm grafu úložiště, která lze integrovat s kanály pro sestavování nebo jiných služeb Azure. Helm úložištích schémat ve službě Azure Container Registry jsou funkce geografické replikace udržovat si grafy blízko nasazení a za účelem zajištění redundance. Pouze platit za úložiště využitá službou grafy a jsou k dispozici ve všech vrstvách ceny Azure Container Registry.

V tomto článku se dozvíte, jak používat úložiště grafu helmu uložené ve službě Azure Container Registry.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku, musí být splněny následující požadavky:

- **Služba Azure Container Registry** – vytvoření registru kontejnerů ve vašem předplatném Azure. Například použít [webu Azure portal](container-registry-get-started-portal.md) nebo [rozhraní příkazového řádku Azure](container-registry-get-started-azure-cli.md).
- **Verze klienta Helm 2.11.0 (ne verzi RC) nebo novější** – spuštění `helm version` k vyhledání aktuální verze. Budete také potřebovat server helmu (Tiller) inicializovat v rámci clusteru Kubernetes. V případě potřeby můžete [vytvořit cluster Azure Kubernetes Service][aks-quickstart]. Další informace o tom, jak nainstalovat a upgradovat Helm, naleznete v tématu [instalace Helm][helm-install].
- **Azure CLI verze 2.0.46 nebo novější** – spuštění `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="add-a-repository-to-helm-client"></a>Přidat do úložiště helmu klienta

Úložiště helmu je server HTTP, která může ukládat grafy Helm. Služba Azure Container Registry můžete zadat toto úložiště pro grafy Helm a spravovat definice indexu přidávat a odebírat grafy do úložiště.

Přidáte služby Azure Container Registry jako úložiště grafu helmu pomocí Azure CLI. S tímto přístupem Helm klienta se aktualizuje s identifikátorem URI a přihlašovací údaje úložiště, které se opírá o Azure Container Registry. Není nutné ručně zadat informace o tomto úložišti, přihlašovací údaje nejsou zveřejněné v historii příkazů, třeba.

V případě potřeby připojte se k rozhraní příkazového řádku Azure a postupujte podle pokynů:

```azurecli
az login
```

Konfigurovat výchozí nastavení rozhraní příkazového řádku Azure s názvem pomocí Azure Container Registry [az konfigurace] [ az-configure] příkazu. V následujícím příkladu nahraďte `<acrName>` s názvem vašeho registru:

```azurecli
az configure --defaults acr=<acrName>
```

Nyní přidejte úložiště grafu helmu registru kontejneru Azure k vašemu klientovi Helm pomocí [az acr helm úložiště přidat] [ az-acr-helm-repo-add] příkazu. Tento příkaz načte ověření tokenu pro svůj registr kontejneru Azure, který se používá klientem nástroje Helm. Ověřovací token je platný pro 1 hodina. Podobně jako `docker login`, spustíte tento příkaz v budoucích relacích rozhraní příkazového řádku pro ověření klienta s úložištěm Azure Container Registry Helm grafu helmu:

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Přidání grafu do úložiště

Pro účely tohoto článku, pojďme si stávající grafu helmu z veřejné Helm *stabilní* úložiště. *Stabilní* úložiště je kurátorované, veřejné úložiště, který obsahuje běžné aplikace grafy. Balíček programu můžete odeslat k jejich grafy *stabilní* úložišti stejným způsobem, že poskytuje Docker Hubu veřejného registru pro běžné Image kontejneru. Graf stažené z veřejného *stabilní* úložiště následnému předání do privátního úložiště Azure Container Registry. Ve většině případů by sestavení a nahrát vlastní grafy pro aplikace, které vyvíjíte. Další informace o tom, jak vytvářet vlastní grafy Helm, naleznete v tématu [vývoj helmu][develop-helm-charts].

Nejprve vytvořte adresář v *~/acr-helm*, pak si stáhnout existující *stable/wordpress* grafu:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

Seznam stažených grafu a poznamenejte si verzi Wordpress zahrnuté v názvu souboru. `helm fetch stable/wordpress` Příkaz neurčili, nevložily konkrétní verzi, takže *nejnovější* verze se načetla. Všechny grafy Helm zahrnují číslo verze v názvu souboru, který následuje [SemVer 2] [ semver2] standard. Následující ukázkový výstup Wordpress schéma je verze *2.1.10*:

```
$ ls

wordpress-2.1.10.tgz
```

Graf nyní push do úložiště helmu grafu ve službě Azure Container Registry pomocí Azure CLI [az acr helm nabízených] [ az-acr-helm-push] příkazu. Zadejte název grafu helmu stáhli v předchozím kroku, například *wordpress 2.1.10.tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Po chvíli se rozhraní příkazového řádku Azure hlásí, že grafu byla uložena, jak je znázorněno v následujícím příkladu výstupu:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>Seznam grafy v úložišti

Klient Helm udržuje místní kopii v mezipaměti obsah ze vzdálených úložištích. Změny do vzdáleného úložiště nechcete aktualizovat automaticky seznamu k dispozici grafy známý místně klientem nástroje Helm. Při hledání grafy mezi úložišti Helm používá jeho místní mezipaměti index. Použít graf nahráli v předchozím kroku, je nutné aktualizovat místní úložiště index Helm. Můžete přeindexování úložišť v klientovi Helm nebo aktualizovat index úložiště pomocí Azure CLI. Pokaždé, když přidáte grafu do úložiště, tento krok je třeba provést:

```azurecli
az acr helm repo add
```

Pravidelné klienta příkazů Helm s grafem místně uložená v úložišti a k dispozici aktualizace indexu, můžete hledat ani instalovat. Pokud chcete zobrazit všechny grafy ve vašem úložišti, použijte `helm search <acrName>`. Zadejte název své vlastní službě Azure Container Registry:

```console
helm search <acrName>
```

Je uvedený graf Wordpress vloženo v předchozím kroku, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

Můžete také zařadit grafy pomocí rozhraní příkazového řádku Azure pomocí [az acr příkaz helm list][az-acr-helm-list]:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Zobrazit informace o Helm chart

Chcete-li zobrazit informace o konkrétní graf v úložišti, můžete znovu regulární Helm klienta. Pokud chcete zobrazit informace v grafu s názvem *wordpress*, použijte `helm inspect`.

```console
helm inspect <acrName>/wordpress
```

Pokud je k dispozici žádné číslo verze, *nejnovější* je použita verze. Příkaz Helm vrátí podrobné informace o schématu, jak je znázorněno v následujícím výstupu zhuštěnému příkladu:

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
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
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

Můžete také zobrazit informace o grafu pomocí Azure CLI [az acr helm zobrazit] [ az-acr-helm-show] příkazu. Znovu *nejnovější* verzi graf se vrátí ve výchozím nastavení. Můžete přidat `--version` do seznamu na konkrétní verzi nástroje grafu, jako například *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Nainstalovat Helm chart z úložiště

Grafu helmu ve vašem úložišti je nainstalován pomocí názvu úložiště a pak grafu název. Pomocí klienta Helm k instalaci Wordpressu grafu:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Pokud push do úložiště grafu helmu registru kontejneru Azure a později vrátit v nové relaci rozhraní příkazového řádku, musí váš místní klient Helm aktualizované ověřovací token. Chcete-li získat nové ověřovací token, použijte [az acr helm úložiště přidat] [ az-acr-helm-repo-add] příkazu.

Během procesu instalace jsou provedeny následující kroky:

- Příkaz Helm, vyhledává index místního úložiště.
- Odpovídající graf je stáhnout z úložiště Azure Container Registry.
- Grafu se nasadí pomocí Tiller v clusteru Kubernetes.

Následující výstup zhuštěnému příkladu ukazuje prostředky Kubernetesu nasazené prostřednictvím grafu helmu:

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>Odstranit z úložiště Helm chart

Chcete-li odstranit grafu z úložiště, použijte [az acr helm odstranit] [ az-acr-helm-delete] příkazu. Zadejte název grafu, například *wordpress*a verze, jako například odstranit *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Pokud chcete odstranit všechny verze s názvem grafu, vynechte `--version` parametru.

Pokračuje v grafu mají být vráceny v `helm search <acrName>`. Helm klienta znovu, nebude automaticky aktualizovat seznam k dispozici grafy v úložišti. Chcete-li aktualizovat index Helm klienta úložiště, použijte [az acr helm úložiště přidat] [ az-acr-helm-repo-add] znovu příkaz:

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>Další postup

Tento článek používá existující grafu helmu před veřejností *stabilní* úložiště. Další informace o tom, jak vytvořit a nasazení diagramů helmu najdete v tématu [vývoj helmu][develop-helm-charts].

Helmu může sloužit jako součást procesu sestavení kontejneru. Další informace najdete v tématu [pomocí Azure Container Registry úlohy][acr-tasks].

Další informace o tom, jak používat a spravovat Azure Container Registry, najdete v článku [osvědčené postupy][acr-bestpractices].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://docs.helm.sh/developing_charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
