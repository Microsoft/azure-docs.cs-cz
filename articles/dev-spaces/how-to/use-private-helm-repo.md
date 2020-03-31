---
title: Jak používat privátní úložiště Helm v Azure Dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Použijte privátní úložiště Helm v Azure Dev Space.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery, Helm
manager: gwallace
ms.openlocfilehash: c8f0e463bc78d278d8162f8389664dbb46a83301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240473"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Použití soukromého úložiště Helm v Azure Dev Spaces

[Helm][helm] je správce balíčků pro Kubernetes. Helm používá formát [grafu][helm-chart] k balení závislostí. Grafy helmu jsou uloženy v úložišti, které může být veřejné nebo soukromé. Azure Dev Spaces načte grafy helmu pouze z veřejných úložišť při spuštění aplikace. V případech, kdy je úložiště Helm soukromé nebo azure dev spaces k němu nemají přístup, můžete přidat graf z tohoto úložiště přímo do vaší aplikace. Přidání grafu přímo umožňuje Azure Dev Spaces spustit vaši aplikaci bez nutnosti přístupu k privátní úložiště Helm.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Přidání soukromého úložiště Helm do místního počítače

Použijte [helm repo přidat][helm-repo-add] a helm [repo aktualizace][helm-repo-update] pro přístup k soukromému úložiště Helm z místního počítače.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Přidání grafu do aplikace

Přejděte do adresáře projektu `azds prep`a spusťte aplikaci .

```cmd
azds prep --enable-ingress
```

> [!TIP]
> Příkaz `prep` se pokusí vygenerovat [Dockerfile a Helm graf](../how-dev-spaces-works-prep.md#prepare-your-code) pro váš projekt. Azure Dev Spaces používá tyto soubory k sestavení a spuštění kódu, ale můžete upravit tyto soubory, pokud chcete změnit způsob sestavení a spuštění projektu.

Vytvořte soubor [requirements.yaml][helm-requirements] s grafem v adresáři grafu aplikace. Například pokud vaše aplikace s názvem *app1*, byste vytvořit *charts/app1/requirements.yaml*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Přejděte do adresáře grafu vaší aplikace a pomocí [aktualizace závislostí helmu][helm-dependency-update] aktualizujte závislosti helmu pro vaši aplikaci a stáhněte graf z privátního úložiště.

```cmd
helm dependency update
```

Ověřte, zda byl do adresáře grafu aplikace přidán podadresář *grafů* se souborem *tgz.* Například *grafy/app1/charts/mychart-0.1.0.tgz*.

Graf z vašeho soukromého úložiště Helm byl stažen a přidán do vašeho projektu. Odeberte soubor *requirements.yaml,* aby se dev Spaces nepokusili tuto závislost aktualizovat.

## <a name="run-your-application"></a>Spusťte aplikaci

Přejděte do kořenového adresáře `azds up` projektu a spusťte a ověřte, zda se aplikace úspěšně spustí v prostoru pro vývoj.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>Další kroky

Přečtěte si více o [Helm a jak to funguje][helm].

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
