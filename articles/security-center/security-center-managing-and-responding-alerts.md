---
title: Správa výstrah zabezpečení ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže používat funkce služby Azure Security Center ke správě výstrah zabezpečení a reagování na ně.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: a47ece762f2df3fa18cf2b79d338c28d4069c4ad
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633467"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Správa a reakce na výstrahy zabezpečení v Azure Security Center

V tomto tématu se dozvíte, jak zobrazit a zpracovat výstrahy Security Center a chránit vaše prostředky.

Rozšířené detekce, které aktivují výstrahy zabezpečení, jsou k dispozici pouze v Azure Defenderu. K dispozici je bezplatná zkušební verze. Informace o upgradu najdete v tématu [Povolení Azure Defenderu](security-center-pricing.md#enable-azure-defender).

## <a name="what-are-security-alerts"></a>Co jsou výstrahy zabezpečení?
Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení, jako jsou brány firewall a řešení ochrany koncových bodů, aby se zjistily skutečné hrozby a snížil počet falešných poplachů. Seznam upřednostňovaných výstrah zabezpečení se zobrazí ve službě Security Center spolu s informacemi, které potřebujete k rychlému prozkoumání problému, a doporučeními týkajícími se řešení útoku.

Další informace o různých typech výstrah najdete v tématu [výstrahy zabezpečení – referenční příručka](alerts-reference.md).

Přehled způsobu, jakým Security Center generuje výstrahy, najdete v tématu [jak Azure Security Center detekuje hrozby a reaguje na](security-center-alerts-overview.md)ně.


## <a name="manage-your-security-alerts"></a>Správa výstrah zabezpečení

1. Na stránce Přehled Security Center vyberte dlaždici **výstrahy zabezpečení** v horní části stránky nebo odkaz z tohoto bočního panelu.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="Získání stránky s výstrahami zabezpečení ze stránky s přehledem Azure Security Center":::

    Otevře se stránka výstrahy zabezpečení.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Seznam výstrah zabezpečení Azure Security Center":::

1. Chcete-li filtrovat seznam výstrah, vyberte některý z příslušných filtrů. Volitelně můžete přidat další filtry pomocí možnosti **Přidat filtr** .

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="Přidání filtrů do zobrazení výstrah" lightbox="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-large.png":::

    Seznam se aktualizuje podle možností filtrování, které jste vybrali. Filtrování může být velmi užitečné. Například můžete chtít zabývat se výstrahami zabezpečení, k nimž došlo v posledních 24 hodinách, protože zjišťujete případný průnik do systému.


## <a name="respond-to-security-alerts"></a>Reakce na výstrahy zabezpečení

1. V seznamu **výstrahy zabezpečení** vyberte výstrahu. Zobrazí se boční podokno s popisem výstrahy a všech ovlivněných prostředků. 

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-details-pane.png" alt-text="Zobrazení zkrácených podrobností výstrahy zabezpečení":::

    > [!TIP]
    > Po otevření tohoto bočního podokna můžete rychle zkontrolovat seznam výstrah pomocí šipek nahoru a dolů na své klávesnici.

1. Chcete-li získat další informace, vyberte možnost **Zobrazit úplné podrobnosti**.

    V levém podokně stránky výstraha zabezpečení jsou uvedeny informace vysoké úrovně týkající se výstrahy zabezpečení: název, závažnost, stav, čas aktivity, popis podezřelé aktivity a ovlivněný prostředek. Společně s ovlivněným prostředkem jsou značky Azure relevantní pro daný prostředek. Použijte k odvození organizačního kontextu prostředku při zkoumání výstrahy.

    Pravé podokno obsahuje kartu **Podrobnosti výstrahy** s dalšími podrobnostmi výstrahy, které vám pomůžou problém prozkoumat: IP adresy, soubory, procesy a další.
     
    ![Návrhy, co dělat s výstrahami zabezpečení](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Také v pravém podokně se nachází karta **provést akci** . Tato karta slouží k provedení dalších akcí týkajících se výstrahy zabezpečení. Akce, jako například:
    - *Zmírnění hrozby* – poskytuje postup ruční nápravy pro tuto výstrahu zabezpečení.
    - *Prevence budoucích útoků* – poskytuje doporučení zabezpečení, která vám pomůžou snížit plochu pro útoky, zvýšit stav zabezpečení a zabránit budoucím útokům.
    - *Aktivovat automatizovanou odezvu* – poskytuje možnost aktivovat aplikaci logiky jako reakci na toto upozornění zabezpečení.
    - *Potlačit podobné výstrahy* – poskytuje možnost potlačit budoucí výstrahy s podobnými charakteristikami, pokud výstraha není relevantní pro vaši organizaci.

    ![Provést kartu akce](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Viz také

V tomto dokumentu jste zjistili, jak zobrazit výstrahy zabezpečení. Související materiály najdete na následujících stránkách:

- [Konfigurace pravidel potlačení výstrah](alerts-suppression-rules.md)
- [Automatizace odpovědí na triggery Security Center](workflow-automation.md)
- [Upozornění zabezpečení – Referenční příručka](alerts-reference.md)
