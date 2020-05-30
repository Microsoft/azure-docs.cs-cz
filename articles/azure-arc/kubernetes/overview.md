---
title: Přehled Kubernetes s povoleným ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: ''
keywords: Kubernetes, oblouk, Azure, kontejnery
ms.custom: references_regions
ms.openlocfilehash: badade7e89d0b03f330bfbe3578d02429220001b
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84194855"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Co je Kubernetes s podporou ARC Azure (Preview)

Clustery Kubernetes můžete připojit a nakonfigurovat uvnitř nebo vně Azure s povoleným Kubernetes ARC Azure (Preview). Když je cluster Kubernetes připojený ke službě Azure ARC, zobrazí se na webu Azure Portal, bude mít ID Azure Resource Manager a spravovanou identitu. Clustery jsou připojené ke standardním předplatným Azure, Live ve skupině prostředků a můžou přijímat značky stejně jako všechny ostatní prostředky Azure. 


Připojení clusteru Kubernetes k Azure vyžaduje, aby správce clusteru nasadil agenty. Tito agenti se spouštějí v oboru názvů Kubernetes s názvem `azure-arc` a jsou standardními nasazeními Kubernetes. Agenti jsou odpovědní za připojení k Azure, shromažďování protokolů a metrik Azure ARC a sledování žádostí o konfiguraci.  
 
 > [!NOTE]
> Kubernetes s povoleným obloukem Azure je ve verzi Preview a nedoporučuje se pro produkční úlohy. 


## <a name="supported-scenarios"></a>Podporované scénáře 

Kubernetes s podporou Azure ARC podporuje následující scénáře: 

* Propojení Kubernetes běžících mimo Azure pro inventarizaci, seskupení a označování 

* Nasazení aplikací a použití konfigurace pomocí správy konfigurace založené na GitOps 

* Použití Azure Monitor pro kontejnery k zobrazení a monitorování clusterů 

* Použití zásad pomocí Azure Policy pro Kubernetes 

 
## <a name="supported-regions"></a>Podporované oblasti 

Služba Azure ARC s povoleným Kubernetes je aktuálně podporována v následujících oblastech: 

* USA – východ 
* Západní Evropa 


## <a name="next-steps"></a>Další kroky

* [Připojení clusteru](./connect-cluster.md)
