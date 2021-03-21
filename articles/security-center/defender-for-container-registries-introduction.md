---
title: Azure Defender pro Registry kontejnerů – výhody a funkce
description: Seznamte se s výhodami a funkcemi Azure Defenderu pro Registry kontejnerů.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ab2ad15da9b1676924197d28e734f6baf59a02ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176633"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Úvod do Azure Defenderu pro registry kontejnerů

Azure Container Registry (ACR) je spravovaná privátní služba registru Docker, která ukládá a spravuje vaše image kontejnerů pro nasazení Azure v centrálním registru. Vychází z open source registru Docker Registry 2,0.

Pokud chcete chránit všechny Registry založené na Azure Resource Manager ve vašem předplatném, povolte **Azure Defender pro Registry kontejnerů** na úrovni předplatného. Security Center pak bude kontrolovat obrázky, které jsou vloženy do registru, importovány do registru nebo všechny image za posledních 30 dnů. Tato funkce se účtuje na základě obrázku.

[!INCLUDE [Defender for container registries availability info](../../includes/security-center-availability-defender-for-container-registries.md)]

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Jaké jsou výhody Azure Defenderu pro Registry kontejnerů?

Security Center v předplatném identifikuje Registry ACR založené na Azure Resource Manager a bezproblémově poskytuje posouzení a správu ohrožení zabezpečení Azure pro image v registru.

**Azure Defender pro Registry kontejnerů** obsahuje skener ohrožení zabezpečení pro kontrolu imagí v Azure Container Registry Registry založeném na Azure Resource Manager a poskytuje hlubší přehled o chybách vašich imagí. Integrovaný skener používá Qualys, což je špičkové ohrožení zabezpečení od dodavatele.

Po nalezení problémů – podle Qualys nebo Security Center – na řídicím panelu Security Center se zobrazí oznámení. U každé chyby zabezpečení Security Center poskytuje užitečná doporučení spolu se klasifikací závažnosti a pokyny, jak problém vyřešit. Podrobnosti o doporučeních Security Center pro kontejnery najdete v [referenčním seznamu doporučení](recommendations-reference.md#recs-compute).

Security Center filtruje a klasifikuje závěry ze skeneru. Když je obrázek v pořádku, Security Center ho označí jako takový. Security Center generuje doporučení zabezpečení pouze pro image, které mají problémy, které mají být vyřešeny. Security Center poskytuje podrobnosti o každé hlášené chybě zabezpečení a klasifikaci závažnosti. Kromě toho poskytuje návod, jak opravit konkrétní chyby zabezpečení nalezené na jednotlivých imagí.

Tím, že se upozorní jenom na problémy, Security Center snižuje potenciální informativní výstrahy.


> [!TIP]
> Další informace o funkcích zabezpečení kontejneru Security Center najdete v tématech:
>
> - [Zabezpečení Azure Security Center a kontejneru](container-security.md)
> - [Seznámení s Azure Defenderem pro Kubernetes](defender-for-kubernetes-introduction.md)

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
Security Center načte image z registru a spustí ji v izolovaném prostoru izolovaného prostoru se skenerem Qualys. Skener extrahuje seznam známých chyb zabezpečení.

Security Center filtruje a klasifikuje závěry ze skeneru. Když je obrázek v pořádku, Security Center ho označí jako takový. Security Center generuje doporučení zabezpečení pouze pro image, které mají problémy, které mají být vyřešeny. Tím, že vás upozorní jenom v případě, že dojde k problémům, Security Center snižuje potenciál nevyžádaných informativních výstrah.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Můžu získat výsledky kontroly prostřednictvím REST API?
Ano. Výsledky jsou k dispozici v [rozhraní REST API pro dílčí posouzení](/rest/api/securitycenter/subassessments/list/). Můžete také použít Azure Resource Graph (ARG), Kusto rozhraní API pro všechny vaše prostředky: dotaz může načíst konkrétní kontrolu.

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Jaké typy registru jsou prohledávány? Jaké typy se účtují?
Seznam typů registrů kontejnerů, které Azure Defender podporuje pro Registry kontejnerů, najdete v tématu [dostupnost](#availability).

Pokud k předplatnému Azure připojíte nepodporované Registry, Azure Defender je nebude kontrolovat a nebude vám je účtovat.

### <a name="can-i-customize-the-findings-from-the-vulnerability-scanner"></a>Můžu přizpůsobit zjištění ze skeneru ohrožení zabezpečení?
Ano. Pokud máte organizaci, kterou je třeba ignorovat, místo toho, aby ji bylo možné opravit, můžete ji případně zakázat. Zakázané závěry neovlivňují vaše zabezpečené skóre nebo generují nežádoucí hluk.

[Seznamte se s vytvářením pravidel, která zakazují zjištění z integrovaného nástroje pro posouzení ohrožení zabezpečení](defender-for-container-registries-usage.md#disable-specific-findings-preview).

### <a name="why-is-security-center-alerting-me-to-vulnerabilities-about-an-image-that-isnt-in-my-registry"></a>Proč Security Center upozorňuje na ohrožení zabezpečení týkající se image, která není v registru?
Security Center poskytuje posouzení ohrožení zabezpečení pro každý nabízený nebo vyžádaný obrázek v registru. Některé obrázky mohou znovu použít značky z obrázku, který již byl zkontrolován. Můžete například změnit přiřazení značky "poslední" pokaždé, když přidáte obrázek do Digest. V takových případech stále existuje image Old v registru a je možné ji i nadále vyhlašovat její hodnotou Digest. Pokud bitová kopie obsahuje zjištění zabezpečení a je vyžádána, zveřejňuje chyby zabezpečení.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Skenování imagí pro chyby zabezpečení](defender-for-container-registries-usage.md)