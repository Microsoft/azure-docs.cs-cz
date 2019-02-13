---
title: Nastavení Azure Security Center | Dokumentace Microsoftu
description: Konfigurace nastavení Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: f61f5ebf4c0658ff846421682d50ea346c699e01
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56107918"
---
# <a name="security-center-settings"></a>Nastavení služby Security Center
Tento článek obsahuje přehled nastavení ve službě Security Center.

V části zásady zabezpečení se dá kontaktovat následující nastavení:

- **Shromažďování dat**: Určuje zřizování agentů a [shromažďování dat](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) nastavení.
- **Zásady zabezpečení**: Určuje, který určuje Security Center monitoruje a doporučuje. Můžete upravit [zásady zabezpečení](tutorial-security-policy.md) ve službě Security Center. Můžete také použít [Azure Policy](tutorial-security-policy.md) vytvářet nové definice, definovat další zásady a přiřazovat zásady napříč skupinami pro správu. 
- **E-mailová oznámení**: Určuje kontakty zabezpečení a [e-mailová oznámení](security-center-provide-security-contact-details.md) nastavení.
- **Cenová úroveň**: Definuje free nebo standard [výběr cenové](security-center-pricing.md). Zvolená úroveň určí, které funkce Security Center budou dostupné pro prostředky v daném rozsahu. Můžete určit úroveň pro předplatná, skupiny prostředků a pracovní prostory.

> [!NOTE]
> Můžete nastavit všechny na jedno předplatné. Pro pracovní prostory můžete nastavit pouze pro sběr dat a cenová úroveň. Pro skupiny prostředků můžete nastavit jenom cenová úroveň.
>


## <a name="who-can-edit-security-policies"></a>Kdo může upravit zásady zabezpečení?
Security Center používá Role-Based řízení přístupu (RBAC), který poskytuje předdefinované role, které je možné přiřadit uživatelům, skupinám a službám v Azure. Když uživatelé otevřou Security Center, uvidí jenom informace týkající se prostředky, ke kterým mají přístup. To znamená, že uživatelé jsou přiřazeni roli *vlastníka*, *Přispěvatel*, nebo *čtečky* k předplatné nebo skupinu prostředků, které prostředek patří. Kromě těchto rolí existují ve službě Security Center dvě specifické role:

- **Čtenář zabezpečení**: Zobrazení oprávnění ke službě Security Center, což zahrnuje doporučení, výstrahy, zásady a stav, ale jejich nemůže provádět změny.
- **Správce zabezpečení**: Stejné oprávnění zobrazit jako *Čtenář zabezpečení*, a můžete také aktualizovat zásady zabezpečení a rušit doporučení a výstrahy.


## <a name="next-steps"></a>Další postup
V tomto článku jste se dozvěděli o zásad zabezpečení ve službě Azure Security Center. Další informace o službě Azure Security Center, najdete v následujících článcích:

* [Nastavení zásad zabezpečení ve službě Azure Security Center](tutorial-security-policy.md): Zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md): Zjistěte, jak doporučení služby Security Center vám pomoct chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md): Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat a zpracovávat výstrahy zabezpečení.
* [Monitorování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak monitorovat stav partnerských řešení.
- [Zabezpečení dat ve službě Azure Security Center](security-center-data-security.md): Zjistěte, jak se spravuje Security Center a chrání data.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md): Získejte odpovědi na nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/): Získejte nejnovější informace zabezpečení Azure a informace.
