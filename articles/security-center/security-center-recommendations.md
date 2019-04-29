---
title: Správa doporučení zabezpečení v Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vás provede jak doporučení ve službě Azure Security Center pomoci chránit prostředky Azure a zůstat souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: e3b4da1c1d835e9d630c000055af058aa7b45968
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905914"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Správa doporučení zabezpečení v Azure Security Center
Tento dokument vás provede postupy použijte doporučení ve službě Azure Security Center k ochraně vašich prostředků Azure.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Tento dokument není podrobný průvodce.
>
>

## <a name="what-are-security-recommendations"></a>Co jsou doporučení týkající se zabezpečení?
Security Center pravidelně analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení. Doporučení vás provedou procesem konfigurace potřebných kontrol.

## <a name="implementing-security-recommendations"></a>Implementace doporučení zabezpečení
### <a name="set-recommendations"></a>Sada doporučení
V [nastavení zásad zabezpečení ve službě Azure Security Center](tutorial-security-policy.md), zjistíte, jak:

* Konfigurovat zásady zabezpečení.
* Shromažďování dat zapněte.
* Zvolte, které doporučení zobrazíte jako součást zásad zabezpečení.

Aktuální zásady doporučení center po aktualizacích systému, pravidla standardních hodnot, antimalwarové programy [skupiny zabezpečení sítě](../virtual-network/security-overview.md) podsítí a síťových rozhraní, auditování služby SQL database, SQL database transparentní šifrování dat, a brány firewall webových aplikací.  [Nastavení zásad zabezpečení](tutorial-security-policy.md) obsahuje popis jednotlivých možností doporučení.

### <a name="monitor-recommendations"></a>Doporučení pro monitorování
Po nastavení zásad zabezpečení bude Security Center analyzovat stav zabezpečení vašich prostředků Azure za účelem identifikace potenciálních ohrožení zabezpečení. **Doporučení** dlaždici v části **přehled** vás informuje, celkový počet doporučení identifikované pomocí služby Security Center.

![Doporučení pro dlaždici][1]

Pokud chcete zobrazit podrobnosti o jednotlivých doporučení, vyberte **doporučení dlaždici** pod **přehled**. **Doporučení** otevře.

![Filtrovat doporučení][2]

Můžete filtrovat doporučení. Chcete-li filtrovat doporučení, vyberte **filtr** na **doporučení** okno. **Filtr** se otevře okno a vybrat závažnost a stavu hodnoty, které chcete zobrazit.


* **DOPORUČENÍ**: Doporučení.
* **ZABEZPEČENÍ SKÓRE DOPAD**:
* **PROSTŘEDEK**: Seznam prostředků, u kterých bude použito toto doporučení.
* **STAVOVÉ ŘÁDKY**:  Popisuje závažnost tohoto konkrétního doporučení:
   * **Vysoká (červená)**: Ohrožení zabezpečení existuje u významného prostředku (například aplikace, virtuálního počítače nebo skupiny zabezpečení sítě) a vyžaduje pozornost.
   * **Střední (oranžová)**: Existuje ohrožení zabezpečení a nekritické nebo další kroky jsou požadovány k jeho odstranění nebo k dokončení procesu.
   * **Nízká (modrá)**: Existuje ohrožení zabezpečení, které mělo by se řešit, ale nevyžaduje okamžitou pozornost. (Ve výchozím nastavení, s nízkou doporučení nejsou k dispozici, ale můžete filtrovat podle doporučení s nízkou, pokud chcete zobrazit.) 
   * **V pořádku (zelený)**:
   * **Není k dispozici (šedá)**:
 


> [!NOTE]
> Chcete se dozvědět [klasické modely nasazení a modely nasazení Resource Manager](../azure-classic-rm.md) pro prostředky Azure.
> 
> 
> ### <a name="apply-recommendations"></a>Použití doporučení
> Po kontrole všech doporučení, rozhodněte, které ten, který jste měli nejdřív použít. Doporučujeme používat hodnocení závažnosti, protože parametr hlavní k vyhodnocení, které doporučení bude použito první.



## <a name="next-steps"></a>Další postup
V tomto dokumentu jste se seznámili doporučení zabezpečení v Security Center. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení ve službě Azure Security Center](tutorial-security-policy.md) – zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md) – zjistěte, jak spravovat a reagovat na výstrahy zabezpečení.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
