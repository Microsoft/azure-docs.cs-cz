---
title: Jak používat privátní úložiště Helm v Azure Dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Využijte soukromé úložiště Helm v prostoru pro vývoj v Azure.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery, Helm
manager: gwallace
ms.openlocfilehash: 7c5f28595df2e552fd48033b44e4e1f0ea4ec306
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91960333"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Použití privátního úložiště Helm v Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

[Helm][helm] je správce balíčků pro Kubernetes. Helm používá k zabalení závislosti formát [grafu][helm-chart] . Grafy Helm se ukládají v úložišti, které může být veřejné nebo soukromé. Azure Dev Spaces načte grafy Helm z veřejných úložišť při spuštění aplikace. V případech, kdy je úložiště Helm soukromé nebo Azure Dev Spaces k němu nemá přístup, můžete přidat graf z tohoto úložiště přímo do aplikace. Přidáním grafu můžete přímo Azure Dev Spaces spustit aplikaci, aniž byste museli přistupovat k privátnímu úložišti Helm.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Přidat úložiště privátního Helm do místního počítače

K získání přístupu k privátnímu úložišti Helm z místního počítače použijte [Helm úložiště přidat][helm-repo-add] a [Helm aktualizace úložiště][helm-repo-update] .

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Přidání grafu do aplikace

Přejděte do adresáře projektu a spusťte příkaz `azds prep` .

```cmd
azds prep --enable-ingress
```

> [!TIP]
> `prep`Příkaz se pokusí vygenerovat [graf souboru Dockerfile a Helm](../how-dev-spaces-works-prep.md#prepare-your-code) pro váš projekt. Azure Dev Spaces používá tyto soubory k sestavení a spuštění kódu, ale můžete upravit tyto soubory, pokud chcete změnit způsob sestavení a spuštění projektu.

Pomocí grafu v adresáři grafu vaší aplikace vytvořte soubor [. yaml požadavků][helm-requirements] . Například pokud má vaše aplikace název *app1*, měli byste vytvořit *grafy/app1/požadavky. yaml*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Přejděte do adresáře grafu vaší aplikace a pomocí [aktualizace závislostí Helm][helm-dependency-update] aktualizujte závislosti Helm pro vaši aplikaci a stáhněte graf z privátního úložiště.

```cmd
helm dependency update
```

Ověřte, že se do adresáře grafů vaší aplikace přidal podadresář *grafů* se souborem *tgz* . Například *grafy/app1/grafy/Mychart-0.1.0. tgz*.

Graf z privátního úložiště Helm se stáhl a přidal do projektu. Odeberte soubor *požadavky. yaml* , aby se vývojové prostory nepokusily aktualizovat tuto závislost.

## <a name="run-your-application"></a>Spusťte aplikaci

Přejděte do kořenového adresáře vašeho projektu a spusťte `azds up` aplikaci, abyste ověřili, jestli se vaše aplikace úspěšně spouštěla ve vývojovém prostoru.

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

Přečtěte si další informace o [Helm a o tom, jak funguje][helm].

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
