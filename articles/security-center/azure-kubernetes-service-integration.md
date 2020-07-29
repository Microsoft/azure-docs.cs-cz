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
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 94d1bccc9a7f45d24d8c5b92aecba54d9f7f630a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800169"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integrace služeb Azure Kubernetes Services s Security Center

Služba Azure Kubernetes Service (AKS) je spravovaná služba Microsoftu pro vývoj, nasazování a správu kontejnerových aplikací. 

Pokud pracujete na úrovni Standard Azure Security Center, můžete přidat sadu prostředků AKS (viz [ceny](security-center-pricing.md)) a získat tak hlubší přehled o vašich AKSch uzlech, cloudovém provozu a ovládacích prvcích zabezpečení.

Společně Security Center a AKS tvoří nejlepší nabídku zabezpečení Kubernetes pro Cloud Native.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Jaké jsou součásti Security Center ochrany Kubernetes?

Ochrana Security Center pro Kubernetes je poskytována kombinací dvou prvků:

- **Azure Security Center ochrany před hrozbami pro virtuální počítače** – pomocí stejného agenta Log Analytics, který Security Center používá na jiných virtuálních počítačích, Security Center může ukázat problémy zabezpečení, ke kterým dochází na AKS uzlech. Agent také monitoruje analýzu specifickou pro kontejner.

- **Azure Security Center nepovinný Kubernetes** balíček – Kubernetes sada přijímá protokoly a informace z podsystému Kubernetes prostřednictvím služby AKS. Tyto protokoly jsou už v Azure k dispozici prostřednictvím služby AKS. Když Security Center povolíte Kubernetes sadu prostředků, udělíte Security Center přístup k protokolům. Proto Security Center přináší výhody zabezpečení clusterům AKS pomocí dat, která jsou již shromážděna hlavním uzlem AKS. Některá z dat, která prohledává Azure Security Center z prostředí Kubernetes, můžou obsahovat citlivé informace.

    ![Přehled vysoké úrovně Azure Security Center a Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

## <a name="what-protections-are-provided"></a>Jaké ochrany jsou k dispozici?

Používání těchto dvou služeb nabízí:

* **Doporučení zabezpečení** – Security Center IDENTIFIKUJE prostředky AKS a kategorizují je: z clusterů na jednotlivé virtuální počítače. Pak můžete zobrazit doporučení zabezpečení na prostředek. Další informace najdete v doporučeních pro kontejnery v [referenčním seznamu doporučení](recommendations-reference.md#recs-containers). 

* **Posílení zabezpečení prostředí** – Security Center neustále monitoruje konfiguraci clusterů Kubernetes a konfigurací Docker. Pak vygeneruje doporučení zabezpečení, která odpovídají oborovým standardům.

* **Ochrana za běhu** – díky průběžné analýze následujících AKS zdrojů vás Security Center upozorní na hrozby a škodlivou aktivitu zjištěnou na úrovni clusteru host *a* AKS. [Přečtěte si další informace o ochraně před hrozbami pro kontejnery](threat-protection.md#azure-containers).


     

![Další podrobnosti Azure Security Center a Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)



## <a name="aks-with-security-center-faq"></a>AKS s nejčastějšími dotazy k Security Center

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Můžu I nadále získávat AKS ochranu bez agenta Log Analytics?

Jak je uvedeno výše, volitelná sada Kubernetes poskytuje ochranu na úrovni clusteru, Log Analytics agenta Azure Security Center úrovně Standard chrání vaše uzly. 

Pro nejúplnější možnou ochranu doporučujeme nasazení obou.

Pokud se rozhodnete neinstalovat agenta na hostitele, obdržíte pouze podmnožinu výhod ochrany před hrozbami a výstrahy zabezpečení. Pořád budete dostávat výstrahy týkající se analýzy sítě a komunikace se škodlivými servery.



## <a name="next-steps"></a>Další kroky

Další informace o funkcích zabezpečení kontejneru Security Center najdete v tématech:

* [Zabezpečení Azure Security Center a kontejneru](container-security.md)

* [Integrace se službou Azure Container Registry](azure-container-registry-integration.md)

* [Správa dat v Microsoftu](https://www.microsoft.com/trust-center/privacy/data-management) – popisuje zásady pro data služeb Microsoftu (včetně Azure, Intune a Microsoft 365), podrobnosti o správě dat od Microsoftu a zásady uchovávání dat, které mají vliv na vaše data.
