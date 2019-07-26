---
title: Jak zavřít aktivní rizikové události v Azure Active Directory Identity Protection | Microsoft Docs
description: Seznamte se s možnostmi zavření aktivních rizikových událostí.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5e24c12b72852ee7009533c8dc24d231fe636f2
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333994"
---
# <a name="how-to-close-active-risk-events"></a>Jak: Zavření aktivních rizikových událostí

V případě [rizikových událostí](../reports-monitoring/concept-risk-events.md)Azure Active Directory detekuje indikátory pro potenciálně ohrožené uživatelské účty. Jako správce chcete získat všechny rizikové události uzavřeně, aby postižení uživatelé již nehrozili.

Tento článek obsahuje přehled dalších možností, které je třeba uzavřít v případě aktivních rizikových událostí.

## <a name="options-to-close-risk-events"></a>Možnosti zavření rizikových událostí 

Stav rizikové události je buď **aktivní** , nebo uzavřený . Všechny aktivní rizikové události přispívají k výpočtu hodnoty s názvem úroveň rizika uživatele. Úroveň rizika uživatele je indikátorem (nízká, střední, vysoká) pro pravděpodobnost, že došlo k ohrožení bezpečnosti účtu. 

Chcete-li zavřít aktivní rizikové události, máte následující možnosti:

- Vyžadovat resetování hesla pomocí zásad rizik uživatelů
- Ruční resetování hesla
- Zavřít všechny rizikové události 
- Ruční uzavření jednotlivých rizikových událostí

## <a name="require-password-reset-with-a-user-risk-policy"></a>Vyžadovat resetování hesla pomocí zásad rizik uživatelů

Když nakonfigurujete [zásady podmíněného přístupu pro uživatele](howto-user-risk-policy.md), můžete vyžadovat změnu hesla, pokud byla zjištěna automaticky zadaná úroveň rizika uživatele. 

![Resetovat heslo](./media/howto-close-active-risk-events/13.png)

Resetování hesla uzavře všechny aktivní události rizik souvisejícího uživatele a vrátí identitu zpátky do bezpečného stavu. Použití zásad rizik uživatelů je upřednostňovanou metodou pro zavření aktivních rizikových událostí, protože tato metoda je automatizovaná. Mezi postiženým uživatelem a pracovníky helpdesku ani správcem není vyžadována žádná interakce.

Používání zásad rizik uživatelů však není vždy použitelné. To platí například pro:

- Uživatelé, kteří nebyli registrováni pro službu Multi-Factor Authentication (MFA).
- Uživatelé s aktivními rizikovými událostmi, které byly odstraněny.
- Šetření, které odhalí, že oprávněný uživatel učinil oznámenou rizikovou událost.

## <a name="manual-password-reset"></a>Ruční resetování hesla

Pokud se vyžaduje resetování hesla pomocí zásad rizik uživatelů, můžete získat všechny rizikové události pro uživatele uzavřené s ručním resetováním hesla.

![Resetovat heslo](./media/howto-close-active-risk-events/04.png)

V souvisejícím dialogovém okně jsou k dispozici dvě různé metody resetování hesla:

![Resetovat heslo](./media/howto-close-active-risk-events/05.png)

**Vygenerovat dočasné heslo** – generováním dočasného hesla můžete okamžitě převést identitu zpátky do bezpečného stavu. Tato metoda vyžaduje interakci s postiženými uživateli, protože potřebují znát, co je dočasné heslo. Můžete například poslat nové dočasné heslo na alternativní e-mailovou adresu uživatele nebo správce uživatele. Vzhledem k tomu, že je heslo dočasné, uživateli se zobrazí výzva, aby při příštím přihlášení změnil heslo.

**Vyžadovat, aby uživatel resetoval heslo** – vyžaduje, aby uživatelé resetování hesla povolili automatické obnovení bez kontaktování helpdesku nebo správce. Podobně jako v případě zásad rizik uživatelů se tato metoda vztahuje jenom na uživatele, kteří jsou zaregistrovaní pro MFA. Pro uživatele, kteří se ještě nezaregistrovali pro MFA, tato možnost není k dispozici.

## <a name="dismiss-all-risk-events"></a>Zavřít všechny rizikové události

Pokud resetování hesla není pro vás možnost, můžete také zavřít všechny rizikové události. 

![Resetovat heslo](./media/howto-close-active-risk-events/03.png)

Když kliknete na **Zavřít všechny události**, všechny události se zavřou a ovlivněný uživatel už nehrozí. Vzhledem k tomu, že tato metoda nemá vliv na existující heslo, nepřinese související identitu zpátky do bezpečného stavu. Upřednostňovaným případem použití této metody je odstraněný uživatel s aktivními rizikovými událostmi. 

## <a name="close-individual-risk-events-manually"></a>Ruční uzavření jednotlivých rizikových událostí

Jednotlivé rizikové události můžete uzavřít ručně. Při ručním uzavírání rizikových událostí můžete snížit úroveň rizika uživatele. Rizikové události jsou obvykle uzavřeny ručně v reakci na související šetření. Například při komunikaci s uživatelem se odhalí, že aktivní riziková událost již není požadována. 
 
Při ručním uzavírání rizikových událostí můžete zvolit provedení libovolné z následujících akcí ke změně stavu rizikové události:

![Akce](./media/howto-close-active-risk-events/06.png)

- **Vyřešit** – Pokud po prošetření rizikové události jste provedli vhodnou akci nápravy mimo ochranu identity, a domníváte se, že by se riziková událost měla brát v úvahu jako uzavřená, označte událost jako vyřešenou. Vyřešené události nastaví stav události rizika na uzavřeno a riziková událost již nebude přispívat k riziku uživatele.
- **Označit jako falešně pozitivní** – v některých případech můžete prozkoumat rizikové události a zjistit, že byla nesprávně označena jako riziková. Počet takových výskytů můžete snížit označením rizikové události jako falešně pozitivní. To vám pomůže algoritmům strojového učení zlepšit klasifikaci podobných událostí v budoucnu. Stav falešně pozitivních událostí je uzavřen a již nebude přispívat k riziku uživatele.
- **Ignore** – Pokud jste neučinili žádnou akci nápravy, ale chcete, aby se riziková událost odebrala ze seznamu aktivních, můžete označit riziko ignorování a stav události bude uzavřen. Ignorované události nepřispívají k riziku uživatele. Tato možnost by se měla používat jenom za neobvyklých okolností.
- **Opětovná aktivace** – rizikové události, které se ručně zavřely (zaškrtnutím možnosti vyřešit, falešně pozitivní nebo ignorovat), se dají znovu aktivovat, a to tak, že se stav události nastaví zpátky na aktivní. Znovu aktivované rizikové události přispívají k výpočtu úrovně rizika uživatele. Rizikové události uzavřené prostřednictvím nápravy (například zabezpečené resetování hesla) nelze znovu aktivovat.

## <a name="next-steps"></a>Další postup

Přehled Azure AD Identity Protection najdete v tématu [Azure AD Identity Protection Overview](overview.md).
