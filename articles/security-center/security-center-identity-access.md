---
title: Monitorování identity a přístupu v Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak používat funkce identity a přístupu v Azure Security Center k monitorování aktivit přístupu uživatelů a problémů souvisejících s identitou.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2020
ms.author: memildin
ms.openlocfilehash: 183b81134b2fe72a539cc6460a05d828342aafbb
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086496"
---
# <a name="monitor-identity-and-access"></a>Monitorování identity a přístupu

> [!TIP]
> Od března 2020 jsou doporučení identit a přístupu Azure Security Center součástí všech předplatných v bezplatné cenové úrovni. Pokud máte předplatné na bezplatné úrovni, bude to mít vliv na jejich bezpečné skóre, protože nebyly dříve vyhodnoceny pro jejich identitu a zabezpečení přístupu. 

Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení, která vás provedou procesem konfigurace potřebných ovládacích prvků k posílení a ochraně vašich prostředků.

Hraniční zabezpečení se vyvinulo z hraniční sítě až po hranice identity. Zabezpečení se zasahuje do ochrany vaší sítě a další informace o tom, jak chrání vaše data, a také o správě zabezpečení vašich aplikací a uživatelů. V současné době se ale čím dál víc dat a aplikací přesouvá do cloudu a novým perimetrem se proto stává identita.

Monitorování aktivit souvisejících s identitou umožňuje proaktivně jednat, ještě než k incidentu dojde, nebo provést reaktivní akci k zastavení pokusu o útok. Příklady doporučení, která se můžou zobrazit v části Azure Security Center **Identita a přístup k** prostředkům zabezpečení, zahrnují:

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- Zastaralé účty by se měly odebírat z předplatného.
- Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.

Úplný seznam doporučených doporučení najdete v tématu [věnovaném doporučením pro identitu a přístup](recommendations-reference.md#recs-identity).

> [!NOTE]
> Pokud má vaše předplatné více než 600 účtů, Security Center není možné spustit doporučení identity pro vaše předplatné. Doporučení, která nejsou spuštěná, jsou uvedená v části "nedostupná posouzení" níže.
Security Center není možné spustit doporučení identity pro agenty správce Cloud Solution Provider (CSP).
>


Všechna doporučení pro identitu a přístup jsou k dispozici ve dvou ovládacích prvcích zabezpečení na stránce **doporučení** :

- Správa přístupu a oprávnění 
- Povolení MFA

![Dva bezpečnostní prvky zabezpečení s doporučeními souvisejícími s identitou a přístupem](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Povolit službu Multi-Factor Authentication (MFA)

Povolení MFA vyžaduje [oprávnění klienta Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). 

- Pokud máte edici Premium ve službě AD, povolte vícefaktorové ověřování pomocí [podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).

- Uživatelé edice Free můžou povolit **výchozí hodnoty zabezpečení** v Azure Active Directory, jak je popsáno v [dokumentaci AD](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) , ale doporučení pro Security Center povolení vícefaktorového ověřování se pořád projeví.


## <a name="next-steps"></a>Další kroky
Další informace o doporučeních, která se vztahují na jiné typy prostředků Azure, najdete v následujících článcích:

- [Ochrana počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
- [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
- [Ochrana dat a služby SQL Azure v Azure Security Center](security-center-sql-service-recommendations.md)