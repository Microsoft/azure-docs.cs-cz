---
title: ZASTARALÉ Monitorování clusteru Azure Kubernetes s využitím spoluškálování
description: Monitorování clusteru Kubernetes v Azure Container Service pomocí spoluškálování
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f1d0ca1ffc2e7a3d645ac5acbaafdf45f85550be
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271098"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>ZASTARALÉ Monitorování clusteru Azure Container Service Kubernetes s využitím spoluškálování

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

V tomto článku vám ukážeme, jak nasadit agenta [Koškály](https://web.archive.org/web/20180317071550/https://www.coscale.com/) pro monitorování všech uzlů a kontejnerů v clusteru Kubernetes v Azure Container Service. Pro tuto konfiguraci potřebujete účet se spoluškálou. 


## <a name="about-coscale"></a>O koškále 

Spoluškála je monitorovací platforma, která shromažďuje metriky a události ze všech kontejnerů v několika platformách Orchestration. Koškála nabízí kompletní monitorování pro Kubernetes prostředí. Poskytuje vizualizace a analýzy pro všechny vrstvy v zásobníku: operační systém, Kubernetes, Docker a aplikace běžící uvnitř vašich kontejnerů. Spoluškálování nabízí několik integrovaných řídicích panelů pro monitorování a má vestavěnou detekci anomálií, která umožňuje operátorům a vývojářům rychle najít problémy s infrastrukturou a aplikacemi.

![Uživatelské rozhraní pro spoluškálování](./media/container-service-kubernetes-coscale/coscale.png)

Jak je znázorněno v tomto článku, můžete nainstalovat agenty v clusteru Kubernetes ke spuštění spoluškálování jako řešení SaaS. Pokud chcete uchovávat data na pracovišti, je pro místní instalaci k dispozici i spoluškálování.


## <a name="prerequisites"></a>Požadavky

Nejdřív je potřeba [vytvořit účet Koškály](https://web.archive.org/web/20170507123133/https://www.coscale.com/free-trial).

Tento návod předpokládá, že jste [vytvořili cluster Kubernetes pomocí Azure Container Service](container-service-kubernetes-walkthrough.md).

Také předpokládá, že máte nainstalované `az` Azure CLI a `kubectl` Tools.

Můžete otestovat, jestli máte nainstalovaný nástroj `az`, a to spuštěním:

```azurecli
az --version
```

Pokud nemáte nainstalovaný nástroj `az`, [tady](/cli/azure/install-azure-cli)najdete pokyny.

Můžete otestovat, jestli máte nainstalovaný nástroj `kubectl`, a to spuštěním:

```bash
kubectl version
```

Pokud nemáte nainstalované `kubectl`, můžete spustit:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Instalace agenta spoluškálování s DaemonSet
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) používají Kubernetes ke spuštění jedné instance kontejneru na každém hostiteli v clusteru.
Jsou ideální pro spouštění agentů monitorování, jako je agent spoluškálování.

Až se přihlásíte ke spoluškálování, na [stránce agenta](https://app.coscale.com/) Nainstalujte agenty spoluškálování na svůj cluster pomocí DaemonSet. Uživatelské rozhraní koškály poskytuje kroky konfigurace s asistencí pro vytvoření agenta a zahájení monitorování kompletního clusteru Kubernetes.

![Konfigurace agenta pro spoluškálování](./media/container-service-kubernetes-coscale/installation.png)

Chcete-li spustit agenta v clusteru, spusťte zadaný příkaz:

![Spustit agenta spoluškálování](./media/container-service-kubernetes-coscale/agent_script.png)

A to je vše! Až budou agenti v provozu, měli byste během několika minut zobrazovat data v konzole. Navštivte [stránku agenta](https://app.coscale.com/) , kde se zobrazí souhrn vašeho clusteru, proveďte další konfigurační kroky a zobrazte řídicí panely, jako je například **Přehled clusteru Kubernetes**.

![Přehled clusteru Kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

Agent spoluškálování se automaticky nasadí na nové počítače v clusteru. Agent se aktualizuje automaticky, když se uvolní nová verze.


## <a name="next-steps"></a>Další kroky

Další informace o řešeních pro monitorování spoluškálování najdete v dokumentaci a [blogu](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog) o [spoluškálování](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/) . 

