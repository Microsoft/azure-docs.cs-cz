---
title: Přehled povolených Kubernetesů Azure ARC
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Tento článek poskytuje přehled služby Azure ARC s povoleným Kubernetes.
keywords: Kubernetes, oblouk, Azure, kontejnery
ms.custom: references_regions
ms.openlocfilehash: b3296aa1c1d5d463cc7f4cb932b44ad76632e19e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121588"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Co je Kubernetes s podporou Azure Arc?

S podporou Kubernetes ARC Azure můžete připojit a nakonfigurovat clustery Kubernetes v rámci nebo mimo Azure. Když připojíte cluster Kubernetes ke službě Azure ARC, bude:
* Zobrazí se v Azure Portal s ID Azure Resource Manager a spravovanou identitou. 
* Jsou umístěni do předplatného Azure a skupiny prostředků.
* Přijímají značky stejně jako všechny ostatní prostředky Azure. 

Aby mohl správce clusteru připojit ke službě Azure cluster Kubernetes, musí nasazovat agenty. Tito agenti:
* Spusťte v `azure-arc` oboru názvů Kubernetes jako standardní nasazení Kubernetes.
* Umožňuje pracovat s připojením k Azure.
* Shromážděte protokoly a metriky Azure ARC.
* Sledujte požadavky na konfiguraci. 

Kubernetes s povoleným ARC Azure podporuje standardní protokol SSL pro zabezpečení přenášených dat. Data jsou také uložená v klidovém stavu v Azure Cosmos DB databázi, aby se zajistila důvěrnost dat.

## <a name="supported-kubernetes-distributions"></a>Podporované distribuce Kubernetes

Kubernetes s podporou Azure ARC funguje se všemi certifikovanými clustery Kubernetes CNCF (Cloud Native Computing Foundation). Tým Azure ARC spolupracoval s [klíčovými partnery pro ověřování shody](./validation-program.md) svých Kubernetes distribucí s povoleným Kubernetes ARC Azure.

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
* USA – středozápad
* Středojižní USA
* Southeast Asia
* Spojené království – jih
* Západní USA 2
* Austrálie – východ
* USA – východ 2
* Severní Evropa

## <a name="next-steps"></a>Další kroky

Naučte se připojit cluster ke službě Azure ARC.
> [!div class="nextstepaction"]
> [Připojit cluster ke službě Azure ARC](./quickstart-connect-cluster.md)
