---
title: Doporučení zabezpečení v Azure Security Center
description: Tento dokument vás seznámí s tím, jak vám doporučení Azure Security Center pomůžou chránit prostředky Azure a zůstat v souladu se zásadami zabezpečení.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 57760443746e111750e74ef55fc18729f6ba32c4
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100334"
---
# <a name="review-your-security-recommendations"></a>Kontrola doporučení zabezpečení

V tomto tématu se dozvíte, jak zobrazit a pochopit doporučení v Azure Security Center, která vám pomůžou chránit vaše prostředky Azure.

## <a name="monitor-recommendations"></a>Doporučení pro monitorování <a name="monitor-recommendations"></a>

Security Center analyzuje stav zabezpečení vašich prostředků, aby identifikoval potenciální ohrožení zabezpečení. 

1. V nabídce Security Center otevřete stránku **doporučení** a podívejte se na doporučení týkající se vašeho prostředí. Doporučení jsou seskupena do ovládacích prvků zabezpečení.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Doporučení seskupená podle řízení zabezpečení" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Pokud chcete najít doporučení specifická pro typ prostředku, závažnost, prostředí nebo další kritéria, která jsou pro vás důležitá, použijte volitelné filtry nad seznamem doporučení.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Filtry pro upřesnění seznamu Azure Security Center doporučení":::

1. Rozbalením ovládacího prvku a výběrem konkrétního doporučení zobrazíte stránku s podrobnostmi o doporučení.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Stránka s podrobnostmi o doporučení." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    Stránka obsahuje:

    1. Pro podporovaná doporučení na horním panelu nástrojů se zobrazí některá nebo všechna následující tlačítka:
        - **Vyhovět** a **zamítnout** (viz [Ochrana před neoprávněnými konfiguracemi s doporučeními pro vymáhání/zamítnutí](prevent-misconfigurations.md)
        - Pokud chcete přejít přímo k položce Azure Policy pro základní zásady, **Zobrazte definici zásad.**
    1. **Indikátor závažnosti**
    1. **Interval aktuálnosti** (tam, kde je relevantní)
    1. **Počet vyloučených prostředků** , pokud pro toto doporučení existují výjimky, zobrazuje se počet vyloučených prostředků.
    1. **Popis** – stručný popis problému
    1. **Postup nápravy** – popis ručních kroků požadovaných k nápravě potíží se zabezpečením u ovlivněných prostředků. Pro doporučení s "rychlou opravou" můžete před použitím navrhované opravy pro vaše prostředky vybrat možnost **Zobrazit logiku oprav** . 
    1. **Ovlivněné prostředky** – vaše prostředky jsou seskupeny do karet:
        - **Prostředky v pořádku** – relevantní prostředky, na které buď nedochází, nebo na které jste už problém napravili.
        - **Zdroje** , které nejsou v pořádku – prostředky, které jsou pořád ovlivněny zjištěným problémem.
        - **Nepoužitelné prostředky** – prostředky, u kterých doporučení nemůže poskytnout definitivní odpověď. Karta netýká se také obsahuje důvody pro každý prostředek. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Z důvodů nelze použít prostředky.":::
    1. Tlačítka akcí k nápravě doporučení nebo spuštění aplikace logiky

## <a name="preview-recommendations"></a>Doporučení pro verzi Preview

Doporučení, která jsou označená jako **Náhled** , nejsou zahrnutá do výpočtů zabezpečeného skóre.

Měly by se pořád opravit všude, kde je to možné, takže po skončení období Preview budou přispět ke svému skóre.

Příklad doporučení verze Preview:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Doporučení s příznakem Preview":::
 
## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se zavedli k doporučením zabezpečení v Security Center. Související informace:

- [Opravit doporučení](security-center-remediate-recommendations.md)– Naučte se konfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
- [Zabraňuje neoprávněným konfiguracím s doporučeními pro vymáhání nebo zakazování](prevent-misconfigurations.md).
- [Automatizace odpovědí na aktivační události Security Center](workflow-automation.md)– automatizace odpovědí na doporučení
- [Vyloučení prostředku z doporučení](exempt-resource.md)
- [Doporučení k zabezpečení – Referenční příručka](recommendations-reference.md)