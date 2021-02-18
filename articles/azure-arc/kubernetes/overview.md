---
title: Přehled povolených Kubernetesů Azure ARC
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Tento článek poskytuje přehled služby Azure ARC s povoleným Kubernetes.
keywords: Kubernetes, oblouk, Azure, kontejnery
ms.custom: references_regions
ms.openlocfilehash: 3d96c8c8764db89501da6fb9c498f0a3d20461af
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652526"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Co je Kubernetes s podporou Azure Arc?

S podporou Kubernetes ARC Azure můžete připojit a nakonfigurovat clustery Kubernetes v rámci nebo mimo Azure. Když připojíte cluster Kubernetes ke službě Azure ARC, bude:
* Zobrazí se v Azure Portal s ID Azure Resource Manager a spravovanou identitou. 
* Připojí se ke standardním předplatným Azure.
* Být umístěn do skupiny prostředků.
* Přijímají značky stejně jako všechny ostatní prostředky Azure. 

Aby mohl správce clusteru připojit ke službě Azure cluster Kubernetes, musí nasazovat agenty. Tito agenti:
* Spusťte v `azure-arc` oboru názvů Kubernetes jako standardní nasazení Kubernetes.
* Umožňuje pracovat s připojením k Azure.
* Shromážděte protokoly a metriky Azure ARC.
* Sledujte požadavky na konfiguraci. 

Kubernetes s povoleným ARC Azure podporuje standardní protokol SSL pro zabezpečení přenášených dat. Tato data jsou uložena zašifrovaná a uložená v databázi Azure Cosmos DB, aby se zajistila důvěrnost dat.
 
## <a name="supported-kubernetes-distributions"></a>Podporované distribuce Kubernetes

Služba Azure ARC s povoleným Kubernetes funguje s jakýmkoli clusterem CNCF (Cloud Native Computing Foundation) s certifikací Kubernetes, jako je například:
* AKS Engine v Azure
* AKS – modul na rozbočovači Azure Stack
* GKE
* EKS
* VMware vSphere

Vyzkoušené funkce Kubernetes ve službě Azure ARC byly testovány týmem ARC v následujících distribucích:
* RedHat OpenShift 4,3
* Rancher RKE 1.0.8
* Kanonický Charmed Kubernetes 1,18
* Modul AKS
* AKS modul na rozbočovači Azure Stack
* AKS na Azure Stack HCI
* Zprostředkovatel rozhraní API pro clustery Azure

## <a name="supported-scenarios"></a>Podporované scénáře 

Kubernetes s podporou Azure ARC podporuje následující scénáře: 

* Připojte Kubernetes běžící mimo Azure pro inventarizaci, seskupení a označování.

* Nasaďte aplikace a použijte konfiguraci pomocí správy konfigurace založené na GitOps. 

* Zobrazení a monitorování clusterů pomocí Azure Monitor pro kontejnery. 

* Použijte zásady pomocí Azure Policy pro Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Podporované oblasti 

Kubernetes se zapnutým Azure ARC v tuto chvíli podporuje tyto oblasti: 

* East US 
* West Europe

## <a name="next-steps"></a>Další kroky

* [Připojení clusteru](./connect-cluster.md)
