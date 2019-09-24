---
title: Nastavení Azure Security Center | Microsoft Docs
description: Nakonfigurujte nastavení Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/03/2018
ms.author: memildin
ms.openlocfilehash: 4a7254d4ac67ee7d1bf203baf5741638dbc8f3dd
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201611"
---
# <a name="security-center-settings"></a>Nastavení služby Security Center
Tento článek poskytuje přehled nastavení v Security Center.

V části zásady zabezpečení je možné dosáhnout následujících nastavení:

- **Shromažďování dat**: Určuje zřizování agenta a nastavení [shromažďování dat](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) .
- **Zásady zabezpečení**: Určuje, které ovládací prvky Security Center monitoruje a doporučuje. [Zásady zabezpečení](tutorial-security-policy.md) můžete upravit v Security Center. Pomocí [Azure Policy](tutorial-security-policy.md) můžete také vytvářet nové definice, definovat další zásady a přiřazovat zásady napříč skupinami pro správu. 
- **E-mailová oznámení**: Určuje kontakty zabezpečení a nastavení [e-mailových oznámení](security-center-provide-security-contact-details.md) .
- **Cenová úroveň**: Definuje [Výběr ceny](security-center-pricing.md)na bezplatné nebo standardní úrovni. Zvolená úroveň určí, které funkce Security Center budou dostupné pro prostředky v daném rozsahu. Můžete zadat úroveň předplatných a pracovních prostorů.

> [!NOTE]
> Všechna tato nastavení můžete nastavit na jedno předplatné. U pracovních prostorů můžete nastavit jenom shromažďování dat a cenovou úroveň.
>


## <a name="who-can-edit-security-policies"></a>Kdo může upravovat zásady zabezpečení?
Security Center používá Access Control na základě rolí (RBAC), která poskytuje předdefinované role, které se dají přiřadit uživatelům, skupinám a službám v Azure. Když uživatelé otevřou Security Center, uvidí jenom informace, které souvisejí s prostředky, ke kterým mají přístup. To znamená, že uživatelé mají přiřazenou roli *vlastník*, *Přispěvatel*nebo *Čtenář* k předplatnému, ke kterému prostředek patří. Kromě těchto rolí existují ve službě Security Center dvě specifické role:

- **Čtecí modul zabezpečení**: Mít oprávnění k zobrazení Security Center, což zahrnuje doporučení, výstrahy, zásady a stav, ale nemůže provádět změny.
- **Správce zabezpečení**: Mít stejná oprávnění k zobrazení jako *Čtenář zabezpečení*a můžou taky aktualizovat zásady zabezpečení a zrušit doporučení a upozornění.


## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli o zásadách zabezpečení v Azure Security Center. Další informace o Azure Security Center najdete v následujících článcích:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md): Přečtěte si, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md): Přečtěte si, jak Security Center doporučení pomůžou chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md): Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat a zpracovávat výstrahy zabezpečení.
* [Monitorování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak monitorovat stav partnerských řešení.
* [Zabezpečení dat Azure Security Center](security-center-data-security.md): Přečtěte si, jak Security Center spravuje a chrání data.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md): Získejte odpovědi na nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/): Získejte nejnovější novinky a informace o zabezpečení Azure.
