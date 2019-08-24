---
title: Jak používat privátní úložiště Helm v Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Využijte soukromé úložiště Helm v prostoru pro vývoj v Azure.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery, Helm
manager: gwallace
ms.openlocfilehash: d0f2cb739a502d614ac329eef1fc57b2fb15cb38
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014513"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Použití privátního úložiště Helm v Azure Dev Spaces

[Helm][helm] je správce balíčků pro Kuberentes. Helm používá k zabalení závislosti formát [grafu][helm-chart] . Grafy Helm se ukládají v úložišti, které může být veřejné nebo soukromé. Azure Dev Spaces načte grafy Helm z veřejných úložišť při spuštění aplikace. V případech, kdy je úložiště Helm soukromé nebo Azure Dev Spaces k němu nemá přístup, můžete přidat graf z tohoto úložiště přímo do aplikace. Přidáním grafu můžete přímo Azure Dev Spaces spustit aplikaci, aniž byste museli přistupovat k privátnímu úložišti Helm.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Přidat úložiště privátního Helm do místního počítače

K získání přístupu k privátnímu úložišti Helm z místního počítače použijte [Helm úložiště přidat][helm-repo-add] a [Helm aktualizace úložiště][helm-repo-update] .

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Přidání grafu do aplikace

Přejděte do adresáře projektu a spusťte příkaz `azds prep`.

```cmd
azds prep --public
```

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

## <a name="run-your-application"></a>Spustit aplikaci

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
[helm-chart]: https://helm.sh/docs/developing_charts/
[helm-dependency-update]: https://helm.sh/docs/helm/#helm-dependency-update
[helm-repo-add]: https://helm.sh/docs/helm/#helm-repo-add
[helm-repo-update]: https://helm.sh/docs/helm/#helm-repo-update
[helm-requirements]: https://helm.sh/docs/developing_charts/#chart-dependencies