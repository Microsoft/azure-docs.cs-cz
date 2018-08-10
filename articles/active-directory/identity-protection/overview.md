---
title: Azure Active Directory Identity Protection | Dokumentace Microsoftu
description: Zjistěte, jak Azure AD Identity Protection umožňuje omezit schopnost útočníka zneužít ohrožení zabezpečení identity nebo zařízení a zabezpečit identitu nebo zařízení, který byl dříve podezřelý nebo známé u něho ohrožena bezpečnost.
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 06e3a596b60bf96319071fff68b0bf1655869559
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003793"
---
# <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

Azure Active Directory Identity Protection je funkce edice Azure AD Premium P2, která vám umožní:

- Zjistit potenciální ohrožení zabezpečení dopadem na identity vaší organizace

- Nakonfigurovat automatické odpovědi na zjištěné podezřelé akce, které se vztahují na identity vaší organizace  

- Prozkoumat podezřelé incidenty a přijmout vhodná opatření k jejich řešení   


## <a name="getting-started"></a>Začínáme

Microsoft má zabezpečené cloudové identity pro více než deset let. S Azure Active Directory Identity Protection ve vašem prostředí, můžete použít samé systémy ochrany, které společnost Microsoft používá k zabezpečení identit.

Většinu narušení zabezpečení se použijí při útočníci získají přístup k prostředí krádeží identity uživatele. V průběhu let staly útočníci stále častěji efektivní využití porušení třetích stran a pomocí sofistikovaných útoků phishing útoky. Jakmile útočník získá přístup k i nízkou privilegovaných uživatelské účty, je pro ně k získání přístupu k prostředkům společnosti důležité prostřednictvím taktiky Lateral Movement je poměrně snadné.

Následkem toho budete muset:

- Chránit všechny identity bez ohledu na jejich úrovně oprávnění

- Proaktivně ohrožení zabezpečení identity zabránit se zneužít

Zjišťování ohrožení zabezpečení identit je žádný snadný úkol. Azure Active Directory používá algoritmy adaptivní strojového učení a heuristik detekovat anomálie a podezřelé incidenty, které indikují potenciální ohrožení identit. Ochrana Identity na základě těchto dat generuje sestavy a výstrahy, které vám umožní zjištěné problémy vyhodnotit a přijmout odpovídající omezení nebo nápravné akce.

Azure Active Directory Identity Protection je větší než monitorování a vytváření sestav nástroje. K ochraně identity ve vaší organizaci, můžete nakonfigurovat zásady, které automaticky reagují na zjištěné problémy, když se dosáhne úroveň zadané rizika. Tyto zásady, kromě jiných ovládacích prvků podmíněný přístup poskytuje Azure Active Directory a EMS, můžete buď automaticky blokovat nebo zahájit adaptivní nápravných akcí, které resetuje heslo včetně a vynucování služby Multi-Factor authentication.


#### <a name="identity-protection-capabilities"></a>Možnosti ochrany identit

**Zjišťování ohrožení zabezpečení a rizikových účty:**  

* Poskytuje vlastní doporučení k vylepšení celkové postavení zabezpečení zvýrazněním ohrožení zabezpečení
* Výpočet úrovně rizika přihlášení
* Výpočet úrovně rizika uživatele


**Zkoumání rizikových událostí:**

* Odesílání oznámení pro rizikové události
* Zkoumání pomocí relevantní a kontextové informace o rizikových událostí
* Poskytuje základní pracovní postupy ke sledování vyšetřování
* Poskytování snadného přístupu pro nápravné akce, jako je například resetování hesla

**Zásady podmíněného přístupu na základě rizik:**

* Zásady pro blokování přihlášení nebo vyžadování výzvy ověřování službou Multi-Factor Authentication zmírnění rizikových přihlášení
* Zásady na blokování nebo zabezpečené rizikové uživatelské účty
* Zásadu, která vyžaduje uživatelé zaregistrovali k ověření službou Multi-Factor Authentication



## <a name="identity-protection-roles"></a>Role identity Protection

Pro vyrovnávání zatížení správy aktivit kolem vaší implementace Identity Protection můžete přiřadit několik rolí. Azure AD Identity Protection podporuje 3 role adresáře:

| Role                         | Můžete provést                          | Nelze provést.
| :--                          | ---                                |  ---   |
| Globální správce         | Úplný přístup k Identity Protection, připojit Identity Protection| |
| Správce zabezpečení       | Úplný přístup k Identity Protection | Připojení Identity Protection, resetujte hesla pro uživatele |
| Čtenář zabezpečení              | Přístup k Identity Protection jen pro čtení | Připojení Identity Protection, uživatelům remidiate, nakonfigurovat zásady, resetování hesla |




Další podrobnosti najdete v tématu [přiřazení rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)



## <a name="detection"></a>Detection (Detekce)

### <a name="vulnerabilities"></a>Ohrožení zabezpečení

Azure Active Directory Identity Protection analyzuje konfiguraci a zjistí ohrožení zabezpečení, která může mít vliv na identity vašich uživatelů. Další podrobnosti najdete v tématu [chyb zabezpečení detekovaných službou Azure Active Directory Identity Protection](vulnerabilities.md).

### <a name="risk-events"></a>Rizikové události

Azure Active Directory používá algoritmy adaptivní strojového učení a heuristik ke zjištění podezřelé akce, které se vztahují k identity vašich uživatelů. Systém vytvoří záznam pro každé zjištěné podezřelé akce. Tyto záznamy jsou také známé jako rizikové události.  
Další podrobnosti najdete v tématu věnovaném [rizikovým událostem služby Azure Active Directory](../active-directory-identity-protection-risk-events.md).


## <a name="investigation"></a>Šetření
Svou cestu prostřednictvím služby Identity Protection obvykle začíná řídicího panelu služby Identity Protection.

![Náprava](./media/overview/1000.png "nápravy")

Řídicí panel poskytuje přístup:

* Sestavy, jako například **uživatelé označení příznakem rizika**, **rizikových událostí** a **ohrožení zabezpečení**
* Nastavení, jako je konfigurace vašeho **zásady zabezpečení**, **oznámení** a **registracích vícefaktorového ověřování**

Obvykle je vaším výchozím bodem pro šetření, což je proces kontroly aktivity, protokoly a další relevantní informace související s riziková událost se rozhodnout, zda jsou nezbytné kroky ke zmírnění nebo nápravy, a jak se identita dojde k ohrožení bezpečnosti a seznamte se s použití ohrožených identity.

Vaše aktivity šetření tak můžete navázat [oznámení](notifications.md) Azure Active Directory Protection odesílá na e-mailu.

Následující části poskytují další podrobnosti a kroky, které se vztahují k vyšetřování.  


## <a name="risky-sign-ins"></a>Riziková přihlášení

Azure Active Directory detekuje [typech rizikových událostí](../reports-monitoring/concept-risk-events.md#risk-event-types) v reálném čase a v režimu offline. Každý rizikovou událost, která byla zjištěna přihlásit uživatele přispívá k logický pojem volána rizikových přihlášení. Riziková přihlášení je indikátorem pokusu přihlásit, který se nemusí provést legitimním vlastníkem uživatelského účtu.


### <a name="sign-in-risk-level"></a>Úroveň rizika přihlášení

Úroveň rizika přihlášení je označení (vysoká, střední nebo nízká) pravděpodobnost, že pokus o přihlášení nebyl prováděné legitimním vlastníkem uživatelského účtu.

### <a name="mitigating-sign-in-risk-events"></a>Zmírnění rizika přihlašování události

Omezení rizik je akci, kterou chcete omezit schopnost útočníka zneužít ohrožení zabezpečení identity nebo zařízení bez obnovení identity nebo zařízení do bezpečného stavu. Omezení rizik nevyřeší předchozí přihlášení rizikové události spojené s identity nebo zařízení.

Zmírnění rizikových přihlášení automaticky, můžete nakonfigurovat zásady zabezpečení rizik přihlašování. Pomocí těchto zásad, zvažte úroveň rizika uživatele nebo přihlášení k blokování rizikových přihlášení nebo vyžadovat, aby uživatel k provedení ověřování Multi-Factor Authentication. Tato akce může zabránit útočníkům ve využívajícím krádež identity způsobit poškození a může dát nějaký čas zabezpečit identitu.

### <a name="sign-in-risk-security-policy"></a>Zásady zabezpečení rizik přihlašování
Zásady rizik přihlašování se zásady podmíněného přístupu, která vyhodnotí riziko pro konkrétní přihlášení a použije omezení rizik na základě předem definované podmínky a pravidla.

![Zásady rizik přihlašování](./media/overview/1014.png "zásady rizik přihlašování")

Služba Azure AD Identity Protection umožňuje správu zmírnění rizikových přihlášení tím, že vám umožňuje:

* Nastavte uživatele a skupiny, které zásady platí pro:

    ![Zásady rizik přihlašování](./media/overview/1015.png "zásady rizik přihlašování")
* Nastavte rizika přihlašování úrovně prahovou hodnotu (nízká, střední nebo vysoké), která aktivuje zásady:

    ![Zásady rizik přihlašování](./media/overview/1016.png "zásady rizik přihlašování")
* Můžete nastavte řízení vynucení při aktivaci zásady:  

    ![Zásady rizik přihlašování](./media/overview/1017.png "zásady rizik přihlašování")
* Přepnutí stavu zásad:

    ![Registrace MFA](./media/overview/403.png "registrace MFA")
* Kontrola a vyhodnocení dopadu změn před aktivací ho:

    ![Zásady rizik přihlašování](./media/overview/1018.png "zásady rizik přihlašování")

#### <a name="what-you-need-to-know"></a>Co je potřeba vědět
Můžete nakonfigurovat zásady zabezpečení rizik přihlašování vyžadovat vícefaktorové ověřování:

![Zásady rizik přihlašování](./media/overview/1017.png "zásady rizik přihlašování")

Ale z bezpečnostních důvodů se toto nastavení funguje jenom pro uživatele, kteří jsou již zaregistrovány ověřování službou Multi-Factor Authentication. Tento uživatel je zablokovaný, pokud je splněna podmínka tak, aby vyžadovala vícefaktorové ověřování pro uživatele, který ještě není zaregistrovaný k vícefaktorovému ověřování.

Jako osvědčený postup Pokud chcete, aby ověřování službou Multi-Factor Authentication pro rizikových přihlášení, měli byste:

1. Povolit [zásady registrace pro vícefaktorové ověřování](#multi-factor-authentication-registration-policy) pro ovlivnění uživatelé.
2. Vyžadovat ovlivněných uživatelů na přihlášení v relaci rizikové k provedení registrace MFA

Dokončení těchto kroků se zajistí, že ověřování službou Multi-Factor Authentication je vyžadováno pro rizikové přihlášení.

#### <a name="best-practices"></a>Osvědčené postupy
Výběr **vysokou** prahová hodnota snižuje počet, kolikrát zásada se aktivuje a minimalizuje dopad na uživatele.  

Ale nezahrnuje **nízká** a **střední** přihlášení označených jako rizikoví ze zásad, které nemusí blokovat útočník ze zneužití ohrožení zabezpečení identity.

Při nastavování zásad,

* Vyloučit uživatele, kteří nejsou / nemůže mít ověřování službou Multi-Factor Authentication
* Vyloučit uživatele v národních prostředí, kde není praktické povolení zásad (například žádný přístup k technické podpory)
* Vyloučit uživatele, kteří mohou generovat velké množství pozitivní hodnotu false (vývojáři, analytikům zabezpečení)
* Použití **vysokou** prahové hodnoty během počáteční zásad uvádění, nebo pokud musíte minimalizovat problémy, koncový uživatel neuvidí.
* Použití **nízká** prahovou hodnotu, pokud vaše organizace vyžaduje vyšší úroveň zabezpečení. Výběr **nízká** prahová hodnota představuje další uživatele přihlásit výzvy, ale zvýšené zabezpečení.

Doporučené výchozí nastavení pro většinu organizací je a nakonfigurujte pravidlo pro **střední** prahová hodnota hledají rovnováhu mezi použitelnost a zabezpečení.

Zásady rizik přihlašování je:

* Použít pro všechny prohlížeče provoz a přihlášení pomocí moderního ověřování.
* Bez použití aplikací s použitím starší protokoly zabezpečení tím, že zakážete koncového bodu WS-Trust ve federovaných zprostředkovatele identity, jako jsou služby AD FS.

**Rizikových událostí** stránky v konzole Identity Protection obsahuje seznam všech událostí:

* Tato zásada byla použita k
* Můžete zkontrolovat aktivity a zjistit, jestli byla akce odpovídající

Přehled související uživatelské prostředí naleznete v tématu:

* [Obnovení riziková přihlášení](flows.md#risky-sign-in-recovery)
* [Riziková přihlášení blokováno](flows.md#risky-sign-in-blocked)  
* [Možnosti přihlašování s Azure AD Identity Protection](flows.md)  

**Chcete-li otevřít dialogové okno Konfigurace související**:

- Na **Azure AD Identity Protection** okno v **konfigurovat** klikněte na tlačítko **zásady rizik přihlašování**.

    ![Zásady rizik uživatelů](./media/overview/1014.png "zásady rizik uživatelů")



## <a name="users-flagged-for-risk"></a>Uživatelé označení příznakem rizika

Všechny aktivní [rizikových událostí](../reports-monitoring/concept-risk-events.md) , které byly zjištěny službou Azure Active Directory pro uživatele přispívat na logický pojem, který volá uživatelského rizika. Uživatele označené příznakem rizika je indikátorem uživatelského účtu, který mohl být ohrožený.

![Uživatelé označení příznakem rizika](./media/overview/1200.png)


### <a name="user-risk-level"></a>Úroveň rizika uživatele

Úroveň rizika uživatele je indikací (vysoká, střední nebo nízká) pravděpodobnost, že se k ohrožení identity uživatele. To se počítají na základě rizikových událostí uživatele, které jsou spojené s identitou uživatele.

Stav rizikové události je buď **aktivní** nebo **uzavřeno**. Pouze rizikových událostí, které jsou **aktivní** přispívat k výpočtu úroveň rizika uživatele.

Úroveň rizika pro uživatele se počítá pomocí následující vstupy:

* Vliv na uživatele Active rizikových událostí
* Úroveň rizika těchto událostí
* Určuje, zda byla přijata jaké nápravné akce

![Riziko uživatele](./media/overview/1031.png "rizika uživatele")

Můžete použít úrovně rizika uživatele k vytvoření zásady podmíněného přístupu, které zablokovat uživatelům rizikových přihlášení, nebo vynuťte jejich bezpečně změnit své heslo.

### <a name="closing-risk-events-manually"></a>Ručnímu zavření rizikových událostí

Ve většině případů bude trvat nápravné akce, jako je zabezpečené heslo resetovat automaticky zavření rizikové události. Nicméně to nemusí být vždy možné.  
To platí, například když:

* Uživatel s aktivním rizikové události se odstranil.
* Šetření odhalí, že ohlášená riziková událost provést legitimní uživatel

Protože rizikové události, které jsou **aktivní** přispívat k výpočtu rizika uživatele, bude pravděpodobně nutné ručně ručnímu zavření rizikových událostí snížit úroveň rizika.  
V průběhu šetření můžete provést některou z těchto akcí, chcete-li změnit stav rizikové události:

![Akce](./media/overview/34.png "akce")

* **Vyřešit** – Pokud po prozkoumání rizikovou událost, trvalo odpovídajících nápravných akci mimo Identity Protection. proto si myslíte, že by měl za riziková událost zavřená, označte události jako vyřešenou. Vyřešené události se riziková událost stav nastaven na Uzavřeno a rizikové události se už přispívat do uživatelského rizika.
* **Označit jako falešně pozitivní** – v některých případech můžete vyšetřovat rizikovou událost a zjistit, že byla nesprávně označena jako rizikové. Vám může pomoct snížit počet výskytů těchto označením riziková událost jako falešně pozitivní. To vám pomůže zlepšit klasifikace podobné události v budoucnu algoritmech strojového učení. Stav falešně pozitivní událostí je **uzavřeno** a už se přispívat do uživatelského rizika.
* **Ignorovat** – Pokud ještě nevstoupilo v libovolné akci nápravy, ale chcete riziková událost odebrat ze seznamu aktivní, můžete označit rizikové události ignorovat a bude uzavřeno stav události. Ignoruje události nepočítají do uživatelského rizika. Tato možnost by měla sloužit pouze v rámci neobvyklé okolnosti.
* **Znovu aktivovat** -rizikových událostí, které nebyly uzavřeny ručně (výběrem **vyřešit**, **falešně pozitivní**, nebo **Ignorovat**) můžete znovu aktivovat, nastavení události stavu zpět do **aktivní**. Opětovně rizikových událostí přispívat k výpočtu úroveň rizika uživatele. Rizikové události jsou zavřené prostřednictvím nápravy (například zabezpečené heslo resetovat) nelze aktivovat.

**Chcete-li otevřít dialogové okno Konfigurace související**:

1. Na **Azure AD Identity Protection** okně v části **prošetření**, klikněte na tlačítko **rizikových událostí**.

    ![Resetování hesla ruční](./media/overview/1002.png "ruční heslo resetovat")
2. V **rizikových událostí** klikněte na možnost riziko.

    ![Resetování hesla ruční](./media/overview/1003.png "ruční heslo resetovat")
3. V okně rizika klikněte pravým tlačítkem na uživatele.

    ![Resetování hesla ruční](./media/overview/1004.png "ruční heslo resetovat")

### <a name="closing-all-risk-events-for-a-user-manually"></a>Ruční zavření všechny rizikové události pro uživatele
Místo ruční zavření rizikové události pro uživatele jednotlivě, Azure Active Directory Identity Protection také poskytuje metodu pro zavřít všechny události pro uživatele s jedním kliknutím.

![Akce](./media/overview/2222.png "akce")

Po kliknutí na **zavřít všechny události**, jsou uzavřeny všechny události a ovlivněného uživatele už nejsou ohrožení.

### <a name="remediating-user-risk-events"></a>Napravování rizikové události uživatele

Nápravy je akce zabezpečení identity nebo zařízení, který byl dříve podezřelý nebo známé u něho ohrožena bezpečnost. Nápravné akce obnoví identity nebo zařízení do bezpečného stavu a odstraňuje předchozí rizikové události související s identity nebo zařízení.

Chcete-li odstranit uživatele rizikové události, můžete:

* Zabezpečené heslo obnovit ručně odstranit rizikové události uživatele
* Konfigurovat zásady zabezpečení rizik uživatelů k zmírnit dopady nebo automaticky odstranit rizikové události uživatele
* Přeinstalace (RE-Image) nakažených zařízení  

#### <a name="manual-secure-password-reset"></a>Ruční zabezpečené heslo resetovat
Zabezpečené heslo resetovat je efektivní opravy mnoha rizikových událostech a při provádění, automaticky zavře tyto rizikové události a přepočítá úroveň rizika pro uživatele. K zahájení obnovení hesla pro uživatele rizikový uživatel mohou pomocí řídicího panelu služby Identity Protection.

Dialogové okno související poskytuje dva různé způsoby vytvoření nového hesla:

**Resetovat heslo** – vyberte **vyžaduje, aby uživatel k resetování hesla** povolit uživatelské samoobslužné obnovení, pokud má uživatel zaregistrován u služby Multi-Factor authentication. Během uživatele příštím přihlášení bude uživatel vyžaduje řešení výzvu ověřování službou Multi-Factor Authentication úspěšně a pak, budete muset změnit heslo. Tato možnost není dostupná, pokud uživatelský účet ještě není registrované služby Multi-Factor authentication.

**Dočasné heslo** – vyberte **vygenerovat dočasné heslo** okamžitě zneplatnit existující heslo a vytvoří nové dočasné heslo pro daného uživatele. Odešlete nové dočasné heslo na alternativní e-mailovou adresu uživatele nebo jeho manažerovi. Protože je dočasné heslo, uživateli zobrazí výzva ke změně hesla při přihlášení.

![Zásady](./media/overview/1005.png "zásad")

**Chcete-li otevřít dialogové okno Konfigurace související**:

1. Na **Azure AD Identity Protection** okna, klikněte na tlačítko **uživatelé označení příznakem rizika**.

    ![Resetování hesla ruční](./media/overview/1006.png "ruční heslo resetovat")
2. Ze seznamu uživatelů vyberte uživatele s alespoň jeden rizikové události.

    ![Resetování hesla ruční](./media/overview/1007.png "ruční heslo resetovat")
3. V okně uživatele klikněte na tlačítko **resetovat heslo**.

    ![Resetování hesla ruční](./media/overview/1008.png "ruční heslo resetovat")

### <a name="user-risk-security-policy"></a>Zásady zabezpečení rizik uživatelů
Zásady zabezpečení rizik uživatelů se zásady podmíněného přístupu, který vyhodnotí úroveň rizika pro konkrétního uživatele a provede akce nápravy a zmírnění distribuovaných útoků na základě předem definované podmínky a pravidla.

![Zásady rizik uživatelů](./media/overview/1009.png "zásady rizik uživatelů")

Azure AD Identity Protection vám pomůže se správou zmírnění a odstraňování problémů uživatelů označených příznakem rizika umožňuje:

* Nastavte uživatele a skupiny, které zásady platí pro:

    ![Zásady rizik uživatelů](./media/overview/1010.png "zásady rizik uživatelů")
* Nastavte uživatele riziko úrovně prahovou hodnotu (nízká, střední nebo vysoké), která aktivuje zásady:

    ![Zásady rizik uživatelů](./media/overview/1011.png "zásady rizik uživatelů")
* Můžete nastavte řízení vynucení při aktivaci zásady:

    ![Zásady rizik uživatelů](./media/overview/1012.png "zásady rizik uživatelů")
* Přepnutí stavu zásad:

    ![Zásady rizik uživatelů](./media/overview/403.png "registrace MFA")
* Kontrola a vyhodnocení dopadu změn před aktivací ho:

    ![Zásady rizik uživatelů](./media/overview/1013.png "zásady rizik uživatelů")

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

    ![Zásady rizik uživatelů](./media/overview/1009.png "zásady rizik uživatelů")

### <a name="mitigating-user-risk-events"></a>Zmírnění rizikových událostí uživatele
Správci můžou nastavit zásady zabezpečení rizik uživatelů k blokování uživatelů při přihlášení v závislosti na úrovni rizika.

Blokování přihlašování se změnami:

* Brání generování nového uživatele rizikové události pro ovlivněného uživatele
* Správcům umožňuje ručně odstranit rizikové události by to ovlivnilo identitu uživatele a jeho obnovení stavu zabezpečení



## <a name="multi-factor-authentication-registration-policy"></a>Zásady registrace pro vícefaktorové ověřování
Azure Multi-Factor authentication je metodu ověřování, který se vyžaduje použití víc než jenom uživatelské jméno a heslo. Poskytuje druhou vrstvu zabezpečení, které uživatelská přihlášení a transakce.  
Doporučujeme, abyste vyžadují ověřování Azure Multi-Factor Authentication pro přihlášení uživatelů, protože ho:

* Nabízí silné ověřování s řadou jednoduchých možností
* Hrají klíčovou roli při přípravě vaší organizaci, aby ochrana a obnovení z účtu ohrožení

![Zásady rizik uživatelů](./media/overview/1019.png "zásady rizik uživatelů")

Další podrobnosti najdete v tématu [co je Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)

Služba Azure AD Identity Protection umožňuje správu zavedení registrace služby Multi-Factor authentication tím, že nakonfigurujete zásady, které vám umožní:

* Nastavte uživatele a skupiny, které zásady platí pro:

    ![Zásady vícefaktorového ověřování](./media/overview/1020.png "zásad vícefaktorového ověřování")
* Můžete nastavit řízení vynucení při aktivaci zásady::  

    ![Zásady vícefaktorového ověřování](./media/overview/1021.png "zásad vícefaktorového ověřování")
* Přepnutí stavu zásad:

    ![Zásady vícefaktorového ověřování](./media/overview/403.png "zásad vícefaktorového ověřování")
* Zobrazte aktuální stav registrace:

    ![Zásady vícefaktorového ověřování](./media/overview/1022.png "zásad vícefaktorového ověřování")

Přehled související uživatelské prostředí naleznete v tématu:

* [Tok ověřování službou Multi-Factor Authentication registrace](flows.md#multi-factor-authentication-registration).  
* [Přihlašovací prostředí v Azure AD Identity Protection](flows.md).  

**Chcete-li otevřít dialogové okno Konfigurace související**:

- Na **Azure AD Identity Protection** okno v **konfigurovat** klikněte na tlačítko **registracích vícefaktorového ověřování**.

    ![Zásady vícefaktorového ověřování](./media/overview/1019.png "zásad vícefaktorového ověřování")

## <a name="next-steps"></a>Další postup
* [Kanál 9: Azure AD a Identity Show: Identity Protection ve verzi Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

* [Povolení služby Azure Active Directory Identity Protection](enable.md)

* [Chyb zabezpečení detekovaných službou Azure Active Directory Identity Protection](vulnerabilities.md)

* [Azure Active Directory rizikových událostí](../active-directory-identity-protection-risk-events.md)

* [Oznámení služby Azure Active Directory Identity Protection](notifications.md)

* [Playbook Azure Active Directory Identity Protection](playbook.md)

* [Glosář Azure Active Directory Identity Protection](glossary.md)

* [Možnosti přihlašování s Azure AD Identity Protection](flows.md)

* [Azure Active Directory Identity Protection – jak odblokovat uživatele](howto-unblock-user.md)

* [Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](graph-get-started.md)
