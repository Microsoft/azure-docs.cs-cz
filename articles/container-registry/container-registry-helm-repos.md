---
title: Použití úložišť Helm v Azure Container Registry
description: Naučte se používat úložiště Helm s Azure Container Registry k ukládání grafů pro aplikace.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: 2135a3a5a8f14cf6c2e7fd2984d9b221e2445c1d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309517"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Použití Azure Container Registry jako úložiště Helm pro vaše aplikační grafy

Pokud chcete rychle spravovat a nasazovat aplikace pro Kubernetes, můžete použít [Správce balíčků Open Source Helm][helm]. V Helm se aplikace definují jako *grafy* , které jsou uložené v úložišti grafu Helm. Tyto grafy definují konfigurace a závislosti a můžou být ve verzi v životním cyklu aplikace. Azure Container Registry lze použít jako hostitele pro úložiště grafu Helm.

Díky Azure Container Registry máte soukromé, zabezpečené úložiště Helm grafů, které se může integrovat s kanály sestavení nebo jinými službami Azure. Úložiště Helm grafů v Azure Container Registry zahrnují funkce geografické replikace, aby se grafy zavřely do nasazení a zajistila redundance. Platíte jenom za úložiště, které používají grafy, a jsou dostupné ve všech Azure Container Registry cenové úrovni.

V tomto článku se dozvíte, jak používat úložiště grafu Helm uložené v Azure Container Registry.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="before-you-begin"></a>Před zahájením

K dokončení kroků v tomto článku musíte splnit následující předpoklady:

- **Azure Container Registry** – vytvořte registr kontejnerů ve vašem předplatném Azure. Použijte například [Azure Portal](container-registry-get-started-portal.md) nebo rozhraní příkazového [řádku Azure CLI](container-registry-get-started-azure-cli.md).
- **Helm Client verze 2.11.0 (ne verze RC) nebo novější** – spusťte `helm version` , abyste našli aktuální verzi. Také budete potřebovat server Helm (do pokladny) inicializovaný v rámci clusteru Kubernetes. V případě potřeby můžete [vytvořit cluster služby Azure Kubernetes][aks-quickstart]. Další informace o tom, jak nainstalovat a upgradovat Helm, najdete v tématu [instalace Helm][helm-install].
- Verze **Azure CLI 2.0.46 nebo novější** – spuštění `az --version` , aby se našela verze. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="add-a-repository-to-helm-client"></a>Přidání úložiště do klienta Helm

Úložiště Helm je server HTTP, který může ukládat grafy Helm. Azure Container Registry může toto úložiště poskytnout pro grafy Helm a spravovat definici indexu při přidávání a odebírání grafů do úložiště.

Pokud chcete přidat Azure Container Registry jako úložiště grafů Helm, použijte rozhraní příkazového řádku Azure. S tímto přístupem se váš klient Helm aktualizuje pomocí identifikátoru URI a přihlašovacích údajů pro úložiště, které Azure Container Registry. Tyto informace o úložišti nemusíte zadávat ručně, takže přihlašovací údaje nejsou k dispozici v historii příkazů, například.

V případě potřeby se přihlaste k rozhraní příkazového řádku Azure a postupujte podle následujících pokynů:

```azurecli
az login
```

Pomocí příkazu [AZ Configure][az-configure] nakonfigurujte výchozí nastavení Azure CLI s názvem vašeho Azure Container Registry. V následujícím příkladu nahraďte `<acrName>` názvem vašeho registru:

```azurecli
az configure --defaults acr=<acrName>
```

Nyní pomocí příkazu [AZ ACR Helm úložiště][az-acr-helm-repo-add] přidejte do svého klienta Helm své Azure Container Registry úložiště grafů Helm. Tento příkaz načte ověřovací token pro službu Azure Container Registry, kterou používá klient Helm. Ověřovací token je platný po dobu 1 hodiny. Podobně jako `docker login`u tohoto příkazu můžete spustit tento příkaz v budoucích relacích rozhraní příkazového řádku a ověřit klienta Helm pomocí úložiště grafu Azure Container Registry Helm:

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Přidání grafu do úložiště

V tomto článku získáme stávající Helm graf z úložiště Helm *stabilní* . *Stabilní* úložiště je veřejné úložiště, které zahrnuje běžné aplikační grafy. Údržba balíčků mohou odesílat grafy do stabilního úložiště stejným způsobem jako dokovací centrum, který poskytuje veřejný registr pro běžné image kontejnerů. Graf stažený *z veřejného úložiště* se pak může do privátního Azure Container Registryho úložiště vložit. Ve většině scénářů byste sestavili a nahráli vlastní grafy pro aplikace, které vyvíjíte. Další informace o tom, jak vytvářet vlastní grafy Helm, najdete v tématu [vývoj Helm grafů][develop-helm-charts].

Nejprve vytvořte adresář na adrese *~/ACR-Helm*a pak stáhněte stávající graf *stabilní/WordPress* :

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

Vypíše stažený graf a poznamenejte si verzi WordPress, která je součástí názvu souboru. Příkaz nezadal konkrétní verzi, takže se načetla nejnovější verze. `helm fetch stable/wordpress` Všechny grafy Helm obsahují číslo verze v názvu souboru, který následuje po standardu [SemVer 2][semver2] . V následujícím příkladu výstupu je graf WordPress verze *2.1.10*:

```
$ ls

wordpress-2.1.10.tgz
```

Nyní nahrajte graf do úložiště grafu Helm v Azure Container Registry pomocí příkazu Azure CLI [AZ ACR Helm push][az-acr-helm-push] . Zadejte název vašeho grafu Helm, který jste stáhli v předchozím kroku, například *WordPress-2.1.10. tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Po chvíli Azure CLI oznámí, že váš graf byl uložený, jak je znázorněno v následujícím příkladu výstupu:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>Seznam grafů v úložišti

Klient Helm udržuje místní kopii obsahu vzdálených úložišť v mezipaměti. Změny ve vzdáleném úložišti automaticky neaktualizují seznam dostupných grafů známých místně pomocí klienta Helm. Při hledání grafů v úložištích Helm používá místní index uložený v mezipaměti. Chcete-li použít graf nahraný v předchozím kroku, je nutné aktualizovat index místního úložiště Helm. Úložiště můžete znovu indexovat v klientovi Helm nebo pomocí Azure CLI aktualizovat index úložiště. Pokaždé, když přidáte graf do úložiště, tento krok je potřeba provést:

```azurecli
az acr helm repo add
```

Pomocí grafu uloženého v úložišti a aktualizovaného indexu, který je k dispozici místně, můžete k hledání nebo instalaci použít regulární příkazy klienta Helm. Chcete-li zobrazit všechny grafy v úložišti, použijte `helm search <acrName>`. Zadejte vlastní název Azure Container Registry:

```console
helm search <acrName>
```

V seznamu je uveden graf WordPress nabízený v předchozím kroku, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

Grafy můžete také zobrazit pomocí Azure CLI pomocí příkaz [AZ ACR Helm list][az-acr-helm-list]:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Zobrazit informace o Helm grafu

Chcete-li zobrazit informace o konkrétním grafu v úložišti, můžete znovu použít regulárního klienta Helm. Chcete-li zobrazit informace o grafus názvem WordPress `helm inspect`, použijte.

```console
helm inspect <acrName>/wordpress
```

Pokud není k dispozici žádné číslo verze, použije se *nejnovější* verze. Helm vrátí podrobné informace o grafu, jak je znázorněno v následujícím zhuštěném příkladu výstupu:

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
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

Můžete také zobrazit informace o grafu pomocí příkazu Azure CLI [AZ ACR Helm show][az-acr-helm-show] . Ve výchozím nastavení se vrátí *nejnovější* verze grafu. Můžete připojit `--version` k seznamu konkrétní verze grafu, jako je například *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Instalace grafu Helm z úložiště

Graf Helm v úložišti se nainstaluje zadáním názvu úložiště a názvu grafu. K instalaci grafu WordPress použijte klienta Helm:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Pokud přejdete do úložiště grafu Azure Container Registry Helm a později se vrátíte do nové relace CLI, váš místní klient Helm potřebuje aktualizovaný ověřovací token. Pokud chcete získat nový ověřovací token, použijte příkaz [AZ ACR Helm úložiště Add][az-acr-helm-repo-add] .

Během procesu instalace se dokončí tyto kroky:

- Klient Helm vyhledá v místním indexu úložiště.
- Odpovídající graf se stáhne z Azure Container Registryho úložiště.
- Graf se nasadí pomocí do pokladny v clusteru Kubernetes.

Následující zhuštěný příklad výstupu ukazuje prostředky Kubernetes nasazené prostřednictvím grafu Helm:

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

## <a name="delete-a-helm-chart-from-the-repository"></a>Odstranění grafu Helm z úložiště

Pokud chcete graf odstranit z úložiště, použijte příkaz [AZ ACR Helm Delete][az-acr-helm-delete] . Zadejte název grafu, například *WordPress*, a verzi, kterou chcete odstranit, například *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Pokud chcete odstranit všechny verze pojmenovaného grafu, ponechte `--version` parametr.

Graf bude nadále vrácen v `helm search <acrName>`. Klient Helm nebude automaticky aktualizovat seznam dostupných grafů v úložišti. K aktualizaci indexu úložiště klienta Helm použijte příkaz [AZ ACR Helm úložiště Add][az-acr-helm-repo-add] :

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>Další kroky

Tento článek použil existující graf Helm z veřejného úložiště s veřejnou stabilituí. Další informace o tom, jak vytvořit a nasadit grafy Helm, najdete v tématu [vývoj Helm grafů][develop-helm-charts].

Grafy Helm lze použít jako součást procesu sestavení kontejneru. Další informace najdete v tématu [použití Azure Container Registry úloh][acr-tasks].

Další informace o tom, jak používat a spravovat Azure Container Registry, najdete v [][acr-bestpractices]tématu věnovaném osvědčeným postupům.

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
