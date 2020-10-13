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
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: e17c7d6618d67d66c89875696c5c529af3a85ea9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440557"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Správa a reakce na upozornění zabezpečení ve službě Azure Security Center

V tomto tématu se dozvíte, jak zobrazit a zpracovat výstrahy, které jste obdrželi za účelem ochrany vašich prostředků. 

* Další informace o různých typech výstrah najdete v tématu [typy výstrah zabezpečení](alerts-reference.md).
* Přehled způsobu, jakým Security Center generuje výstrahy, najdete v tématu [jak Azure Security Center detekuje hrozby a reaguje na](security-center-alerts-overview.md)ně.

> [!NOTE]
> Pokud chcete povolit rozšířené zjišťování, povolte Azure Defender. K dispozici je bezplatná zkušební verze. Pokud chcete provést upgrade, v [zásadách zabezpečení](tutorial-security-policy.md) vyberte cenovou úroveň. Další informace najdete v článku o [cenách Azure Security Center](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Co jsou výstrahy zabezpečení?
Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení, jako jsou brány firewall a řešení ochrany koncových bodů, aby se zjistily skutečné hrozby a snížil počet falešných poplachů. Seznam upřednostňovaných výstrah zabezpečení se zobrazí ve službě Security Center spolu s informacemi, které potřebujete k rychlému prozkoumání problému, a doporučeními týkajícími se řešení útoku.

> [!NOTE]
> Další informace o tom, jak funkce zjišťování Security Center fungují, najdete v tématu [jak Azure Security Center detekuje hrozby a reaguje na](security-center-alerts-overview.md#detect-threats)ně.

## <a name="manage-your-security-alerts"></a>Správa výstrah zabezpečení

1. Na řídicím panelu Security Center se na dlaždici  **Ochrana před hrozbami** zobrazí a zobrazí přehled výstrah.

    ![Dlaždice Výstrahy zabezpečení ve službě Security Center](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Pokud chcete zobrazit další podrobnosti o výstrahách, klikněte na dlaždici.

   ![Výstrahy zabezpečení ve službě Security Center](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Chcete-li filtrovat zobrazené výstrahy, klikněte na tlačítko **Filtr**a v okně **filtru** , které se otevře, vyberte možnosti filtru, které chcete použít. Seznam se aktualizuje podle vybraného filtru. Filtrování může být velmi užitečné. Například můžete chtít zabývat se výstrahami zabezpečení, k nimž došlo v posledních 24 hodinách, protože zjišťujete případný průnik do systému.

    ![Filtrování výstrah ve službě Security Center](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Reakce na výstrahy zabezpečení

1. V seznamu **výstrahy zabezpečení** klikněte na výstrahu zabezpečení. Zobrazí se potřebné prostředky a kroky, které je třeba provést k nápravě útoku.

    ![Reakce na výstrahy zabezpečení](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Po kontrole informací klikněte na prostředek, který byl napadený.

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
- [Automatizace odpovědí na výstrahy a doporučení pomocí automatizace pracovních postupů](workflow-automation.md)
