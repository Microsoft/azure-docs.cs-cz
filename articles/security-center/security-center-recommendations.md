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
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 3b2f111f83dbd731b69671e58d4bf9dc648a596f
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526490"
---
# <a name="security-recommendations-in-azure-security-center"></a>Doporučení zabezpečení v Azure Security Center 

V tomto tématu se dozvíte, jak zobrazit a pochopit doporučení v Azure Security Center, která vám pomůžou chránit vaše prostředky Azure.


## <a name="what-are-security-recommendations"></a>Co jsou doporučení zabezpečení?

Security Center pravidelně analyzuje stav zabezpečení vašich prostředků Azure, aby se identifikovaly potenciální ohrožení zabezpečení. Pak vám poskytne doporučení k nápravě těchto chyb zabezpečení.

Doporučení jsou akce, které můžete provést při zabezpečení a posílení prostředků. 

Každé doporučení vám poskytne:

- Stručný popis problému
- Kroky k nápravě, které je potřeba provést, aby bylo možné implementovat doporučení.
- Ovlivněné prostředky

## <a name="how-does-microsoft-decide-what-needs-securing-and-hardening"></a>Jak Microsoft rozhodne, co potřebuje k zabezpečení a posílení zabezpečení?

Doporučení pro Security Center jsou založená na testu zabezpečení Azure. Téměř každé doporučení má základní zásadu, která je odvozena z požadavku ve srovnávacím testu.

Srovnávací test zabezpečení Azure je Microsoftem vytvořená sada pokynů pro zabezpečení a osvědčené postupy na základě běžných architektur dodržování předpisů, které jsou specifické pro Azure. Toto široce uznávané sestavování srovnávacích testů v ovládacích prvcích z [centra pro Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) se soustředí na zabezpečení zaměřené na Cloud. Přečtěte si další informace o [srovnávacím testu zabezpečení Azure](../security/benchmarks/introduction.md).

Při prohlížení podrobností doporučení je často užitečné, abyste mohli zobrazit základní zásady. Pro každé doporučení podporované zásadou použijte odkaz **Zobrazit definici zásad** ze stránky s podrobnostmi doporučení a přejít přímo na položku Azure Policy pro příslušné zásady:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Odkaz na stránku Azure Policy pro konkrétní zásadu podporující doporučení":::

Pomocí tohoto odkazu můžete zobrazit definici zásady a zkontrolovat logiku hodnocení. 

Pokud si prohlédnete seznam doporučení v našich [doporučeních o zabezpečení](recommendations-reference.md), přečtěte si také odkazy na stránky definice zásad:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Přístup na stránku Azure Policy pro konkrétní zásadu přímo z referenční stránky Azure Security Center doporučení":::

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