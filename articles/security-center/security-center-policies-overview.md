---
title: Nastavení zásad zabezpečení Azure Security Center | Dokumentace Microsoftu
description: Konfigurovat nastavení zásad zabezpečení Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: ab8a289ea0de263871b76788514052c09a6bf4da
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295733"
---
# <a name="security-policy-settings"></a>Nastavení zásad zabezpečení
Tento článek obsahuje přehled funkcí zabezpečení nastavení zásad ve službě Security Center.

## <a name="what-are-security-policies"></a>Co jsou zásady zabezpečení?
Zásady zabezpečení definují požadovanou konfiguraci úloh a pomáhají zajišťovat dodržování předpisů společnosti nebo soulad se zákonnými požadavky na zabezpečení. Ve službě Azure Security Center můžete definovat zásady pro vaše předplatná Azure a je typu úlohy nebo citlivosti dat. přizpůsobit. Aplikace, které používají regulovaná data, jako jsou identifikovatelné osobní údaje, například může vyžadovat vyšší úroveň zabezpečení než jiné úlohy.

Můžete nastavit následující v části zásady zabezpečení:

- **Shromažďování dat**: Určuje zřizování agentů a [shromažďování dat](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) nastavení.
- **Zásady zabezpečení**: Určuje, který určuje Security Center monitoruje a doporučuje. Můžete upravit [zásady zabezpečení](security-center-policies.md) ve službě Security Center. Můžete také použít [Azure Policy](security-center-azure-policy.md) vytvářet nové definice, definovat další zásady a přiřazovat zásady napříč skupinami pro správu. 
- **E-mailová oznámení**: Určuje kontakty zabezpečení a [e-mailová oznámení](security-center-provide-security-contact-details.md) nastavení.
- **Cenová úroveň**: definuje free nebo standard [výběr cenové](security-center-pricing.md). Zvolená úroveň určí, které funkce Security Center budou dostupné pro prostředky v daném rozsahu. Můžete určit úroveň pro předplatná, skupiny prostředků a pracovní prostory.

> [!NOTE]
> Můžete nastavit všechny na jedno předplatné. Pro pracovní prostory můžete nastavit pouze pro sběr dat a cenová úroveň. Pro skupiny prostředků můžete nastavit jenom cenová úroveň.
>


## <a name="who-can-edit-security-policies"></a>Kdo může upravit zásady zabezpečení?
Security Center používá Role-Based řízení přístupu (RBAC), který poskytuje předdefinované role, které je možné přiřadit uživatelům, skupinám a službám v Azure. Když uživatelé otevřou Security Center, uvidí jenom informace týkající se prostředky, ke kterým mají přístup. To znamená, že uživatelé jsou přiřazeni roli *vlastníka*, *Přispěvatel*, nebo *čtečky* k předplatné nebo skupinu prostředků, které prostředek patří. Kromě těchto rolí existují ve službě Security Center dvě specifické role:

- **Čtenář zabezpečení**: mají zobrazit práva ke službě Security Center, což zahrnuje doporučení, výstrahy, zásady a stav, ale že nemůže provádět změny.
- **Správce zabezpečení**: stejné oprávnění zobrazit jako *Čtenář zabezpečení*, a můžete také aktualizovat zásady zabezpečení a rušit doporučení a výstrahy.


## <a name="next-steps"></a>Další postup
V tomto článku jste se dozvěděli o zásad zabezpečení ve službě Azure Security Center. Další informace o službě Azure Security Center, najdete v následujících článcích:

* [Nastavení zásad zabezpečení ve službě Azure Security Center](security-center-policies.md): Zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md): Zjistěte, jak doporučení služby Security Center vám pomoct chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak sledovat stav vašich partnerských řešení.
- [Zabezpečení dat ve službě Azure Security Center](security-center-data-security.md): Zjistěte, jak se spravuje Security Center a chrání data.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Získejte odpovědi na nejčastější dotazy týkající se použití této služby.
* [Blog o zabezpečení Azure](http://blogs.msdn.com/b/azuresecurity/): Získejte nejnovější zprávy zabezpečení Azure a informace.
