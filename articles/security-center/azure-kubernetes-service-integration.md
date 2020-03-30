---
title: Azure Security Center a služba Azure Kubernetes
description: Informace o integraci Azure Security Center se službami Azure Kubernetes Services
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d1cd4691586b27282d221a19c5fb7a1af034ed6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125160"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integrace služeb Azure Kubernetes s Centrem zabezpečení

Azure Kubernetes Service (AKS) je spravovaná služba Microsoftu pro vývoj, nasazování a správu kontejnerizovaných aplikací. 

Pomocí AKS společně se standardní úrovní Azure Security Center (viz [ceny)](security-center-pricing.md)získáte hlubší přehled o uzlech AKS, cloudovém provozu a ovládacích prvcích zabezpečení.

Security Center přináší výhody zabezpečení clusterům AKS pomocí dat, která již shromáždil hlavní uzel AKS. 

![Azure Security Center a Azure Kubernetes Service (AKS) přehled na vysoké úrovni](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Tyto dva nástroje společně tvoří nejlepší cloudově nativní nabídku zabezpečení Kubernetes. 

## <a name="benefits-of-integration"></a>Výhody integrace

Použití obou služeb společně poskytuje:

* **Doporučení zabezpečení** – Security Center identifikuje vaše prostředky AKS a kategorizuje je: od clusterů k jednotlivým virtuálním počítačům. Poté můžete zobrazit doporučení zabezpečení pro jednotlivé zdroje. Další informace naleznete v doporučení kontejnerů v [referenčníseznam doporučení](recommendations-reference.md#recs-computeapp). 

* **Posílení prostředí** – Security Center neustále monitoruje konfiguraci clusterů Kubernetes a konfigurací Dockeru. Poté generuje doporučení zabezpečení, která odrážejí oborové standardy.

* **Ochrana za běhu** – Prostřednictvím průběžné analýzy následujících zdrojů AKS vás Centrum zabezpečení upozorní na hrozby a škodlivou aktivitu zjištěnou na úrovni hostitele *a* clusteru AKS:
    * Nezpracovaná události zabezpečení, například síťová data a vytváření procesů
    * Protokol auditu Kubernetes

    Další informace najdete v tématu [ochrana před hrozbami pro kontejnery Azure.](threat-protection.md#azure-containers)

    Seznam možných výstrah naleznete v těchto částech v referenční tabulce výstrah: [Výstrahy na úrovni clusteru AKS](alerts-reference.md#alerts-akscluster) a [výstrahy na úrovni hostitele kontejneru](alerts-reference.md#alerts-containerhost).  

![Azure Security Center a Azure Kubernetes Service (AKS) podrobněji](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Některá data naskenovaná službou Azure Security Center z vašeho prostředí Kubernetes mohou obsahovat citlivé informace.


## <a name="next-steps"></a>Další kroky

Další informace o funkcích zabezpečení kontejnerů centra Security Center najdete v tématu:

* [Azure Security Center a zabezpečení kontejnerů](container-security.md)

* [Integrace se službou Azure Container Registry](azure-container-registry-integration.md)

* [Správa dat v Microsoftu](https://www.microsoft.com/trust-center/privacy/data-management) – popisuje zásady dat služeb Microsoftu (včetně Azure, Intune a Office 365), podrobnosti o správě dat microsoftu a zásady uchovávání informací, které ovlivňují vaše data.