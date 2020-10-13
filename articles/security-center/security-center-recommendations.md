---
title: Doporučení zabezpečení v Azure Security Center
description: Tento dokument vás seznámí s tím, jak vám doporučení Azure Security Center pomůžou chránit prostředky Azure a zůstat v souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 0efb2c26a63418835dd2b7561a1fb9d3a9d5fee8
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945549"
---
# <a name="security-recommendations-in-azure-security-center"></a>Doporučení zabezpečení v Azure Security Center 
V tomto tématu se dozvíte, jak zobrazit a pochopit doporučení v Azure Security Center, která vám pomůžou chránit vaše prostředky Azure.


## <a name="what-are-security-recommendations"></a>Co jsou doporučení zabezpečení?

Doporučení jsou akce, které můžete provést při zabezpečení svých prostředků.

Security Center pravidelně analyzuje stav zabezpečení vašich prostředků Azure, aby se identifikovaly potenciální ohrožení zabezpečení. Pak vám poskytne doporučení k nápravě těchto chyb zabezpečení.

Každé doporučení vám poskytne:

- Stručný popis problému
- Kroky k nápravě, které je potřeba provést, aby bylo možné implementovat doporučení.
- Ovlivněné prostředky

## <a name="monitor-recommendations"></a>Doporučení pro monitorování <a name="monitor-recommendations"></a>

Security Center analyzuje stav zabezpečení vašich prostředků, aby identifikoval potenciální ohrožení zabezpečení. 

1. V nabídce Security Center otevřete stránku **doporučení** a podívejte se na doporučení týkající se vašeho prostředí. Doporučení jsou seskupena do ovládacích prvků zabezpečení.

      ![Doporučení seskupená podle řízení zabezpečení](./media/security-center-recommendations/view-recommendations.png)

1. Rozbalením ovládacího prvku a výběrem konkrétního doporučení zobrazíte stránku s podrobnostmi o doporučení.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Stránka s podrobnostmi o doporučení." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    Stránka obsahuje:

    1. **Vynutilit** a **Odepřít** tlačítka pro podporovaná doporučení (viz [Ochrana před neoprávněnými konfiguracemi s doporučeními pro vymáhání/zamítnutí](prevent-misconfigurations.md)
    1. **Indikátor závažnosti**
    1. **Interval aktuálnosti**  (tam, kde je relevantní) 
    1. **Popis** – stručný popis problému
    1. **Postup nápravy** – popis ručních kroků požadovaných k nápravě potíží se zabezpečením u ovlivněných prostředků. Pro doporučení s "rychlou opravou" můžete před použitím navrhované opravy pro vaše prostředky vybrat možnost **Zobrazit logiku oprav** . 
    1. **Ovlivněné prostředky** – vaše prostředky jsou seskupeny do karet:
        - **Prostředky v pořádku** – relevantní prostředky, na které buď nedochází, nebo na které jste už problém napravili.
        - **Zdroje** , které nejsou v pořádku – prostředky, které jsou pořád ovlivněny zjištěným problémem.
        - **Nepoužitelné prostředky** – prostředky, u kterých doporučení nemůže poskytnout definitivní odpověď. Karta netýká se také obsahuje důvody pro každý prostředek. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Stránka s podrobnostmi o doporučení.":::
    1. Tlačítka akcí k nápravě doporučení nebo spuštění aplikace logiky

## <a name="preview-recommendations"></a>Doporučení pro verzi Preview

Doporučení, která jsou označená jako **Náhled** , nejsou zahrnutá do výpočtů zabezpečeného skóre.

Měly by se pořád opravit všude, kde je to možné, takže po skončení období Preview budou přispět ke svému skóre.

Příklad doporučení verze Preview:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Stránka s podrobnostmi o doporučení.":::
 
## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se zavedli k doporučením zabezpečení v Security Center. Další informace o řešení těchto doporučení:

- [Opravit doporučení](security-center-remediate-recommendations.md) – Naučte se konfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
- [Zabraňuje neoprávněným konfiguracím s doporučeními pro vymáhání nebo zakazování](prevent-misconfigurations.md).
