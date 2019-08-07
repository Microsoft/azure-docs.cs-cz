---
title: Doporučení zabezpečení v Azure Security Center | Microsoft Docs
description: Tento dokument vás seznámí s tím, jak vám doporučení Azure Security Center pomůžou chránit prostředky Azure a zůstat v souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: v-mohabe
ms.openlocfilehash: 229b8949facae34a809c0789154a3b56264ee2c5
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779006"
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
- Kroky k nápravě, které je nutné provést pro implementaci doporučení. <!-- In some cases, one-click remediation is available. -->
- Které prostředky potřebují k provedení doporučené akce.
- **Dopad zabezpečeného skóre**, což je množství, které se bude používat při implementaci tohoto doporučení v zabezpečeném skóre.

## Doporučení pro monitorování<a name="monitor-recommendations"></a>

Security Center analyzuje stav zabezpečení vašich prostředků, aby identifikoval potenciální ohrožení zabezpečení. Dlaždice **doporučení** v části **Přehled** zobrazuje celkový počet doporučení identifikovaných nástrojem Security Center.

![Přehled služby Security Center](./media/security-center-recommendations/asc-overview.png)

1. V části **Přehled**vyberte **dlaždici doporučení** . Otevře se seznam **doporučení** .

      ![Zobrazit doporučení](./media/security-center-recommendations/view-recommendations.png)

    Doporučení můžete filtrovat. Pokud chcete vyfiltrovat doporučení, vyberte v okně **doporučení** možnost **Filtr** . Otevře se okno **filtru** a vyberete závažnost a hodnoty stavu, které chcete zobrazit.

   * **DOPORUČENÍ**: Doporučení.
   * **DOPAD NA ZABEZPEČENÍ SKÓRE**: Skóre vygenerované Security Centerm pomocí doporučení zabezpečení a použití pokročilých algoritmů k určení, jak rozhodující je každé doporučení. Další informace najdete v tématu [zabezpečení výpočtu skóre](security-center-secure-score.md#secure-score-calculation).
   * **PROSTŘEDEK**: Zobrazí seznam prostředků, na které se vztahuje toto doporučení.
   * **STAVOVÉ ŘÁDKY**:  Popisuje závažnost tohoto konkrétního doporučení:
       * **Vysoká (červená)** : Existuje chyba s smysluplným prostředkem (jako je aplikace, virtuální počítač nebo skupina zabezpečení sítě) a vyžaduje pozornost.
       * **Střední (oranžová)** : Existuje ohrožení zabezpečení a nekritické nebo další kroky jsou vyžadovány k jeho odstranění nebo k dokončení procesu.
       * **Nízká (modrá)** : Existuje ohrožení zabezpečení, které by mělo být adresováno, ale nevyžaduje okamžitou pozornost. (Ve výchozím nastavení se neprezentují nízká doporučení, ale pokud je chcete zobrazit, můžete je vyfiltrovat podle nízkých doporučení.) 
       * **V pořádku (zelená)** :
       * **Není k dispozici (šedá)** :

1. Pokud chcete zobrazit podrobnosti o jednotlivých doporučeních, klikněte na doporučení.

    ![Podrobnosti doporučení](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Viz [modely nasazení Classic a správce prostředků](../azure-classic-rm.md) pro prostředky Azure.
 
## <a name="next-steps"></a>Další postup

V tomto dokumentu jste se zavedli k doporučením zabezpečení v Security Center. Další informace o řešení těchto doporučení:

* [Opravit doporučení](security-center-remediate-recommendations.md) – Naučte se konfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
