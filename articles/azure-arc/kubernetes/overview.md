---
title: Přehled Kubernetes s podporou ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Tento článek poskytuje přehled Kubernetes s podporou ARC Azure.
keywords: Kubernetes, oblouk, Azure, kontejnery
ms.custom: references_regions
ms.openlocfilehash: 4f07c0e5e01648984514701cd4838f85478d86af
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050051"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Co je Kubernetes ve verzi Preview s podporou ARC Azure?

Clustery Kubernetes můžete připojit a nakonfigurovat uvnitř nebo vně Azure pomocí Azure Kubernetes ve verzi Preview s podporou ARC. Když je cluster Kubernetes připojený ke službě Azure ARC, zobrazí se v Azure Portal. Bude mít ID Azure Resource Manager a spravovanou identitu. Clustery jsou připojené ke standardním předplatným Azure, jsou umístěné ve skupině prostředků a můžou přijímat značky stejně jako všechny ostatní prostředky Azure. 

Aby mohl správce clusteru připojit ke službě Azure cluster Kubernetes, musí nasazovat agenty. Tito agenti se spouštějí v oboru názvů Kubernetes s názvem `azure-arc` a jsou standardními nasazeními Kubernetes. Agenti jsou odpovědní za připojení k Azure, shromažďování protokolů a metrik Azure ARC a sledování žádostí o konfiguraci. 

Kubernetes s podporou ARC Azure podporuje standardní protokol SSL pro zabezpečení přenášených dat. Data jsou také uložená v klidovém stavu v Azure Cosmos DB databázi, aby se zajistila důvěrnost dat.
 
> [!NOTE]
> Kubernetes s povoleným obloukem Azure je ve verzi Preview. Nedoporučujeme ho pro produkční úlohy.

## <a name="supported-scenarios"></a>Podporované scénáře 

Kubernetes s podporou ARC Azure podporuje tyto scénáře: 

* Připojte Kubernetes běžící mimo Azure pro inventarizaci, seskupení a označování.

* Nasazení aplikací a použití konfigurace pomocí správy konfigurace založené na GitOps 

* Pomocí Azure Monitor pro kontejnery můžete zobrazit a monitorovat clustery. 

* Použijte zásady pomocí Azure Policy pro Kubernetes. 

## <a name="supported-regions"></a>Podporované oblasti 

Kubernetes s povoleným ARC Azure je aktuálně podporované v těchto oblastech: 

* East US 
* West Europe 

## <a name="next-steps"></a>Další kroky

* [Připojení clusteru](./connect-cluster.md)
