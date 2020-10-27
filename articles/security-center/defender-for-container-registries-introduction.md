---
title: Azure Defender pro Registry kontejnerů – výhody a funkce
description: Seznamte se s výhodami a funkcemi Azure Defenderu pro Registry kontejnerů.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 05d7316ceccd67a8eeb1d081843307162ff210c1
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558631"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Seznámení s Azure Defenderem pro Registry kontejnerů

Azure Container Registry (ACR) je spravovaná privátní služba registru Docker, která ukládá a spravuje vaše image kontejnerů pro nasazení Azure v centrálním registru. Vychází z open source registru Docker Registry 2,0.

Pokud chcete chránit všechny Registry založené na Azure Resource Manager ve vašem předplatném, povolte **Azure Defender pro Registry kontejnerů** na úrovni předplatného. Security Center pak bude kontrolovat obrázky, které jsou vloženy do registru, importovány do registru nebo všechny image za posledních 30 dnů. Tato funkce se účtuje na základě obrázku.

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Jaké jsou výhody Azure Defenderu pro Registry kontejnerů?

Security Center v předplatném identifikuje Registry ACR založené na Azure Resource Manager a bezproblémově poskytuje posouzení a správu ohrožení zabezpečení Azure pro image v registru.

**Azure Defender pro Registry kontejnerů** obsahuje skener ohrožení zabezpečení pro kontrolu imagí v Azure Container Registry Registry založeném na Azure Resource Manager a poskytuje hlubší přehled o chybách vašich imagí. Integrovaný skener používá Qualys, což je špičkové ohrožení zabezpečení od dodavatele.

Po nalezení problémů – podle Qualys nebo Security Center – na řídicím panelu Security Center se zobrazí oznámení. U každé chyby zabezpečení Security Center poskytuje užitečná doporučení spolu se klasifikací závažnosti a pokyny, jak problém vyřešit. Podrobnosti o doporučeních Security Center pro kontejnery najdete v [referenčním seznamu doporučení](recommendations-reference.md#recs-containers).

Security Center filtruje a klasifikuje závěry ze skeneru. Když je obrázek v pořádku, Security Center ho označí jako takový. Security Center generuje doporučení zabezpečení pouze pro image, které mají problémy, které mají být vyřešeny. Security Center poskytuje podrobnosti o každé hlášené chybě zabezpečení a klasifikaci závažnosti. Kromě toho poskytuje návod, jak opravit konkrétní chyby zabezpečení nalezené na jednotlivých imagí.

Tím, že se upozorní jenom na problémy, Security Center snižuje potenciální informativní výstrahy.


## <a name="when-are-images-scanned"></a>Kdy jsou naskenované obrázky?

Pro kontrolu imagí jsou k dispozici tři aktivační události:

- Při **vložení** – pokaždé, když se do registru pošle obrázek, Security Center automaticky vyhledá tento obrázek. Pokud chcete spustit kontrolu obrázku, nahrajte ho do úložiště.

- **Nedávno načteno** – protože nové chyby zabezpečení se objevují každý den, **Azure Defender pro Registry kontejnerů** také prohledává všechny image, které byly během posledních 30 dnů vyžádány. Za opětovné prohledání se neúčtují žádné další poplatky; Jak je uvedeno výše, Fakturuje se jednou na obrázek.

- **Při importu** Azure Container Registry nástroje pro import do registru přinášejí image z Docker Hub, Microsoft Container registry nebo jiného služby Azure Container Registry. **Azure Defender pro Registry kontejneru** prohledává všechny podporované bitové kopie, které naimportujete. Další informace najdete v [importu imagí kontejneru do registru kontejneru](../container-registry/container-registry-import-images.md).
 
Kontrola se dokončí obvykle do 2 minut, ale může to trvat až 15 minut. Nálezy jsou dostupné jako Security Center doporučení, jako je tato:

[![Ukázka Azure Security Center doporučení týkající se ohrožení zabezpečení zjištěného v hostované imagi Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Jak Security Center pracovat s Azure Container Registry

Níže je uvedený diagram vysoké úrovně komponent a výhod ochrany registrů pomocí Security Center.

![Přehled vysoké úrovně Azure Security Center a Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>Nejčastější dotazy k Azure Container Registry kontrole imagí

### <a name="how-does-security-center-scan-an-image"></a>Jak Security Center naskenovat obrázek?
Bitová kopie je načítána z registru. Pak se spustí v izolovaném izolovaném prostoru (sandbox) se skenerem Qualys, který extrahuje seznam známých chyb zabezpečení.

Security Center filtruje a klasifikuje závěry ze skeneru. Když je obrázek v pořádku, Security Center ho označí jako takový. Security Center generuje doporučení zabezpečení pouze pro image, které mají problémy, které mají být vyřešeny. Tím, že se upozorní jenom na problémy, Security Center snižuje potenciální informativní výstrahy.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Můžu získat výsledky kontroly prostřednictvím REST API?
Ano. Výsledky se nacházejí v rámci [dílčích posouzení rozhraní REST API](/rest/api/securitycenter/subassessments/list/). Můžete také použít Azure Resource Graph (ARG), Kusto rozhraní API pro všechny vaše prostředky: dotaz může načíst konkrétní kontrolu.

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Jaké typy registru jsou prohledávány? Jaké typy se účtují?
Seznam typů registrů kontejnerů, které Azure Defender podporuje pro Registry kontejnerů, najdete v tématu [dostupnost](defender-for-container-registries-usage.md#availability).

Pokud ke svému předplatnému Azure připojíte nepodporované Registry, nebudou se kontrolovat a nebudou se vám účtovat.

### <a name="can-i-customize-the-findings-from-the-vulnerability-scanner"></a>Můžu přizpůsobit zjištění ze skeneru ohrožení zabezpečení?
Ano. Pokud máte organizaci, kterou je třeba ignorovat, místo toho, aby ji bylo možné opravit, můžete ji případně zakázat. Zakázané závěry neovlivňují vaše zabezpečené skóre nebo generují nežádoucí hluk.

[Seznamte se s vytvářením pravidel, která zakazují zjištění z integrovaného nástroje pro posouzení ohrožení zabezpečení](defender-for-container-registries-usage.md#disable-specific-findings-preview).



## <a name="next-steps"></a>Další kroky

Další informace o funkcích zabezpečení kontejneru Security Center najdete v tématech:

- [Zabezpečení Azure Security Center a kontejneru](container-security.md)

- [Seznámení s Azure Defenderem pro Kubernetes](defender-for-kubernetes-introduction.md)


