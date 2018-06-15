---
title: Úvod do zásad zabezpečení Azure Security Center | Microsoft Docs
description: Další informace o službě Azure Security Center zásady zabezpečení a klíčových funkcí.
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: f552164edc22b450c4ace57cce5c051d1c93e780
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776121"
---
# <a name="security-policies-overview"></a>Přehled zásad zabezpečení
Tento článek obsahuje přehled zásad zabezpečení ve službě Security Center.

## <a name="what-are-security-policies"></a>Co jsou zásady zabezpečení?
Zásady zabezpečení definují požadovanou konfiguraci úloh a pomáhají zajišťovat dodržování předpisů společnosti nebo soulad se zákonnými požadavky na zabezpečení. V Azure Security Center můžete definovat zásady pro předplatné Azure a přizpůsobit pro váš typ úlohy, nebo citlivosti vašich data. Aplikace, které používají regulovaná data, jako třeba identifikovatelné osobní údaje, například může vyžadovat vyšší úroveň zabezpečení než jiné úlohy. 

Zásady Security Center obsahují následující součásti:

- **Shromažďování dat**: Určuje agenta zřizování a [shromažďování dat](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) nastavení.
- **Zásady zabezpečení**: Určuje, které řídí monitorování Security Center a doporučuje. Můžete upravit [zásady zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-policies) ve službě Security Center. Můžete také použít [zásad Azure](security-center-azure-policy.md) (ve verzi preview omezené) k vytvoření nových definic, definovat další zásady a přiřazení zásad skupinami pro správu.
- **E-mailová oznámení**: Určuje zabezpečení kontakty a [e-mailová oznámení](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details) nastavení.
- **Cenová úroveň**: Definuje volné nebo standardní [ceny výběr](https://docs.microsoft.com/azure/security-center/security-center-pricing). Zvolená úroveň určí, které funkce Security Center budou dostupné pro prostředky v daném rozsahu. Můžete zadat úroveň pro pracovní prostory, skupiny prostředků a předplatná. 


## <a name="who-can-edit-security-policies"></a>Kdo můžete upravit zásady zabezpečení?
Security Center používá na základě rolí řízení přístupu (RBAC), která poskytuje integrované role, které lze přiřadit uživatelům, skupinám a službám v Azure. Když uživatelé otevřou Security Center, uvidí jenom informace, které souvisí s prostředky, ke kterým mají přístup. To znamená, že uživatelé budou přiřazení role *vlastníka*, *Přispěvatel*, nebo *čtečky* ke skupině předplatné nebo prostředek, který daný prostředek patří. Kromě těchto rolí existují ve službě Security Center dvě specifické role:

- **Zabezpečení čtečky**: mít zobrazení přístupových práv k Security Center, která obsahuje doporučení, výstrahy, zásady a stav, ale jejich nelze provádět změny.
- **Správce zabezpečení**: mají stejná práva zobrazení jako *zabezpečení čtečky*, a můžete taky aktualizovat zásady zabezpečení a zavření doporučení a výstrahy.


## <a name="next-steps"></a>Další postup
V tomto článku jste se dozvěděli o zásady zabezpečení v Azure Security Center. Další informace o službě Azure Security Center, najdete v následujících článcích:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md): Zjistěte, jak nakonfigurovat zásady zabezpečení pro skupiny prostředků a předplatná Azure.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md): Zjistěte, jak doporučení služby Security Center pomáhá chránit prostředky v Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak sledovat stav vašich partnerských řešení.
- [Zabezpečení dat v Azure Security Center](security-center-data-security.md): Zjistěte, jak Security Center spravuje a chrání data.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Získejte odpovědi na nejčastější dotazy týkající se použití této služby.
* [Blog o bezpečnosti Azure](http://blogs.msdn.com/b/azuresecurity/): Získejte nejnovější informace zabezpečení Azure a informace.


