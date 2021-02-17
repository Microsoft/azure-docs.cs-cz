---
title: Přehled povolených Kubernetesů Azure ARC
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Tento článek poskytuje přehled služby Azure ARC s povoleným Kubernetes.
keywords: Kubernetes, oblouk, Azure, kontejnery
ms.custom: references_regions
ms.openlocfilehash: 54b439d73ed5dfb0709d33dea1f588b8bdf4489f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560268"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Co je Kubernetes s podporou služby Azure Arc (Preview)?

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
* AKS na Azure Stack HCI
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

## <a name="next-steps"></a>Další kroky

* [Připojení clusteru](./connect-cluster.md)
