---
title: Azure Security Center a Azure Container Registry
description: Další informace o kontrole registrů kontejnerů pomocí Azure Security Center
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
ms.openlocfilehash: 1335b1034304b7efe2b113f7ff2d2927fea41638
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977369"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Azure Container Registry skenování imagí Security Center

Azure Container Registry (ACR) je spravovaná privátní služba registru Docker, která ukládá a spravuje vaše image kontejnerů pro nasazení Azure v centrálním registru. Vychází z open source registru Docker Registry 2,0.

Povolte **Azure Defender pro Registry kontejnerů** , abyste měli hlubší přehled o chybách zabezpečení imagí v registrech založených na Azure Resource Manager. Povolte nebo zakažte plán na úrovni předplatného, aby pokryl všechny Registry v rámci předplatného. Tato funkce se účtuje podle obrázku, jak je znázorněno na [stránce s cenami](security-center-pricing.md). Když povolíte Azure Defender, zajistíte, aby byla Security Center připravená na skenování imagí, které se připravují do registru. 


## <a name="when-are-images-scanned"></a>Kdy jsou naskenované obrázky?

Pokaždé, když se do registru vloží obrázek, Security Center automaticky vyhledá tento obrázek. Pokud chcete spustit kontrolu obrázku, nahrajte ho do úložiště.

Až se kontrola dokončí (obvykle přibližně 2 minuty, ale může to být až 15 minut), nálezy jsou dostupné jako Security Center doporučení, jako je:

[![Ukázka Azure Security Center doporučení týkající se ohrožení zabezpečení zjištěného v hostované imagi Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Výhody integrace

Security Center identifikuje Registry ACR založené na Azure Resource Manager v předplatném a hladce poskytuje:

* **Nativní kontrola ohrožení zabezpečení Azure** pro všechny nabízené image Linux. Security Center naskenuje Image pomocí skeneru z špičkové kontroly ohrožení zabezpečení od dodavatele, Qualys. Toto nativní řešení je ve výchozím nastavení bezproblémové integrováno.

* **Doporučení zabezpečení** pro Image Linux se známými chybami zabezpečení. Security Center poskytuje podrobnosti o každé hlášené chybě zabezpečení a klasifikaci závažnosti. Kromě toho poskytuje návod, jak opravit konkrétní chyby zabezpečení nalezené na jednotlivých imagí, které byly vloženy do registru.

![Přehled vysoké úrovně Azure Security Center a Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Nejčastější dotazy k Azure Container Registry kontrole imagí

### <a name="how-does-security-center-scan-an-image"></a>Jak Security Center naskenovat obrázek?
Bitová kopie je načítána z registru. Pak se spustí v izolovaném izolovaném prostoru (sandbox) se skenerem Qualys, který extrahuje seznam známých chyb zabezpečení.

Security Center filtruje a klasifikuje závěry ze skeneru. Když je obrázek v pořádku, Security Center ho označí jako takový. Security Center generuje doporučení zabezpečení pouze pro image, které mají problémy, které mají být vyřešeny. Tím, že se upozorní jenom na problémy, Security Center snižuje potenciální informativní výstrahy.

### <a name="how-often-does-security-center-scan-my-images"></a>Jak často Security Center kontrolovat obrázky?
Při každém nabízení se spouštějí kontroly imagí.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Můžu získat výsledky kontroly prostřednictvím REST API?
Ano. Výsledky se nacházejí v rámci [dílčích posouzení rozhraní REST API](/rest/api/securitycenter/subassessments/list/). Můžete také použít Azure Resource Graph (ARG), Kusto rozhraní API pro všechny vaše prostředky: dotaz může načíst konkrétní kontrolu.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Jaké typy registru jsou prohledávány? Jaké typy se účtují?
V části dostupnost jsou uvedené typy registrů kontejnerů, které Azure Defender podporuje pro Registry kontejnerů. 

Pokud jsou registry, které nejsou podporované, připojené k vašemu předplatnému Azure, nebudou se kontrolovat a nebudou se vám účtovat.


## <a name="next-steps"></a>Další kroky

Další informace o funkcích zabezpečení kontejneru Security Center najdete v tématech:

* [Zabezpečení Azure Security Center a kontejneru](container-security.md)

* [Integrace se službou Azure Kubernetes Service](azure-kubernetes-service-integration.md)


