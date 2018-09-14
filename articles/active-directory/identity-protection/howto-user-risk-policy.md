---
title: Jak nakonfigurovat zásady rizik uživatelů v Azure Active Directory Identity Protection | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zásady rizik uživatelů Azure AD Identity Protection.
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: bcfab9ab95e41b723cb8a8e49d7390a2894d5219
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45581367"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Postupy: Konfigurace zásady rizik uživatelů

Všechny aktivní [rizikových událostí](../reports-monitoring/concept-risk-events.md) , které byly zjištěny službou Azure Active Directory pro uživatele přispívat na logický pojem, který volá uživatelského rizika. Uživatele označené příznakem rizika je indikátorem uživatelského účtu, který mohl být ohrožený.

![Uživatelé označení příznakem rizika](./media/howto-user-risk-policy/1200.png)


## <a name="user-risk-level"></a>Úroveň rizika uživatele

Úroveň rizika uživatele je indikací (vysoká, střední nebo nízká) pravděpodobnost, že se k ohrožení identity uživatele. To se počítají na základě rizikových událostí uživatele, které jsou spojené s identitou uživatele.

Stav rizikové události je buď **aktivní** nebo **uzavřeno**. Pouze rizikových událostí, které jsou **aktivní** přispívat k výpočtu úroveň rizika uživatele.

Úroveň rizika pro uživatele se počítá pomocí následující vstupy:

* Vliv na uživatele Active rizikových událostí
* Úroveň rizika těchto událostí
* Určuje, zda byla přijata jaké nápravné akce

![Riziko uživatele](./media/howto-user-risk-policy/1031.png "rizika uživatele")

Můžete použít úrovně rizika uživatele k vytvoření zásady podmíněného přístupu, které zablokovat uživatelům rizikových přihlášení, nebo vynuťte jejich bezpečně změnit své heslo.

## <a name="closing-risk-events-manually"></a>Ručnímu zavření rizikových událostí

Ve většině případů bude trvat nápravné akce, jako je zabezpečené heslo resetovat automaticky zavření rizikové události. Nicméně to nemusí být vždy možné.  
To platí, například když:

* Uživatel s aktivním rizikové události se odstranil.
* Šetření odhalí, že ohlášená riziková událost provést legitimní uživatel

Protože rizikové události, které jsou **aktivní** přispívat k výpočtu rizika uživatele, bude pravděpodobně nutné ručně ručnímu zavření rizikových událostí snížit úroveň rizika.  
V průběhu šetření můžete provést některou z těchto akcí, chcete-li změnit stav rizikové události:

![Akce](./media/howto-user-risk-policy/34.png "akce")

* **Vyřešit** – Pokud po prozkoumání rizikovou událost, trvalo odpovídajících nápravných akci mimo Identity Protection. proto si myslíte, že by měl za riziková událost zavřená, označte události jako vyřešenou. Vyřešené události se riziková událost stav nastaven na Uzavřeno a rizikové události se už přispívat do uživatelského rizika.
* **Označit jako falešně pozitivní** – v některých případech můžete vyšetřovat rizikovou událost a zjistit, že byla nesprávně označena jako rizikové. Vám může pomoct snížit počet výskytů těchto označením riziková událost jako falešně pozitivní. To vám pomůže zlepšit klasifikace podobné události v budoucnu algoritmech strojového učení. Stav falešně pozitivní událostí je **uzavřeno** a už se přispívat do uživatelského rizika.
* **Ignorovat** – Pokud ještě nevstoupilo v libovolné akci nápravy, ale chcete riziková událost odebrat ze seznamu aktivní, můžete označit rizikové události ignorovat a bude uzavřeno stav události. Ignoruje události nepočítají do uživatelského rizika. Tato možnost by měla sloužit pouze v rámci neobvyklé okolnosti.
* **Znovu aktivovat** -rizikových událostí, které nebyly uzavřeny ručně (výběrem **vyřešit**, **falešně pozitivní**, nebo **Ignorovat**) můžete znovu aktivovat, nastavení události stavu zpět do **aktivní**. Opětovně rizikových událostí přispívat k výpočtu úroveň rizika uživatele. Rizikové události jsou zavřené prostřednictvím nápravy (například zabezpečené heslo resetovat) nelze aktivovat.

**Chcete-li otevřít dialogové okno Konfigurace související**:

1. Na **Azure AD Identity Protection** okně v části **prošetření**, klikněte na tlačítko **rizikových událostí**.

    ![Resetování hesla ruční](./media/howto-user-risk-policy/1002.png "ruční heslo resetovat")
2. V **rizikových událostí** klikněte na možnost riziko.

    ![Resetování hesla ruční](./media/howto-user-risk-policy/1003.png "ruční heslo resetovat")
3. V okně rizika klikněte pravým tlačítkem na uživatele.

    ![Resetování hesla ruční](./media/howto-user-risk-policy/1004.png "ruční heslo resetovat")

## <a name="closing-all-risk-events-for-a-user-manually"></a>Ruční zavření všechny rizikové události pro uživatele
Místo ruční zavření rizikové události pro uživatele jednotlivě, Azure Active Directory Identity Protection také poskytuje metodu pro zavřít všechny události pro uživatele s jedním kliknutím.

![Akce](./media/howto-user-risk-policy/2222.png "akce")

Po kliknutí na **zavřít všechny události**, jsou uzavřeny všechny události a ovlivněného uživatele už nejsou ohrožení.

## <a name="remediating-user-risk-events"></a>Napravování rizikové události uživatele

Nápravy je akce zabezpečení identity nebo zařízení, který byl dříve podezřelý nebo známé u něho ohrožena bezpečnost. Nápravné akce obnoví identity nebo zařízení do bezpečného stavu a odstraňuje předchozí rizikové události související s identity nebo zařízení.

Chcete-li odstranit uživatele rizikové události, můžete:

* Zabezpečené heslo obnovit ručně odstranit rizikové události uživatele
* Konfigurovat zásady zabezpečení rizik uživatelů k zmírnit dopady nebo automaticky odstranit rizikové události uživatele
* Přeinstalace (RE-Image) nakažených zařízení  

### <a name="manual-secure-password-reset"></a>Ruční zabezpečené heslo resetovat
Zabezpečené heslo resetovat je efektivní opravy mnoha rizikových událostech a při provádění, automaticky zavře tyto rizikové události a přepočítá úroveň rizika pro uživatele. K zahájení obnovení hesla pro uživatele rizikový uživatel mohou pomocí řídicího panelu služby Identity Protection.

Dialogové okno související poskytuje dva různé způsoby vytvoření nového hesla:

**Resetovat heslo** – vyberte **vyžaduje, aby uživatel k resetování hesla** povolit uživatelské samoobslužné obnovení, pokud má uživatel zaregistrován u služby Multi-Factor authentication. Během uživatele příštím přihlášení bude uživatel vyžaduje řešení výzvu ověřování službou Multi-Factor Authentication úspěšně a pak, budete muset změnit heslo. Tato možnost není dostupná, pokud uživatelský účet ještě není registrované služby Multi-Factor authentication.

**Dočasné heslo** – vyberte **vygenerovat dočasné heslo** okamžitě zneplatnit existující heslo a vytvoří nové dočasné heslo pro daného uživatele. Odešlete nové dočasné heslo na alternativní e-mailovou adresu uživatele nebo jeho manažerovi. Protože je dočasné heslo, uživateli zobrazí výzva ke změně hesla při přihlášení.

![Zásady](./media/howto-user-risk-policy/1005.png "zásad")

**Chcete-li otevřít dialogové okno Konfigurace související**:

1. Na **Azure AD Identity Protection** okna, klikněte na tlačítko **uživatelé označení příznakem rizika**.

    ![Resetování hesla ruční](./media/howto-user-risk-policy/1006.png "ruční heslo resetovat")
2. Ze seznamu uživatelů vyberte uživatele s alespoň jeden rizikové události.

    ![Resetování hesla ruční](./media/howto-user-risk-policy/1007.png "ruční heslo resetovat")
3. V okně uživatele klikněte na tlačítko **resetovat heslo**.

    ![Resetování hesla ruční](./media/howto-user-risk-policy/1008.png "ruční heslo resetovat")

## <a name="user-risk-security-policy"></a>Zásady zabezpečení rizik uživatelů
Zásady zabezpečení rizik uživatelů se zásady podmíněného přístupu, který vyhodnotí úroveň rizika pro konkrétního uživatele a provede akce nápravy a zmírnění distribuovaných útoků na základě předem definované podmínky a pravidla.

![Zásady rizik uživatelů](./media/howto-user-risk-policy/1009.png "zásady rizik uživatelů")

Azure AD Identity Protection vám pomůže se správou zmírnění a odstraňování problémů uživatelů označených příznakem rizika umožňuje:

* Nastavte uživatele a skupiny, které zásady platí pro:

    ![Zásady rizik uživatelů](./media/howto-user-risk-policy/1010.png "zásady rizik uživatelů")
* Nastavte uživatele riziko úrovně prahovou hodnotu (nízká, střední nebo vysoké), která aktivuje zásady:

    ![Zásady rizik uživatelů](./media/howto-user-risk-policy/1011.png "zásady rizik uživatelů")
* Můžete nastavte řízení vynucení při aktivaci zásady:

    ![Zásady rizik uživatelů](./media/howto-user-risk-policy/1012.png "zásady rizik uživatelů")
* Přepnutí stavu zásad:

    ![Zásady rizik uživatelů](./media/howto-user-risk-policy/403.png "registrace MFA")
* Kontrola a vyhodnocení dopadu změn před aktivací ho:

    ![Zásady rizik uživatelů](./media/howto-user-risk-policy/1013.png "zásady rizik uživatelů")

Výběr **vysokou** prahová hodnota snižuje počet, kolikrát zásada se aktivuje a minimalizuje dopad na uživatele.
Ale nezahrnuje **nízká** a **střední** uživatelů označených příznakem rizika ze zásad, které nemusí zabezpečit identity nebo zařízení, které byly dříve vzbuzovat podezření na nebo známé u něho ohrožena bezpečnost.

Při nastavování zásad,

* Vyloučit uživatele, kteří mohou generovat velké množství pozitivní hodnotu false (vývojáři, analytikům zabezpečení)
* Vyloučit uživatele v národních prostředí, kde není praktické povolení zásad (například žádný přístup k technické podpory)
* Použití **vysokou** prahové hodnoty během počáteční zásad uvádění, nebo pokud musíte minimalizovat problémy, koncový uživatel neuvidí.
* Použití **nízká** prahovou hodnotu, pokud vaše organizace vyžaduje vyšší úroveň zabezpečení. Výběr **nízká** prahová hodnota představuje další uživatele přihlásit výzvy, ale zvýšené zabezpečení.

Doporučené výchozí nastavení pro většinu organizací je a nakonfigurujte pravidlo pro **střední** prahová hodnota hledají rovnováhu mezi použitelnost a zabezpečení.

Přehled související uživatelské prostředí naleznete v tématu:

* [Dojde k ohrožení bezpečnosti účtu obnovení toku](flows.md#compromised-account-recovery).  
* [Dojde k ohrožení bezpečnosti účtu blokované tok](flows.md#compromised-account-blocked).  

**Chcete-li otevřít dialogové okno Konfigurace související**:

- Na **Azure AD Identity Protection** okno v **konfigurovat** klikněte na tlačítko **zásady rizik uživatelů**.

    ![Zásady rizik uživatelů](./media/howto-user-risk-policy/1009.png "zásady rizik uživatelů")

## <a name="mitigating-user-risk-events"></a>Zmírnění rizikových událostí uživatele
Správci můžou nastavit zásady zabezpečení rizik uživatelů k blokování uživatelů při přihlášení v závislosti na úrovni rizika.

Blokování přihlašování se změnami:

* Brání generování nového uživatele rizikové události pro ovlivněného uživatele
* Správcům umožňuje ručně odstranit rizikové události by to ovlivnilo identitu uživatele a jeho obnovení stavu zabezpečení


## <a name="next-steps"></a>Další postup

Získejte přehled o Azure AD Identity Protection, najdete v článku [Přehled služby Azure AD Identity Protection](overview.md).
