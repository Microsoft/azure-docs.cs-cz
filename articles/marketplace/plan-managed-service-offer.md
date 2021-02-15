---
title: Plánování nabídky spravované služby pro komerční tržiště Microsoftu
description: Postup plánování nové nabídky spravované služby pro Azure Marketplace pomocí programu komerčního tržiště v partnerském centru Microsoftu.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.date: 12/23/2020
ms.openlocfilehash: f096e53f8054039f361bde1c5f2adffac615c53d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371936"
---
# <a name="how-to-plan-a-managed-service-offer-for-the-microsoft-commercial-marketplace"></a>Postup plánování nabídky spravované služby pro komerční tržiště Microsoftu

V tomto článku se seznámíte s požadavky pro publikování nabídky spravované služby na komerční tržišti společnosti Microsoft prostřednictvím partnerského centra.

Spravované služby jsou Azure Marketplace nabídky, které umožňují správu více tenantů a víceklientské architektury s Azure Lighthouse. Další informace najdete v tématu [co je Azure Lighthouse?](../lighthouse/overview.md) Když zákazník koupí nabídku spravované služby, může delegovat jedno nebo víc předplatných nebo skupin prostředků. Pak můžete na těchto prostředcích pracovat pomocí možností [správy delegovaných prostředků Azure](../lighthouse/concepts/azure-delegated-resource-management.md) v Azure Lighthouse.

## <a name="eligibility-requirements"></a>Požadavky na nárok

Pokud chcete publikovat nabídku spravované služby, musíte mít na cloudové platformě vytvořenou zlatou nebo stříbrné kompetence Microsoftu. Tato kompetence znázorňuje vaše znalosti pro zákazníky. Další informace najdete v tématu [Microsoft Partner Network kompetence](https://partner.microsoft.com/membership/competencies).

Nabídky musí splňovat všechny použitelné [zásady certifikace komerčního tržiště](/legal/marketplace/certification-policies) , které se mají publikovat na Azure Marketplace.

## <a name="customer-leads"></a>Potenciální zákazníci

Abyste mohli shromažďovat informace o zákaznících, musíte vaši nabídku připojit k systému správy vztahů se zákazníky (CRM). Zákazník bude požádán o oprávnění ke sdílení svých informací. Tyto podrobnosti o zákaznících spolu s názvem nabídky, ID a online obchodem, kde si vaši nabídku našli, budou odeslány do systému CRM, který jste nakonfigurovali. Komerční tržiště podporuje různé druhy systémů CRM, společně s možností použít tabulku Azure nebo nakonfigurovat koncový bod HTTPS pomocí Power automatu.

Připojení k CRM můžete kdykoli přidat nebo změnit během vytváření nabídky nebo po ní. Podrobné pokyny najdete v [zákaznických zákaznících z nabídky komerčních webů na webu Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Právní smlouvy

Na stránce vlastnosti v partnerském centru budete požádáni o zadání podmínek **a ujednání** pro použití vaší nabídky. Můžete buď zadat své výrazy přímo do partnerského centra, nebo zadat adresu URL, kde se dají najít. Zákazníci budou před nákupem vaší nabídky muset přijmout tyto podmínky a ujednání.

## <a name="offer-listing-details"></a>Podrobnosti o seznamu nabídek

Když vytvoříte nabídku spravované služby v partnerském centru, zadáte text, obrázky, dokumenty a další podrobnosti o nabídce. Tím se zákazníkům zobrazí při zjištění vaší nabídky na Azure Marketplace. Prohlédněte si následující příklad:

:::image type="content" source="media/example-managed-service.png" alt-text="Ukazuje, jak se na Azure Marketplace zobrazuje nabídka spravované služby.":::

**Popisy volání**

1. Logo
1. Name
1. Krátký popis
1. Kategorie
1. Právní smlouvy a zásady ochrany osobních údajů
1. Description
1. Snímky obrazovky a videa
1. Užitečné odkazy

Tady je příklad, jak se seznam nabídek zobrazuje v Azure Portal:

:::image type="content" source="media/example-managed-service-azure-portal.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Portal.":::

**Popisy volání**

1. Název
2. Description
3. Užitečné odkazy
4. Snímky obrazovky a videa

> [!NOTE]
> Pokud je vaše nabídka v jiném jazyce než v angličtině, seznam nabídek může být v tomto jazyce, ale popis musí začínat nebo končit anglickou frází "Tato služba je k dispozici v &lt; jazyce obsahu vaší nabídky>. Podpůrné dokumenty můžete také poskytnout v jazyce, který se liší od toho, který se používá v podrobnostech seznamu nabídky.

Abyste mohli snadněji vytvořit nabídku, připravte si některé z těchto položek předem. Pokud není uvedeno jinak, jsou vyžadovány následující položky.

**Název**: Tato funkce se zobrazí jako název vaší nabídky na komerčním webu Marketplace. Název může být ve vaší společnosti. Nemůže obsahovat Emoji (Pokud se nejedná o symboly ochranných známek a autorských práv) a musí být omezené na 50 znaků.

**Shrnutí výsledků hledání**: popište účel nebo cíl vaší nabídky na 100 znaků nebo méně. Tento souhrn se používá ve výsledcích hledání ve výpisech z komerčního tržiště. Neměl by být stejný jako název. Zvažte zahrnutí nejdůležitějších klíčových slov SEO.

**Krátký popis**: zadejte krátký popis vaší nabídky (až 256 znaků). Zobrazí se na seznamu nabídek v Azure Portal.

**Popis**: Popište svou nabídku maximálně na 3 000 znaků. Tento popis se zobrazí v seznamu komerčního tržiště. Zvažte zahrnutí hodnoty do umístění, klíčové výhody, kategorie nebo sdružení v oboru a veškeré nezbytné zveřejnění.

Zde jsou některé tipy pro psaní popisu:

* Jasně popište hodnotu vaší nabídky v prvních několika větách, včetně:
    * Typ uživatele, který je z nabídky výhodou
    * Co zákazník potřebuje nebo vydá adresy nabídky.
* Pamatujte, že ve výsledcích hledání se může zobrazit několik prvních vět.
* Použijte slovník specifický pro obor.

K formátování popisu můžete použít značky HTML. Informace o formátování HTML najdete v tématu [značky HTML podporované v popisech nabídky komerčních webů na webu Marketplace](./supported-html-tags.md).

**Odkaz zásady ochrany osobních údajů**: zadejte adresu URL zásad ochrany osobních údajů, která je hostovaná na vašem webu. Zodpovídáte za to, že vaše nabídka vyhovuje předpisům a zákonům o ochraně osobních údajů a poskytuje platné zásady ochrany osobních údajů.

**Užitečné odkazy** (volitelné): nahrání dalších online dokumentů o vaší nabídce.

**Kontaktní informace**: Zadejte jméno, e-mailovou adresu a telefonní číslo dvou lidí ve vaší společnosti (můžete být jeden z nich): kontakt na podporu a technický kontakt. Tyto informace budeme používat ke komunikaci s vámi o vaší nabídce. Tyto informace se zákazníkům nezobrazují, ale můžou být poskytovány partnerům Cloud Solution Provider (CSP).

**Adresy URL podpory** (volitelné): Pokud máte weby podpory pro globální zákazníky Azure a/nebo Azure Government zákazníky, zadejte tyto adresy URL.

**Média Marketplace – loga**: Poskytněte soubor PNG pro logo velké velikosti vaší nabídky. Partnerské centrum ho bude používat k vytváření středních a malých log. Volitelně můžete tato loga nahradit jiným obrázkem později.

* V seznamu nabídek na Azure Marketplace se zobrazí velké logo (od 216 x 216 do 350 × 350 px).
* Při vytvoření nového prostředku se zobrazí logo střední (90 x 90 pixelů).
* Ve výsledcích hledání Azure Marketplace se používá malé logo (48 x 48 px).

Postupujte podle těchto pokynů pro vaše loga:

* Ujistěte se, že obrázek není roztažen.
* Design Azure má jednoduchou paletu barev. Omezte počet primárních a sekundárních barev ve vašem logu.
* Azure Portal barvy jsou bílé a černé. Nepoužívejte je jako pozadí loga. Doporučujeme jednoduché primární barvy, které povedou k výraznému logo.
* Pokud použijete průhledné pozadí, ujistěte se, že text ani logo nejsou bílé, černé nebo modré.
* Vzhled a chování loga by mělo být ploché. Vyhněte se přechodům. Na logo neumisťujte žádný text, a to ani název vaší společnosti nebo název značky.

**Media Marketplace – snímky obrazovky** (volitelné): přidejte až pět imagí, které ukazují, jak vaše nabídka funguje. Všechny obrázky musí mít velikost 1280 × 720 pixelů a v. Formát PNG.

**Média Marketplace – videa** (volitelné): Nahrajte až pět videí, která předvádí vaši nabídku. Videa musí být hostována na YouTube nebo Vimeo a mají miniaturu (soubor PNG 1280 x 720).

## <a name="preview-audience"></a>Cílová skupina Preview

Cílová skupina verze Preview má přístup k vaší nabídce před tím, než se publikuje na Azure Marketplace, aby ji bylo možné otestovat. Na stránce **Preview cílové skupiny** v partnerském centru můžete definovat skupinu omezené verze Preview.

> [!NOTE]
> Cílová skupina verze Preview se liší od privátního plánu. Soukromý plán je ten, který zpřístupníte jenom pro konkrétní cílovou skupinu, kterou zvolíte. To vám umožní vyjednat vlastní plán s konkrétními zákazníky.

Pozvánky můžete odesílat do e-mailových adres účtu Microsoft (MSA) nebo Azure Active Directory (Azure AD). Přidejte až 10 e-mailových adres ručně nebo importujte až 20 souborů. csv. Pokud je vaše nabídka už živá, můžete i nadále definovat cílovou skupinu verze Preview pro testování změn nebo aktualizací vaší nabídky.

## <a name="plans-and-pricing"></a>Plány a ceny

Nabídka spravované služby vyžaduje aspoň jeden plán. Plán definuje rozsah řešení, omezení a související ceny, pokud jsou k dispozici. Můžete vytvořit několik plánů pro vaši nabídku, abyste svým zákazníkům poskytli různé technické a cenové možnosti. Obecné pokyny k plánům, včetně soukromých plánů, najdete v tématu [plány a ceny pro komerční nabídky na webu Marketplace](plans-pricing.md).

Spravované služby podporují jenom jeden cenový model: **Přineste si vlastní licenci (BYOL)**. To znamená, že vaše zákazníky budete účtovat přímo a společnost Microsoft vám nebude účtovat žádné poplatky.

## <a name="next-steps"></a>Další kroky

* [Vytvoření nabídky spravované služby](./create-managed-service-offer.md)
* [Osvědčené postupy nabídky](./gtm-offer-listing-best-practices.md)