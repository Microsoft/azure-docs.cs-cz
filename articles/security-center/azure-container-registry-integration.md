---
title: Azure Security Center a Azure Container Registry | Microsoft Docs
description: Další informace o integraci Azure Security Center s Azure Container Registry
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
ms.openlocfilehash: f0de56f968488f0e5d551ad705cc6f8ca6e7bc47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521862"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Azure Container Registry integrace s Security Center (Preview)

Azure Container Registry (ACR) je spravovaná privátní služba registru Docker, která ukládá a spravuje vaše image kontejnerů pro nasazení Azure v centrálním registru. Vychází z open source registru Docker Registry 2,0.

Pokud používáte ACR společně s úrovní Standard Azure Security Center (viz [ceny](security-center-pricing.md)), získáte lepší přehled o chybách zabezpečení registru a imagí.

[doporučení pro ![Azure Container Registry (ACR) v Azure Security Center](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Výhody integrace

Security Center identifikuje Registry ACR ve vašem předplatném a hladce poskytuje:

* **Nativní kontrola ohrožení zabezpečení Azure** pro všechny nabízené image Linux. Security Center naskenuje Image pomocí skeneru z špičkové kontroly ohrožení zabezpečení od dodavatele, Qualys. Toto nativní řešení je ve výchozím nastavení bezproblémové integrováno.

* **Doporučení zabezpečení** pro Image Linux se známými chybami zabezpečení. Security Center poskytuje podrobnosti o každé hlášené chybě zabezpečení a klasifikaci závažnosti. Kromě toho poskytuje návod, jak opravit konkrétní chyby zabezpečení nalezené na jednotlivých imagí, které byly vloženy do registru.

![Přehled vysoké úrovně Azure Security Center a Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Další kroky

Další informace o funkcích zabezpečení kontejneru Security Center najdete v tématech:

* [Zabezpečení Azure Security Center a kontejneru](container-security.md)

* [Integrace se službou Azure Kubernetes](azure-kubernetes-service-integration.md)

* [Ochrana virtuálního počítače](security-center-virtual-machine-protection.md) – popisuje doporučení Security Center.