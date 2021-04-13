---
title: Postup plánování a nasazení Kubernetes s povoleným ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 04/12/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Zaveďte velký počet clusterů do služby Azure ARC s povoleným Kubernetes pro správu konfigurace.
ms.openlocfilehash: 4b34cee08db508728f01d262ae4b1ee4ed4754e1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315394"
---
# <a name="plan-and-deploy-azure-arc-enabled-kubernetes"></a>Plánování a nasazení Kubernetes s povoleným ARC Azure

Nasazení služby IT infrastruktury nebo obchodní aplikace je výzvou k jakékoli společnosti. Aby nedošlo k nepřekvapenímm nebo neplánovaným nákladům, je potřeba, abyste ho důkladně naplánovali, abyste měli jistotu, že jste připravení. Takový plán by měl identifikovat návrh a kritéria nasazení, které je potřeba splnit k dokončení úkolů.

Aby nasazení mohlo pokračovat plynule, váš plán by měl jasně pochopit:

* Role a odpovědnosti.
* Inventář všech clusterů Kubernetes
* Splnění síťových požadavků.
* Sada dovedností a školení potřebné k povolení úspěšného nasazení a průběžné správy.
* Kritéria přijetí a způsob, jakým sledujete jeho úspěch.
* Nástroje nebo metody, které se mají použít k automatizaci nasazení.
* Zjištěná rizika a plány zmírňování zabraňují zpoždění a přerušení.
* Jak se vyhnout přerušení během nasazování.
* Jaká je cesta k eskalaci při výskytu významného problému?

Účelem tohoto článku je ujistit se, že jste připraveni na úspěšné nasazení Kubernetes s povoleným ARC Azure v rámci několika produkčních clusterů ve vašem prostředí.

## <a name="prerequisites"></a>Požadavky

* Existující cluster Kubernetes Pokud ho ještě nemáte, můžete cluster vytvořit pomocí jedné z těchto možností:
    - [Kubernetes v Docker (druh)](https://kind.sigs.k8s.io/)
    - Vytvoření clusteru Kubernetes pomocí Docker pro [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) nebo [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    - Samostatný cluster Kubernetes s využitím [rozhraní API clusteru](https://cluster-api.sigs.k8s.io/user/quick-start.html)

* Vaše počítače mají připojení z vaší místní sítě nebo jiného cloudového prostředí k prostředkům v Azure, a to buď přímo, nebo prostřednictvím proxy server. Další podrobnosti najdete v části [požadavky na síť](quickstart-connect-cluster.md#meet-network-requirements).

* `kubeconfig`Soubor ukazující na cluster, který chcete připojit ke službě Azure ARC.
* Oprávnění číst a zapsat pro uživatele nebo instanční objekt, který vytváří typ prostředku Kubernetes s povoleným ARC Azure `Microsoft.Kubernetes/connectedClusters` .

## <a name="pilot"></a>Pilotní nasazení

Před nasazením do všech produkčních clusterů začněte vyhodnocením tohoto procesu nasazení, abyste ho mohli ve svém prostředí široce přijmout. V případě pilotního projektu Identifikujte reprezentativní vzorkování clusterů, které nepostradatelné pro vaše společnosti mají schopnost podnikání. Budete chtít mít jistotu, že máte dostatek času na spuštění pilotního projektu a posoudit jeho dopad: doporučujeme přibližně 30 dní.

Vytvořte formální plán, který popisuje rozsah a podrobnosti pilotního projektu. Následující ukázkový plán vám může pomáhat začít.

* **Cíle** – popisuje obchodní a technické ovladače, které vedly k rozhodnutí, že pilotní nasazení je nezbytné.
* **Kritéria výběru** – určuje kritéria pro výběr, které aspekty řešení budou prokazatelně prostřednictvím pilotního projektu.
* **Rozsah** – pokrývá komponenty řešení, očekávaný plán, dobu trvání pilotního projektu a počet clusterů, které se mají cílit.
* **Kritéria a metriky úspěchu** – definujte kritéria úspěšnosti pilotního nasazení a konkrétní míry, které se používají k určení úrovně úspěchu.
* **Plán školení** – popisuje plán pro školení systémových techniků, správců atd., kteří jsou během pilotního nasazení noví v Azure a IT službách.
* **Plán přechodu** – popisuje strategii a kritéria, pomocí kterých je možné řídit přechod z pilotního projektu do produkčního prostředí.
* **Vrácení zpět** – popisuje postupy vrácení pilotního projektu do stavu před nasazením.
* **Rizika** – Vypíše všechna zjištěná rizika pro vedení pilotního nasazení a přidružená k produkčnímu nasazení.

## <a name="phase-1-build-a-foundation"></a>Fáze 1: sestavení základu

V této fázi mohou systémové technici nebo správci provádět základní aktivity, jako je vytváření skupin prostředků, značek, přiřazení rolí, aby bylo možné vytvářet a provozovat prostředky Kubernetes s povoleným použitím ARC Azure.

|Úkol |Podrobnosti |Doba trvání |
|-----|-------|---------|
| [Vytvoření skupiny prostředků](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Vyhrazená skupina prostředků pro zahrnutí jenom prostředků Kubernetes ARC Azure s povolenými prostředky a centralizaci správy a monitorování těchto prostředků. | Jedna hodina |
| Použijte [značky](../../azure-resource-manager/management/tag-resources.md) , které vám pomůžou organizovat počítače. | Vyhodnoťte a vytvořte [strategii označování](/azure/cloud-adoption-framework/decision-guides/resource-tagging/)zarovnaná na základě IT. To může přispět ke snížení složitosti správy prostředků Kubernetes s povoleným ARC Azure a zjednodušit rozhodování o správě. | Jeden den |
| Určení [konfigurací](tutorial-use-gitops-connected-cluster.md) pro GitOps | Identifikujte konfigurace aplikací nebo standardních hodnot, například `PodSecurityPolicy` , `NetworkPolicy` které chcete nasadit do svých clusterů. | Jeden den |
| Vývoj plánu zásad správného řízení [v Azure Policy](../../governance/policy/overview.md) | Určete, jak budete implementovat řízení clusterů Kubernetes s povoleným rozšířením Azure ARC v oboru předplatného nebo skupiny prostředků s Azure Policy. | Jeden den |
| Konfigurace [řízení přístupu na základě role](../../role-based-access-control/overview.md) (RBAC) | Vývoj plánu přístupu pro identifikaci, kdo má oprávnění ke čtení/zápisu a všem vašim clusterům | Jeden den |

## <a name="phase-2-deploy-azure-arc-enabled-kubernetes"></a>Fáze 2: nasazení Kubernetes s povoleným ARC Azure

V této fázi připojíme vaše clustery Kubernetes k Azure:

|Úkol |Podrobnosti |Doba trvání |
|-----|-------|---------|
| [Připojení prvního clusteru Kubernetes ke službě Azure ARC](quickstart-connect-cluster.md) | Jako součást připojení prvního clusteru ke službě Azure ARC nastavte prostředí pro připojování pomocí všech požadovaných nástrojů, jako je Azure CLI, Helm a rozšíření pro rozhraní příkazového `connectedk8s` řádku Azure. | 15 minut |
| [Vytvoření instančního objektu](create-onboarding-service-principal.md) | Vytvoření instančního objektu pro propojení Kubernetes clusterů neinteraktivně pomocí Azure CLI nebo PowerShellu. | Jedna hodina |


## <a name="phase-3-manage-and-operate"></a>Fáze 3: Správa a provoz

V této fázi nasadíme aplikace a konfigurace standardních hodnot do Kubernetes clusterů.

|Úkol |Podrobnosti |Doba trvání |
|-----|-------|---------|
|[Vytváření konfigurací](tutorial-use-gitops-connected-cluster.md) v clusterech | Vytvořte konfigurace pro nasazení vašich aplikací do prostředku Kubernetes s povoleným ARC Azure. | 15 minut |
|[Použití Azure Policy](use-azure-policy.md) pro vynucování konfigurací v měřítku | Vytvořte přiřazení zásad pro automatizaci nasazení standardních konfigurací napříč všemi clustery v rámci předplatného nebo oboru skupiny prostředků. | 15 minut |
| [Upgradovat agenty ARC Azure](agent-upgrade.md) | Pokud jste zakázali automatický upgrade agentů v clusterech, aktualizujte své agenty ručně na nejnovější verzi, abyste měli jistotu, že máte nejnovější opravy zabezpečení a chyb. | 15 minut |

## <a name="next-steps"></a>Další kroky

* Pomocí našeho rychlého startu můžete [připojit cluster Kubernetes ke službě Azure ARC](./quickstart-connect-cluster.md).
* [Vytvořte konfigurace](./tutorial-use-gitops-connected-cluster.md) v clusteru Kubernetes s povoleným ARC Azure.
* [Použijte Azure Policy pro aplikování konfigurace se škálováním](./use-azure-policy.md).