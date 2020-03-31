---
title: Správa výstrah zabezpečení ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže používat funkce služby Azure Security Center ke správě výstrah zabezpečení a reagování na ně.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 6ea951b542d893b8fef3cdf19a964ce2ef8a034d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415707"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Správa a reakce na upozornění zabezpečení ve službě Azure Security Center

Toto téma ukazuje, jak zobrazit a zpracovat výstrahy, které jste obdrželi za účelem ochrany prostředků. 

* Informace o různých typech výstrah najdete v [tématu Typy výstrah zabezpečení](alerts-reference.md).
* Přehled toho, jak Security Center generuje výstrahy, najdete v článku [Jak Azure Security Center detekuje a reaguje na hrozby](security-center-alerts-overview.md).

> [!NOTE]
> Pokud chcete povolit rozšířené detekce, upgradujte na Azure Security Center Standard. K dispozici je bezplatná zkušební verze. Pokud chcete provést upgrade, v [zásadách zabezpečení](tutorial-security-policy.md) vyberte cenovou úroveň. Další informace najdete v článku o [cenách Azure Security Center](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Co jsou výstrahy zabezpečení?
Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z vašich prostředků Azure, sítě a připojených partnerských řešení, jako jsou brány firewall a řešení ochrany koncových bodů, aby se zjistily skutečné hrozby a snížil počet falešných poplachů. Seznam upřednostňovaných výstrah zabezpečení se zobrazí ve službě Security Center spolu s informacemi, které potřebujete k rychlému prozkoumání problému, a doporučeními týkajícími se řešení útoku.

> [!NOTE]
> Další informace o tom, jak fungují funkce zjišťování Centra zabezpečení, najdete v [tématu Jak Azure Security Center detekuje hrozby a reaguje na ně](security-center-alerts-overview.md#detect-threats).

## <a name="manage-your-security-alerts"></a>Správa výstrah zabezpečení

1. Na řídicím panelu Centra zabezpečení najdete v tématu Dlaždice **ochrany před hrozbami,** kde najdete přehled výstrah a jejich přehled.

    ![Dlaždice Výstrahy zabezpečení ve službě Security Center](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Chcete-li zobrazit další podrobnosti o výstrahách, klikněte na dlaždici.

   ![Výstrahy zabezpečení ve službě Security Center](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Chcete-li zobrazit zobrazené výstrahy filtrovat, klepněte na tlačítko **Filtr**a v okně **Filtr,** které se otevře, vyberte možnosti filtru, které chcete použít. Seznam se aktualizuje podle vybraného filtru. Filtrování může být velmi užitečné. Například můžete chtít zabývat se výstrahami zabezpečení, k nimž došlo v posledních 24 hodinách, protože zjišťujete případný průnik do systému.

    ![Filtrování výstrah ve službě Security Center](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Reakce na výstrahy zabezpečení

1. V seznamu **výstrah zabezpečení** klikněte na výstrahu zabezpečení. Zobrazí se související zdroje a kroky, které je třeba podniknout k nápravě útoku.

    ![Reakce na výstrahy zabezpečení](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Po kontrole informací klikněte na prostředek, který byl napaden.

    ![Návrhy, jak dělat výstrahy zabezpečení](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Část **Obecné informace** může nabídnout přehled o tom, co spustilo výstrahu zabezpečení. Zobrazuje informace, jako je cílový prostředek, zdrojová IP adresa (pokud je to možné), pokud je výstraha stále aktivní, a doporučení, jak se napravit.  

    > [!NOTE]
    >V některých případech není zdrojová adresa IP k dispozici, některé protokoly událostí zabezpečení systému Windows neobsahují adresu IP.

1. Postup nápravy navrhovaný službou Security Center se liší podle výstrahy zabezpečení. Postupujte podle nich pro každou výstrahu. 

    V některých případech, za účelem zmírnění výstrahy zabezpečení, budete muset použít jiné ovládací prvky Azure nebo služby k implementaci doporučené nápravy. 

## <a name="see-also"></a>Viz také

V tomto dokumentu jste zjistili, jak ve službě Security Center konfigurovat zásady zabezpečení. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Výstrahy zabezpečení v Centru zabezpečení Azure](security-center-alerts-overview.md).
* [Řešení bezpečnostních incidentů](security-center-incident.md)