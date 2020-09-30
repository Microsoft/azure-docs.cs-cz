---
title: Přehled povolených Kubernetesů Azure ARC
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Tento článek poskytuje přehled služby Azure ARC s povoleným Kubernetes.
keywords: Kubernetes, oblouk, Azure, kontejnery
ms.custom: references_regions
ms.openlocfilehash: 61317f7f5f2bf17c88fc019294574993c1854e59
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540638"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Co je Kubernetes ve verzi Preview s podporou Azure ARC?

Clustery Kubernetes můžete připojit a nakonfigurovat v rámci Azure nebo mimo něj pomocí Kubernetes ve verzi Preview s podporou Azure ARC. Když je cluster Kubernetes připojený ke službě Azure ARC, zobrazí se v Azure Portal. Bude mít ID Azure Resource Manager a spravovanou identitu. Clustery jsou připojené ke standardním předplatným Azure, jsou umístěné ve skupině prostředků a můžou přijímat značky stejně jako všechny ostatní prostředky Azure. 

Aby mohl správce clusteru připojit ke službě Azure cluster Kubernetes, musí nasazovat agenty. Tito agenti se spouštějí v oboru názvů Kubernetes s názvem `azure-arc` a jsou standardními nasazeními Kubernetes. Agenti jsou odpovědní za připojení k Azure, shromažďování protokolů a metrik Azure ARC a sledování žádostí o konfiguraci. 

Kubernetes s povoleným ARC Azure podporuje standardní protokol SSL pro zabezpečení přenášených dat. Data jsou také uložená v klidovém stavu v Azure Cosmos DB databázi, aby se zajistila důvěrnost dat.
 
> [!NOTE]
> Kubernetes s povoleným obloukem Azure je ve verzi Preview. Nedoporučujeme ho pro produkční úlohy.

## <a name="supported-kubernetes-distributions"></a>Podporované distribuce Kubernetes

Kubernetes s podporou Azure ARC funguje s jakýmkoli clusterem s certifikací CNCF (Cloud Native Computing Foundation), jako je AKS modul v Azure, AKS modul na Azure Stack hub, GKE, EKS a VMware vSphere clusteru.

Funkce Kubernetes s podporou ARC Azure byly testovány týmem ARC v následujících distribucích:
* RedHat OpenShift 4,3
* Rancher RKE 1.0.8
* Kanonický Charmed Kubernetes 1,18
* Modul AKS
* AKS modul na rozbočovači Azure Stack
* Zprostředkovatel rozhraní API pro clustery Azure

## <a name="supported-scenarios"></a>Podporované scénáře 

Kubernetes s podporou Azure ARC podporuje tyto scénáře: 

* Připojte Kubernetes běžící mimo Azure pro inventarizaci, seskupení a označování.

* Nasazení aplikací a použití konfigurace pomocí správy konfigurace založené na GitOps 

* Pomocí Azure Monitor pro kontejnery můžete zobrazit a monitorovat clustery. 

* Použijte zásady pomocí Azure Policy pro Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Podporované oblasti 

Kubernetes se zapnutým Azure ARC v tuto chvíli podporuje tyto oblasti: 

* East US 
* West Europe

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

* Jaký je rozdíl mezi Kubernetes a službou Azure Kubernetes (AKS) s podporou ARC?

    Azure Kubernetes Service (AKS) je spravovaná nabídka Kubernetes v Azure. AKS usnadňuje nasazení spravovaného clusteru Kubernetes v Azure. AKS zjednodušuje správu Kubernetes a snižuje provozní režii s tím spojenou díky přenášení většiny zodpovědnosti na Azure. Hlavní servery Kubernetes spravuje Azure. Pouze spravujete a udržujete uzly agentů.

    Kubernetes s povoleným ARC Azure umožňuje propojit clustery Kubernetes s Azure a rozšířit možnosti správy Azure, jako je Azure Monitor a Azure Policy. Údržba samotného clusteru Kubernetes se provádí sami.

* Potřebuji připojit clustery služby Azure Kubernetes spuštěné v Azure do Azure ARC?

    No. Všechny funkce služby Azure ARC s povoleným Kubernetes, jako je Azure Monitor, Azure Policy (gatekeeper), jsou nativně dostupné v AKS, které už mají v Azure reprezentaci prostředků. Konfigurace clusteru (GitOps) je také k dispozici nativně v AKS a aktuálně ve verzi Private Preview. K získání přístupu k této funkci použijte tento [registrační formulář](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5acO18Lmx5Bk_qao2CrOcFUQ0UyRllDR1BEV1BPNENYRERYN1pFWTQ4WC4u) .
    
* Mám svůj cluster AKS připojit k Azure Stack HCL do Azure ARC? Jaké clustery Kubernetes běží na rozbočovači Azure Stack nebo Azure Stackm stroji?

    Ano, připojení těchto clusterů ke službě Azure ARC má výhody. Poskytuje reprezentaci prostředků pro tyto clustery Kubernetes v Azure Resource Manager. Pomocí této reprezentace prostředků se dají rozšířit možnosti jako konfigurace clusteru, Azure Monitor Azure Policy (gatekeeper) na tyto clustery Kubernetes.

## <a name="next-steps"></a>Další kroky

* [Připojení clusteru](./connect-cluster.md)
