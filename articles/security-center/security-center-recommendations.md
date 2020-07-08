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
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 4d65b43dad80cb130d582132d21e2d10bd8051dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791380"
---
# <a name="security-recommendations-in-azure-security-center"></a>Doporučení zabezpečení v Azure Security Center 
V tomto tématu se dozvíte, jak zobrazit a pochopit doporučení v Azure Security Center, která vám pomůžou chránit vaše prostředky Azure.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Tento dokument není pokrokový průvodce.
>

## <a name="what-are-security-recommendations"></a>Co jsou doporučení zabezpečení?

Doporučení jsou akce, které můžete provést při zabezpečení svých prostředků.

Security Center pravidelně analyzovat stav zabezpečení vašich prostředků Azure, abyste identifikovali potenciální ohrožení zabezpečení. Pak vám pak nabídne doporučení, jak je odebrat.

Každé doporučení vám poskytne:

- Stručný popis toho, co se doporučuje.
- Kroky k nápravě, které je nutné provést pro implementaci doporučení. <!-- In some cases, Quick Fix remediation is available. -->
- Které prostředky potřebují k provedení doporučené akce.
- **Dopad zabezpečeného skóre**, což je množství, které se bude používat při implementaci tohoto doporučení v zabezpečeném skóre.

## <a name="monitor-recommendations"></a>Doporučení pro monitorování<a name="monitor-recommendations"></a>

Security Center analyzuje stav zabezpečení vašich prostředků, aby identifikoval potenciální ohrožení zabezpečení. Dlaždice **doporučení** v části **Přehled** zobrazuje celkový počet doporučení identifikovaných nástrojem Security Center.

![Přehled služby Security Center](./media/security-center-recommendations/asc-overview.png)

1. V části **Přehled**vyberte **dlaždici doporučení** . Otevře se seznam **doporučení** .

      ![Zobrazení doporučení](./media/security-center-recommendations/view-recommendations.png)

    Doporučení můžete filtrovat. Pokud chcete vyfiltrovat doporučení, vyberte v okně **doporučení** možnost **Filtr** . Otevře se okno **filtru** a vyberete závažnost a hodnoty stavu, které chcete zobrazit.

   * **Doporučení**: doporučení.
   * **Bezpečný dopad na skóre**: skóre vygenerované Security centerm pomocí doporučení zabezpečení a použití pokročilých algoritmů k určení, jak rozhodující je každé doporučení. Další informace najdete v tématu [zabezpečení výpočtu skóre](secure-score-security-controls.md#how-your-secure-score-is-calculated).
   * **Prostředek**: zobrazí seznam prostředků, na které se vztahuje toto doporučení.
   * **Stavové řádky**: popisuje závažnost tohoto konkrétního doporučení:
       * **Vysoká (červená)**: ohrožení zabezpečení existuje smysluplným prostředkem (jako je aplikace, virtuální počítač nebo skupina zabezpečení sítě) a vyžaduje pozornost.
       * **Střední (oranžová)**: existuje ohrožení zabezpečení a nekritické nebo další kroky jsou vyžadovány k jeho odstranění nebo k dokončení procesu.
       * **Nízká (modrá)**: existuje ohrožení zabezpečení, které by mělo být adresováno, ale nevyžaduje okamžitou pozornost. (Ve výchozím nastavení se neprezentují nízká doporučení, ale pokud je chcete zobrazit, můžete je vyfiltrovat podle nízkých doporučení.) 
       * **V pořádku (zelená)**:
       * **Není k dispozici (šedá)**:

1. Pokud chcete zobrazit podrobnosti o jednotlivých doporučeních, klikněte na doporučení.

    ![Podrobnosti doporučení](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Viz [modely nasazení Classic a správce prostředků](../azure-classic-rm.md) pro prostředky Azure.
 
## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se zavedli k doporučením zabezpečení v Security Center. Další informace o řešení těchto doporučení:

* [Opravit doporučení](security-center-remediate-recommendations.md) – Naučte se konfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
