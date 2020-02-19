---
title: Azure Security Center a služba Azure Kubernetes
description: Další informace o integraci Azure Security Center s Azure Kubernetes Services
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
ms.openlocfilehash: 9d8c5f98cfd8b4b3831bcbd7e65285f93e6c323f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77441948"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Integrace služeb Azure Kubernetes Services s využitím Security Center (Preview)
Služba Azure Kubernetes Service (AKS) je spravovaná služba Microsoftu pro vývoj, nasazování a správu kontejnerových aplikací. 

Využijte AKS společně s úrovní Standard Azure Security Center (viz [ceny](security-center-pricing.md)) a získejte hlubší přehled o vašich AKSch uzlech, cloudovém provozu a kontrolách zabezpečení.

Security Center přináší výhody zabezpečení clusterům AKS pomocí dat, která již byla shromážděna hlavním uzlem AKS. 

![Přehled vysoké úrovně Azure Security Center a Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Tyto dva nástroje tvoří nejlepší nabídku zabezpečení Kubernetes v cloudu. 

## <a name="benefits-of-integration"></a>Výhody integrace

Používání těchto dvou služeb nabízí:

* **Doporučení zabezpečení** – Security Center IDENTIFIKUJE prostředky AKS a kategorizují je: z clusterů na jednotlivé virtuální počítače. Pak můžete zobrazit doporučení zabezpečení na prostředek. Další informace najdete v doporučeních pro kontejnery v [referenčním seznamu doporučení](recommendations-reference.md#recs-computeapp). 

    > [!NOTE]
    > Pokud název Security Center doporučení končí značkou "(Preview)", odkazuje na charakter verze Preview doporučení, nikoli na funkci.

* **Posílení zabezpečení prostředí** – Security Center neustále monitoruje konfiguraci clusterů Kubernetes a konfigurací Docker. Pak vygeneruje doporučení zabezpečení, která odpovídají oborovým standardům.

* **Ochrana za běhu** – díky průběžné analýze následujících AKS zdrojů vás Security Center vás upozorní na hrozby a škodlivou aktivitu zjištěnou na úrovni clusteru hostitel *a* AKS:
    * Nezpracované události zabezpečení, jako jsou například síťové údaje a vytváření procesů
    * Protokol auditu Kubernetes

    Další informace najdete v tématu [detekce hrozeb pro kontejnery Azure](security-center-alerts-compute.md#azure-containers-) .

    Seznam možných výstrah najdete v těchto částech v tabulce referenčních informací o výstrahách: [AKS výstrahy na úrovni clusteru](alerts-reference.md#alerts-akscluster) a [výstrahy na úrovni hostitele kontejneru](alerts-reference.md#alerts-containerhost).  

![Další podrobnosti Azure Security Center a Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Některá z dat, která prohledává Azure Security Center z prostředí Kubernetes, můžou obsahovat citlivé informace.


## <a name="next-steps"></a>Další kroky

Další informace o funkcích zabezpečení kontejneru Security Center najdete v tématech:

* [Zabezpečení Azure Security Center a kontejneru](container-security.md)

* [Integrace s Azure Container Registry](azure-container-registry-integration.md)

* [Správa dat v Microsoftu](https://www.microsoft.com/trust-center/privacy/data-management) – popisuje zásady pro data služeb Microsoftu (včetně Azure, Intune a Office 365), podrobnosti o správě dat od Microsoftu a zásady uchovávání dat, které mají vliv na vaše data.