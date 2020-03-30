---
title: Doporučení zabezpečení v Azure Security Center
description: Tento dokument vás provede tím, jak vám doporučení v Azure Security Center pomáhají chránit prostředky Azure a dodržovat zásady zabezpečení.
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
ms.openlocfilehash: 408b0f020be72b8e6b10dd6c97298afda1b91360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245301"
---
# <a name="security-recommendations-in-azure-security-center"></a>Doporučení zabezpečení v Azure Security Center 
Toto téma vysvětluje, jak zobrazit a pochopit doporučení v Centru zabezpečení Azure, které vám pomůžou chránit prostředky Azure.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Tento dokument není podrobným průvodcem.
>

## <a name="what-are-security-recommendations"></a>Jaká jsou bezpečnostní doporučení?

Doporučení jsou akce, které je třeba provést za účelem zabezpečení vašich zdrojů.

Security Center pravidelně analyzuje stav zabezpečení prostředků Azure k identifikaci potenciálních chyb zabezpečení. Poté poskytuje doporučení, jak je odstranit.

Každé doporučení vám poskytuje:

- Stručný popis toho, co se doporučuje.
- Nápravné kroky, které je třeba provést za účelem provedení doporučení. <!-- In some cases, Quick Fix remediation is available. -->
- Které zdroje jsou potřeba provést doporučenou akci na ně.
- **Dopad na zabezpečené skóre**, což je částka, kterou vaše skóre zabezpečení zvýší, pokud implementujete toto doporučení.

## <a name="monitor-recommendations"></a>Sledovat doporučení<a name="monitor-recommendations"></a>

Security Center analyzuje stav zabezpečení vašich prostředků k identifikaci potenciálních chyb zabezpečení. Dlaždice **Doporučení** v části **Přehled** zobrazuje celkový počet doporučení identifikovaných Centrem zabezpečení.

![Centrum zabezpečení – přehled](./media/security-center-recommendations/asc-overview.png)

1. Včásti **Přehled**vyberte **dlaždici Doporučení** . Otevře se seznam **Doporučení.**

      ![Zobrazení doporučení](./media/security-center-recommendations/view-recommendations.png)

    Můžete filtrovat doporučení. Chcete-li filtrovat doporučení, vyberte **filtrovat** v okně **Doporučení.** Otevře se okno **Filtr** a vyberete hodnoty závažnosti a stavu, které chcete zobrazit.

   * **DOPORUČENÍ**: Doporučení.
   * **SECURE SCORE IMPACT**: Skóre generované Security Center pomocí doporučení zabezpečení a použití pokročilých algoritmů k určení, jak zásadní je každé doporučení. Další informace naleznete v tématu [Výpočet zabezpečeného skóre](security-center-secure-score.md#secure-score-calculation).
   * **ZDROJ**: Uvádí zdroje, na které se toto doporučení vztahuje.
   * **STAVOVÉ LIŠTY**: Popisuje závažnost tohoto konkrétního doporučení:
       * **Vysoká (červená)**: Existuje chyba zabezpečení se smysluplným prostředkem (například aplikací, virtuálním počítačem nebo skupinou zabezpečení sítě) a vyžaduje pozornost.
       * **Střední (oranžová)**: Existuje chyba zabezpečení a k jejímu odstranění nebo dokončení procesu jsou nutné nekritické nebo další kroky.
       * **Nízká (modrá)**: Existuje chyba zabezpečení, která by měla být vyřešena, ale nevyžaduje okamžitou pozornost. (Ve výchozím nastavení nejsou uvedena nízká doporučení, ale pokud je chcete zobrazit, můžete filtrovat podle nízkých doporučení.) 
       * **Zdravé (zelená)**:
       * **Není k dispozici (šedá):**

1. Chcete-li zobrazit podrobnosti o jednotlivých doporučeních, klikněte na doporučení.

    ![Podrobnosti doporučení](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Podívejte se na [klasické modely nasazení a modely nasazení Správce prostředků](../azure-classic-rm.md) pro prostředky Azure.
 
## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste byli představeni s doporučeními zabezpečení v Centru zabezpečení. Chcete-li se dozvědět, jak navádět doporučení:

* [Náprava doporučení](security-center-remediate-recommendations.md) – Zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
