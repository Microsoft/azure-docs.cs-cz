---
title: Azure Security Center a Azure Container Registry
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
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: bb004d536d30d62116866581daeef9b42c23d470
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76936369"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Azure Container Registry integrace s Security Center (Preview)

Azure Container Registry (ACR) je spravovaná privátní služba registru Docker, která ukládá a spravuje vaše image kontejnerů pro nasazení Azure v centrálním registru. Vychází z open source registru Docker Registry 2,0.

Pokud pracujete na úrovni Standard Azure Security Center, můžete přidat sadu registrů kontejnerů. Tato volitelná funkce přináší hlubší přehled o chybách zabezpečení imagí v registrech založených na ARM. Povolte nebo zakažte sadu na úrovni předplatného tak, aby pokryla všechny Registry v rámci předplatného. Tato funkce se účtuje na základě obrázku, ne podle kontroly, jak je znázorněno na [stránce s cenami](security-center-pricing.md). 

Když zapnete sadu registrů kontejnerů, zajistíte, aby Security Center byla připravená na skenování imagí, které se připravují do registru. Kontroly jsou na úrovni obrázku: Security Center nekontrolují registr, hledá Image uložené v registru. 

Pokaždé, když se do registru vloží obrázek, Security Center automaticky vyhledá tento obrázek. Pokud chcete spustit kontrolu obrázku, nahrajte ho do úložiště.

Až se kontrola dokončí (obvykle po přibližně 10 minutách), výsledky jsou k dispozici v Security Center doporučení, jako je tato:

[Ukázka ![Azure Security Center doporučení týkající se ohrožení zabezpečení zjištěného v hostované imagi Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Výhody integrace

Security Center v předplatném identifikuje Registry ACR založené na ARM a hladce poskytuje:

* **Nativní kontrola ohrožení zabezpečení Azure** pro všechny nabízené image Linux. Security Center naskenuje Image pomocí skeneru z špičkové kontroly ohrožení zabezpečení od dodavatele, Qualys. Toto nativní řešení je ve výchozím nastavení bezproblémové integrováno.

* **Doporučení zabezpečení** pro Image Linux se známými chybami zabezpečení. Security Center poskytuje podrobnosti o každé hlášené chybě zabezpečení a klasifikaci závažnosti. Kromě toho poskytuje návod, jak opravit konkrétní chyby zabezpečení nalezené na jednotlivých imagí, které byly vloženy do registru.

![Přehled vysoké úrovně Azure Security Center a Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Další kroky

Další informace o funkcích zabezpečení kontejneru Security Center najdete v tématech:

* [Zabezpečení Azure Security Center a kontejneru](container-security.md)

* [Integrace se službou Azure Kubernetes](azure-kubernetes-service-integration.md)

* [Ochrana virtuálního počítače](security-center-virtual-machine-protection.md) – popisuje doporučení Security Center.
