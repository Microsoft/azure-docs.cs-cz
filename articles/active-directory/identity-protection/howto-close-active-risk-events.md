---
title: Jak zavřít aktivní zjišťování rizik v Azure Active Directory Identity Protection | Microsoft Docs
description: Seznamte se s možnostmi zavření aktivních detekcí rizik.
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
ms.openlocfilehash: d24ec94d0381fc2e79fdef97b6d525b7cec33fef
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126480"
---
# <a name="how-to-close-active-risk-detections"></a>Jak: Zavřít aktivní zjišťování rizik

V případě [detekce rizik](../reports-monitoring/concept-risk-events.md)Azure Active Directory detekuje indikátory pro potenciálně ohrožené uživatelské účty. Jako správce chcete získat uzavřená všechna zjištění rizik, aby postižení uživatelé už nehrozí.

Tento článek obsahuje přehled dalších možností, které je třeba uzavřít v případě aktivních detekcí rizik.

## <a name="options-to-close-risk-detections"></a>Možnosti uzavření zjištění rizik 

Stav detekce rizik je buď **aktivní** , nebo uzavřený. Všechna aktivní zjišťování rizik přispívají k výpočtu hodnoty s názvem úroveň rizika uživatele. Úroveň rizika uživatele je indikátorem (nízká, střední, vysoká) pro pravděpodobnost, že došlo k ohrožení bezpečnosti účtu. 

Chcete-li zavřít aktivní zjišťování rizik, máte následující možnosti:

- Vyžadovat resetování hesla pomocí zásad rizik uživatelů
- Ruční resetování hesla
- Zavřít všechna zjišťování rizik 
- Ruční uzavření zjištění jednotlivých rizik

## <a name="require-password-reset-with-a-user-risk-policy"></a>Vyžadovat resetování hesla pomocí zásad rizik uživatelů

Když nakonfigurujete [zásady podmíněného přístupu pro uživatele](howto-user-risk-policy.md), můžete vyžadovat změnu hesla, pokud byla zjištěna automaticky zadaná úroveň rizika uživatele. 

![Resetovat heslo](./media/howto-close-active-risk-events/13.png)

Resetování hesla uzavře všechny aktivní události rizik souvisejícího uživatele a vrátí identitu zpátky do bezpečného stavu. Použití zásad rizik uživatelů je upřednostňovanou metodou pro uzavření aktivních detekcí rizik, protože tato metoda je automatizovaná. Mezi postiženým uživatelem a pracovníky helpdesku ani správcem není vyžadována žádná interakce.

Používání zásad rizik uživatelů však není vždy použitelné. To platí například pro:

- Uživatelé, kteří nebyli registrováni pro službu Multi-Factor Authentication (MFA).
- Uživatelé s aktivními detekci rizik, které byly odstraněny
- Šetření, které odhaluje, že legitimní uživatel provedl zjištěnou detekci rizika.

## <a name="manual-password-reset"></a>Ruční resetování hesla

Pokud se vyžaduje resetování hesla pomocí zásad rizik uživatelů, můžete získat všechny detekce rizik pro uživatele uzavřené s ručním resetováním hesla.

![Resetovat heslo](./media/howto-close-active-risk-events/04.png)

V souvisejícím dialogovém okně jsou k dispozici dvě různé metody resetování hesla:

![Resetovat heslo](./media/howto-close-active-risk-events/05.png)

**Vygenerovat dočasné heslo** – generováním dočasného hesla můžete okamžitě převést identitu zpátky do bezpečného stavu. Tato metoda vyžaduje interakci s postiženými uživateli, protože potřebují znát, co je dočasné heslo. Můžete například poslat nové dočasné heslo na alternativní e-mailovou adresu uživatele nebo správce uživatele. Vzhledem k tomu, že je heslo dočasné, uživateli se zobrazí výzva, aby při příštím přihlášení změnil heslo.

**Vyžadovat, aby uživatel resetoval heslo** – vyžaduje, aby uživatelé resetování hesla povolili automatické obnovení bez kontaktování helpdesku nebo správce. Podobně jako v případě zásad rizik uživatelů se tato metoda vztahuje jenom na uživatele, kteří jsou zaregistrovaní pro MFA. Pro uživatele, kteří se ještě nezaregistrovali pro MFA, tato možnost není k dispozici.

## <a name="dismiss-all-risk-detections"></a>Zavřít všechna zjišťování rizik

Pokud resetování hesla není pro vás možnost, můžete taky zrušit všechny detekce rizik. 

![Resetovat heslo](./media/howto-close-active-risk-events/03.png)

Když kliknete na **Zavřít všechny události**, všechny události se zavřou a ovlivněný uživatel už nehrozí. Vzhledem k tomu, že tato metoda nemá vliv na existující heslo, nepřinese související identitu zpátky do bezpečného stavu. Upřednostňovaným případem použití této metody je odstraněný uživatel s aktivními detekci rizik. 

## <a name="close-individual-risk-detections-manually"></a>Ruční uzavření zjištění jednotlivých rizik

Jednotlivá zjišťování rizik můžete uzavřít ručně. Pokud se detekce rizik uzavírá ručně, můžete snížit úroveň rizika uživatele. Detekce rizik je typicky uzavřená ručně v reakci na související šetření. Například při komunikaci s uživatelem se odhalí, že aktivní zjišťování rizik již není vyžadováno. 
 
Při ručním uzavírání detekce rizik můžete zvolit provedení libovolné z následujících akcí, abyste změnili stav detekce rizik:

![Akce](./media/howto-close-active-risk-events/06.png)

- **Vyřešit** – Pokud po prozkoumání detekce rizik jste provedli vhodnou akci nápravy mimo ochranu identity a domníváte se, že by se mělo detekovat riziko za uzavřenou, označte událost jako vyřešenou. Vyřešené události nastaví stav detekce rizik na uzavřeno a rozpoznávání rizik již nebude přispívat k riziku uživatele.
- **Označit jako falešně pozitivní** – v některých případech můžete prozkoumat detekci rizik a zjistit, že byla nesprávně označena jako riziková. Počet takových výskytů můžete snížit označením detekce rizika jako falešně pozitivní. To vám pomůže algoritmům strojového učení zlepšit klasifikaci podobných událostí v budoucnu. Stav falešně pozitivních událostí je uzavřen a již nebude přispívat k riziku uživatele.
- **Ignore** – Pokud jste neučinili žádnou akci nápravy, ale chcete, aby se detekce rizik odebrala ze seznamu aktivních, můžete označit detekci rizik jako ignorováno a stav události bude uzavřen. Ignorované události nepřispívají k riziku uživatele. Tato možnost by se měla používat jenom za neobvyklých okolností.
- **Opětovná aktivace** – je možné znovu aktivovat detekci rizik, které byly ručně uzavřeny (výběrem možnosti vyřešit, falešně pozitivní nebo ignorovat), nastavením stavu události zpět na aktivní. Znovu aktivované detekce rizik přispívají k výpočtu úrovně rizika uživatele. Detekce rizik uzavřená prostřednictvím nápravy (například zabezpečené resetování hesla) se nedá znovu aktivovat.

## <a name="next-steps"></a>Další postup

Přehled Azure AD Identity Protection najdete v tématu [Azure AD Identity Protection Overview](overview.md).
