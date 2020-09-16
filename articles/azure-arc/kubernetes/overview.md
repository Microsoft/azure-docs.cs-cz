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
ms.openlocfilehash: fb8a7b7c2e1e5b3de7d1ccdb4054e44825231458
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604797"
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


## <a name="next-steps"></a>Další kroky

* [Připojení clusteru](./connect-cluster.md)
