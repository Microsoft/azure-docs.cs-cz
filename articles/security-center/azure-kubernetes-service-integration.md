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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: f1444ea31fe693f1c912a3ecb785bbb1e3aa6333
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977268"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integrace služeb Azure Kubernetes Services s Security Center

Služba Azure Kubernetes Service (AKS) je spravovaná služba Microsoftu pro vývoj, nasazování a správu kontejnerových aplikací. 

Umožněte Azure **Defenderu pro Kubernetes** získat hlubší přehled o vašich AKSch uzlech, cloudovém provozu a bezpečnostních ovládacích prvcích.

Společně Security Center a AKS tvoří nejlepší nabídku zabezpečení Kubernetes pro Cloud Native.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Jaké jsou součásti Security Center ochrany Kubernetes?

Ochrana Security Center pro Kubernetes je poskytována kombinací dvou prvků:

- **Azure Security Center ochrany před hrozbami pro virtuální počítače** – pomocí stejného agenta Log Analytics, který Security Center používá na jiných virtuálních počítačích, Security Center může ukázat problémy zabezpečení, ke kterým dochází na AKS uzlech. Agent také monitoruje analýzu specifickou pro kontejner.

- **Plán Kubernetes je nepovinný pro Azure Defender** – plán Kubernetes přijímá protokoly a informace z podsystému Kubernetes prostřednictvím služby AKS. Azure Security Center Tyto protokoly jsou už v Azure k dispozici prostřednictvím služby AKS. Když povolíte Azure Defender pro Kubernetes, udělíte jim Security Center přístup k protokolům. Proto Security Center přináší výhody zabezpečení clusterům AKS pomocí dat, která jsou již shromážděna hlavním uzlem AKS. Některá z dat, která prohledává Azure Security Center z prostředí Kubernetes, můžou obsahovat citlivé informace.


## <a name="next-steps"></a>Další kroky

Další informace o funkcích zabezpečení kontejneru Security Center najdete v tématech:

* [Zabezpečení Azure Security Center a kontejneru](container-security.md)

* [Integrace se službou Azure Container Registry](azure-container-registry-integration.md)

* [Správa dat v Microsoftu](https://www.microsoft.com/trust-center/privacy/data-management) – popisuje zásady pro data služeb Microsoftu (včetně Azure, Intune a Microsoft 365), podrobnosti o správě dat od Microsoftu a zásady uchovávání dat, které mají vliv na vaše data.
