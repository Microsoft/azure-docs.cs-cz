---
title: Azure Security Center a služba Azure Kubernetes | Microsoft Docs
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
ms.openlocfilehash: 9bc01e0e703ea9f98d877be39011dcca7c7b284a
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521758"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Integrace služeb Azure Kubernetes Services s využitím Security Center (Preview)
Služba Azure Kubernetes Service (AKS) je spravovaná služba Microsoftu pro vývoj, nasazování a správu kontejnerových aplikací. 

Využijte AKS společně s úrovní Standard Azure Security Center (viz [ceny](security-center-pricing.md)) a získejte hlubší přehled o vašich AKSch uzlech, cloudovém provozu a kontrolách zabezpečení.

Security Center přináší výhody zabezpečení clusterům AKS pomocí dat, která již byla shromážděna hlavním uzlem AKS. 

![Přehled vysoké úrovně Azure Security Center a Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Tyto dva nástroje tvoří nejlepší nabídku zabezpečení Kubernetes v cloudu. 

## <a name="benefits-of-integration"></a>Výhody integrace

Používání těchto dvou služeb nabízí:

* **Doporučení zabezpečení** – Security Center IDENTIFIKUJE prostředky AKS a kategorizují je: z clusterů na jednotlivé virtuální počítače. Pak můžete zobrazit doporučení zabezpečení na prostředek. Další informace najdete v tématu [implementace doporučení zabezpečení](security-center-recommendations.md). 

    > [!NOTE]
    > Pokud název Security Center doporučení končí značkou "(Preview)", odkazuje se na povahu verze Preview doporučení; Nejedná se o funkci.

* **Posílení zabezpečení prostředí** – Security Center neustále monitoruje konfiguraci clusterů Kubernetes a generuje bezpečnostní doporučení, která odpovídají oborovým standardům.

* **Ochrana za běhu** – prostřednictvím průběžné analýzy následujících AKS zdrojů, Security Center vás upozorní na hrozby a škodlivou aktivitu zjištěnou na úrovni clusteru hostitel *a* AKS (Další informace najdete v tématu [Azure Container Service ](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-container-service-)):
    * Nezpracované události zabezpečení, jako jsou například síťové údaje a vytváření procesů
    * Protokol auditu Kubernetes

![Další podrobnosti Azure Security Center a Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Některá z dat, která prohledává Azure Security Center z prostředí Kubernetes, můžou obsahovat citlivé informace.

## <a name="next-steps"></a>Další kroky

Další informace o funkcích zabezpečení kontejneru Security Center najdete v tématech:

* [Zabezpečení Azure Security Center a kontejneru](container-security.md)

* [Integrace s Azure Container Registry](azure-container-registry-integration.md)

* [Ochrana virtuálního počítače](security-center-virtual-machine-protection.md) – popisuje doporučení Security Center.

* [Správa dat v Microsoftu](https://www.microsoft.com/trust-center/privacy/data-management) – popisuje zásady pro data služeb Microsoftu (včetně Azure, Intune a Office 365), podrobnosti o správě dat od Microsoftu a zásady uchovávání dat, které mají vliv na vaše data.