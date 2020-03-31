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
ms.date: 03/16/2020
ms.author: memildin
ms.openlocfilehash: 152168bbadd5815659bc5f70c91bd2a28f5e049d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481955"
---
# <a name="monitor-identity-and-access"></a>Monitorování identity a přístupu

> [!TIP]
> Od března 2020 jsou identity azure security center a doporučení pro přístup zahrnuty ve všech předplatných na bezplatné cenové úrovni. Pokud máte předplatná na bezplatné úrovni, jejich skóre zabezpečení bude ovlivněno, protože nebyly dříve posouzeny z hlediska jejich identity a zabezpečení přístupu. 

Když Security Center identifikuje potenciální slabá místa zabezpečení, vytvoří doporučení, která vás provedou procesem konfigurace potřebných ovládacích prvků pro posílení a ochranu vašich prostředků.

Bezpečnostní perimetr se vyvinul z obvodu sítě na obvod identity. Zabezpečení se stává méně o obraně vaší sítě a více o obraně dat, stejně jako o správě zabezpečení vašich aplikací a uživatelů. V současné době se ale čím dál víc dat a aplikací přesouvá do cloudu a novým perimetrem se proto stává identita.

Sledováním aktivit identity můžete před incidentem provést proaktivní akce nebo reaktivní akce k zastavení pokusu o útok. Centrum zabezpečení může například označit zastaralé účty (účty, které už nejsou potřeba, a zablokovat přihlášení službou Azure Active Directory) pro odebrání. 

Příklady doporučení, která se mohou zobrazit v části Zabezpečení **prostředků identity a přístupu** v Centru zabezpečení Azure, zahrnují:

- Vícefaktorové informace by měly být povoleny u účtů s oprávněními vlastníka k vašemu předplatnému.
- Pro vaše předplatné by měli být určeni maximálně 3 vlastníci.
- Z předplatného by měly být odebrány externí účty s oprávněním ke čtení.
- Zastaralé účty by měly být odebrány z vašeho předplatného

Další informace o těchto doporučeních a úplný seznam doporučení, která se mohou zobrazit zde, naleznete v [tématu Doporučení pro identitu a přístup](recommendations-reference.md#recs-identity).

> [!NOTE]
> Pokud vaše předplatné obsahuje více než 600 účtů, Security Center nemůže spustit doporučení identity proti předplatnému. Doporučení, která nejsou spuštěna, jsou uvedena v části "nedostupná hodnocení" níže.
Security Center nemůže spustit doporučení identity proti agentům správce partnera cloudového řešení (CSP).
>


Všechna doporučení pro identitu a přístup jsou k dispozici v rámci dvou ovládacích prvků zabezpečení na stránce **Doporučení:**

- Správa přístupu a oprávnění 
- Povolení MFA

![Dvě bezpečnostní kontroly s doporučeními týkajícími se identity a přístupu](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Povolit vícefaktorové ověřování (MFA)

Povolení vícefaktorové informace vyžaduje [oprávnění klienta služby Azure Active Directory (AD).](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- Pokud máte prémiovou edici služby AD, povolte vícefaktorové povolení pomocí [podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).

- Uživatelé edice AD free edition můžou ve službě Azure Active Directory povolit **výchozí nastavení zabezpečení,** jak je popsáno v [dokumentaci služby AD,](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) ale doporučení Centra zabezpečení pro povolení vícefaktorového přístupu se stále zobrazí.


## <a name="next-steps"></a>Další kroky
Další informace o doporučeních, která platí pro jiné typy prostředků Azure, najdete v následujících článcích:

- [Ochrana počítačů a aplikací ve službě Azure Security Center](security-center-virtual-machine-protection.md)
- [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)
- [Ochrana služby Azure SQL a dat v Azure Security Center](security-center-sql-service-recommendations.md)