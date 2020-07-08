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
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: f3ef633ff0271d74eea7320faadf17685976d3b6
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970463"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Azure Container Registry integrace s Security Center

Azure Container Registry (ACR) je spravovaná privátní služba registru Docker, která ukládá a spravuje vaše image kontejnerů pro nasazení Azure v centrálním registru. Vychází z open source registru Docker Registry 2,0.

Pokud pracujete na úrovni Standard Azure Security Center, můžete přidat sadu registrů kontejnerů. Tato volitelná funkce přináší hlubší přehled o chybách zabezpečení imagí v registrech založených na ARM. Povolte nebo zakažte sadu na úrovni předplatného tak, aby pokryla všechny Registry v rámci předplatného. Tato funkce se účtuje podle obrázku, jak je znázorněno na [stránce s cenami](security-center-pricing.md). Když zapnete sadu registrů kontejnerů, zajistíte, aby Security Center byla připravená na skenování imagí, které se připravují do registru. 


## <a name="availability"></a>Dostupnost

- Stav vydání: **Obecná dostupnost**
- Požadované role: **Čtenář zabezpečení** a [role čtecího zařízení Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-roles)
- Cloud 
    - ✔ Komerční cloudy
    - Cloud pro státní správu USA ✘
    - ✘ Čína – Cloud pro státní správu, další gov cloudy


## <a name="when-are-images-scanned"></a>Kdy jsou naskenované obrázky?

Pokaždé, když se do registru vloží obrázek, Security Center automaticky vyhledá tento obrázek. Pokud chcete spustit kontrolu obrázku, nahrajte ho do úložiště.

Až se kontrola dokončí (obvykle po přibližně 10 minutách, ale může to být až 40 minut), nálezy jsou dostupné jako Security Center doporučení, jako je:

[![Ukázka Azure Security Center doporučení týkající se ohrožení zabezpečení zjištěného v hostované imagi Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Výhody integrace

Security Center v předplatném identifikuje Registry ACR založené na ARM a hladce poskytuje:

* **Nativní kontrola ohrožení zabezpečení Azure** pro všechny nabízené image Linux. Security Center naskenuje Image pomocí skeneru z špičkové kontroly ohrožení zabezpečení od dodavatele, Qualys. Toto nativní řešení je ve výchozím nastavení bezproblémové integrováno.

* **Doporučení zabezpečení** pro Image Linux se známými chybami zabezpečení. Security Center poskytuje podrobnosti o každé hlášené chybě zabezpečení a klasifikaci závažnosti. Kromě toho poskytuje návod, jak opravit konkrétní chyby zabezpečení nalezené na jednotlivých imagí, které byly vloženy do registru.

![Přehled vysoké úrovně Azure Security Center a Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="acr-with-security-center-faq"></a>ACR s nejčastějšími dotazy k Security Center

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Jaké typy imagí mohou Azure Security Center prohledávat?
Security Center prohledává image založené na operačním systému Linux, které poskytují přístup k prostředí. 

Qualys skener nepodporuje image Super minimalist, jako jsou [pomocné obrázky Docker](https://hub.docker.com/_/scratch/) , nebo Image Distroless, které obsahují jenom vaši aplikaci a její závislosti za běhu bez správce balíčků, prostředí nebo operačního systému.

### <a name="how-does-azure-security-center-scan-an-image"></a>Jak Azure Security Center naskenovat obrázek?
Bitová kopie je načítána z registru. Pak se spustí v izolovaném izolovaném prostoru (sandbox) se skenerem Qualys, který extrahuje seznam známých chyb zabezpečení.

Security Center filtruje a klasifikuje závěry ze skeneru. Když je obrázek v pořádku, Security Center ho označí jako takový. Security Center generuje doporučení zabezpečení pouze pro image, které mají problémy, které mají být vyřešeny. Tím, že se upozorní jenom na problémy, Security Center snižuje potenciální informativní výstrahy.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Jak často Azure Security Center kontrolovat obrázky?
Při každém nabízení se spouštějí kontroly imagí.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Můžu získat výsledky kontroly prostřednictvím REST API?
Ano. Výsledky se nacházejí v rámci [dílčích posouzení rozhraní REST API](/rest/api/securitycenter/subassessments/list/). Můžete také použít Azure Resource Graph (ARG), Kusto rozhraní API pro všechny vaše prostředky: dotaz může načíst konkrétní kontrolu.
 



## <a name="next-steps"></a>Další kroky

Další informace o funkcích zabezpečení kontejneru Security Center najdete v tématech:

* [Zabezpečení Azure Security Center a kontejneru](container-security.md)

* [Integrace se službou Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Ochrana virtuálního počítače](security-center-virtual-machine-protection.md) – popisuje doporučení Security Center.
