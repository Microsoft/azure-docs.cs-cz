---
title: Ochrany identit Azure Active Directory | Microsoft Docs
description: Zjistěte, jak Azure AD Identity Protection umožňuje omezit možnost útočník zneužít ohroženými identity nebo zařízení a zabezpečit identity nebo zařízení, která byla dříve by mohly vzbuzovat podezření nebo známé došlo k narušení.
services: active-directory
keywords: ochrany identit Azure active directory, cloud app discovery,. Správa aplikací, zabezpečení, rizik, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: b8ae865e06e085ebe1bb71b35d812024190e2b21
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

Azure Active Directory ochrany identit je funkce služby Azure AD Premium P2 edici, která vám umožní:

- Zjistit potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci

- Nakonfigurovat automatické odpovědi na zjištěné podezřelé akce, které se vztahují k identity ve vaší organizaci  

- Prozkoumat podezřelé incidenty a proveďte příslušné akce jejich řešení   


## <a name="getting-started"></a>Začínáme

Microsoft má zabezpečené cloudové identity pro více než deset. S Azure Active Directory Identity Protection ve vašem prostředí, můžete použít stejné ochrany systémů, které společnost Microsoft používá k zabezpečení identity.

Velká většina narušení zabezpečení se provede při útočník přístupu do prostředí tak, že krádež identity uživatele. V průběhu let se staly útočníci stále efektivní využívání narušení třetích stran a za použití útoky phishing sofistikované. Jakmile útočník získá přístup k i nízkou privilegované uživatelských účtů, je pro ně k získání přístupu k prostředkům společnosti důležité prostřednictvím laterální pohyb je poměrně snadné.

V důsledku toho je potřeba:

- Chránit všechny identity, bez ohledu na jejich úroveň oprávnění

- Proaktivní ohroženými identity zabránit se překročen

Zjišťování ohrožení zabezpečení identity je bez snadno úlohy. Azure Active Directory používá algoritmy adaptivní strojového učení a heuristiky zjištění anomálií a podezřelé incidenty, které indikují potenciálně ohrožených identity. Na základě těchto dat Identity Protection generuje sestavy a výstrahy, které umožňují vyhodnotit zjištěné problémy a proveďte příslušné zmírnění nebo nápravné akce.

Azure Active Directory Identity Protection je větší než monitorování a vytváření sestav nástroje. Pokud chcete ochránit identity ve vaší organizaci, můžete nakonfigurovat zásady na základě rizik, které automaticky reagovat na zjištěné problémy, pokud bylo dosaženo úroveň zadaný rizika. Tyto zásady, kromě jiných ovládacích prvků podmíněného přístupu poskytuje Azure Active Directory a EMS, můžete buď automaticky blokovat nebo zahájit adaptivní nápravných akcí, které resetuje včetně heslo a vynucování služby Multi-Factor authentication.


#### <a name="identity-protection-capabilities"></a>Funkce ochrany identit

**Zjišťování ohrožení zabezpečení a rizikové účty:**  

* Poskytování vlastních doporučení pro zlepšení celkové postavení zabezpečení podle zvýraznění ohrožení zabezpečení
* Výpočet úrovní rizika přihlášení
* Výpočet úrovní rizika uživatele


**Zkoumání rizikových událostí:**

* Odesílání oznámení o rizikových událostech
* Zkoumání rizikových událostí pomocí relevantní a kontextové informace
* Poskytuje základní pracovní postupy pro sledování šetření
* Poskytuje snadný přístup k nápravné akce, jako je například resetování hesla

**Zásady podmíněného přístupu na základě rizika:**

* Zásady pro zmírnění rizikové přihlášení blokování přihlášení nebo že vyřeší problémy spojené vícefaktorového ověřování
* Zásady na blokování nebo zabezpečený rizikové uživatelské účty
* Zásady budou muset uživatelé zaregistrovat u služby Multi-Factor authentication



## <a name="identity-protection-roles"></a>Role ochranu identity

Pro vyrovnávání zatížení činnosti správy kolem implementaci Identity Protection můžete přiřadit několik rolí. Azure AD Identity Protection podporuje 3 directory role:

| Role                         | Můžete provést                          | Nelze provést
| :--                          | ---                                |  ---   |
| Globální správce         | Úplný přístup k ochraně Identity, zařadit ochranu Identity| |
| Správce zabezpečení       | Úplný přístup k Identity Protection | Zařadit Identity Protection resetovat hesla pro uživatele |
| Čtenář zabezpečení              | Přístup jen pro čtení k Identity Protection | Zařadit Identity Protection, uživatelé remidiate, nakonfigurovat zásady, resetování hesla |




Další podrobnosti najdete v tématu [přiřazení rolí správce v Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)



## <a name="detection"></a>Detection (Detekce)

### <a name="vulnerabilities"></a>Ohrožení zabezpečení

Azure Active Directory Identity Protection analýz konfiguraci a zjistí chyby zabezpečení, které můžou mít vliv na identit uživatelů. Další podrobnosti najdete v tématu [chyb zabezpečení detekovaných službou Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md).

### <a name="risk-events"></a>Rizikové události

Azure Active Directory používá algoritmy adaptivní strojového učení a heuristiky ke zjištění podezřelé akce, které se vztahují k identit uživatelů. Systém vytvoří záznam pro každé zjištěné podezřelé akce. Tyto záznamy se také označují jako rizikových událostech.  
Další podrobnosti najdete v tématu věnovaném [rizikovým událostem služby Azure Active Directory](active-directory-identity-protection-risk-events.md).


## <a name="investigation"></a>Šetření
Vaše cesty přes Identity Protection obvykle začíná řídicího panelu ochrany identit.

![Náprava](./media/active-directory-identityprotection/1000.png "nápravy")

Řídicí panel poskytuje přístup k:

* Sestavy, jako **uživatelé označení příznakem rizik**, **rizik události** a **ohrožení zabezpečení**
* Nastavení, jako například konfigurace vaší **zásady zabezpečení**, **oznámení** a **registrace služby Multi-Factor authentication**

Je obvykle počáteční bod pro šetření, což je proces kontroly aktivity, protokoly a další důležité informace vztahující se k události riziko rozhodnout, zda jsou potřebné kroky ke zmírnění nebo nápravy, a jak se identita dojde k ohrožení a pochopit použití ohroženými identity.

Dokáže spojit vaše aktivity šetření a [oznámení](active-directory-identityprotection-notifications.md) Azure Active Directory Protection odešle na e-mailu.

Následující části poskytují další podrobnosti a kroky, které se vztahují k vyšetřování.  


## <a name="risky-sign-ins"></a>Riziková přihlášení

Azure Active Directory zjistí [rizik typů událostí](active-directory-reporting-risk-events.md#risk-event-types) v reálném čase a offline. Každý riziko událost, která byla zjištěna u přihlášení uživatele přispívá k logický pojem, který volá rizikové přihlášení. Rizikové přihlášení je indikátorem pro pokusu přihlášení, který nemusí provedly legitimní vlastníkem uživatelského účtu.


### <a name="sign-in-risk-level"></a>Úroveň rizika přihlášení

Úroveň rizika přihlášení je označením (vysoká, střední nebo nízká) pravděpodobnost, že pokus o přihlášení nebyla provedena legitimní vlastníkem uživatelského účtu.

### <a name="mitigating-sign-in-risk-events"></a>Zmírnění rizika přihlašovací události

Zmírnění je akci, kterou chcete omezit schopnost útočník zneužít ohroženými identity nebo zařízení bez obnovení identity nebo zařízení do bezpečného stavu. Zmírnění nevyřeší předchozí přihlášení rizikových událostech spojených s identity nebo zařízení.

Pro zmírnění rizikové přihlášení automaticky, můžete nakonfigurovat zásady zabezpečení riziko přihlášení. Pomocí těchto zásad, zvažte úroveň rizika uživatele nebo přihlášení k blokování rizikové přihlášení nebo vyžadovat, aby uživatel k provedení ověřování Multi-Factor authentication. Tato akce může zabránit útočníkům ve využívání odcizené identity způsobit poškození a může získáte chvíli k zabezpečení identity.

### <a name="sign-in-risk-security-policy"></a>Zásady zabezpečení riziko přihlášení
Zásady přihlášení riziko je zásadu podmíněného přístupu, která vyhodnotí riziko pro konkrétní přihlášení a použije způsoby zmírnění rizik na základě předem definované podmínky a pravidla.

![Zásady přihlášení riziko](./media/active-directory-identityprotection/1014.png "zásad riziko přihlašování")

Azure AD Identity Protection pomáhá spravovat zmírnění rizikové přihlášení tím, že vám umožňuje:

* Nastavte uživatele a skupiny, které zásady platí pro:

    ![Zásady přihlášení riziko](./media/active-directory-identityprotection/1015.png "zásad riziko přihlašování")
* Nastavte přihlašovací riziko úrovně prahovou hodnotu (nízká, střední nebo vysokou), která spustí zásady:

    ![Zásady přihlášení riziko](./media/active-directory-identityprotection/1016.png "zásad riziko přihlašování")
* Nastavte ovládací prvky vynutit, pokud aktivuje zásady:  

    ![Zásady přihlášení riziko](./media/active-directory-identityprotection/1017.png "zásad riziko přihlašování")
* Přepnutí stavu zásad:

    ![Registrace MFA](./media/active-directory-identityprotection/403.png "registrace MFA")
* Kontrola a vyhodnocení dopad změny před aktivací ho:

    ![Zásady přihlášení riziko](./media/active-directory-identityprotection/1018.png "zásad riziko přihlašování")

#### <a name="what-you-need-to-know"></a>Co potřebujete vědět
Můžete nakonfigurovat zásady zabezpečení riziko přihlášení pro požadovat použití vícefaktorového ověřování:

![Zásady přihlášení riziko](./media/active-directory-identityprotection/1017.png "zásad riziko přihlašování")

Ale z bezpečnostních důvodů se toto nastavení funguje pouze pro uživatele, kteří již byl registrován pro službu Multi-Factor authentication. Pokud je splněna podmínka vyžadovat vícefaktorové ověřování pro uživatele, který dosud není registrován u služby Multi-Factor authentication, uživatel je blokován.

Jako osvědčený postup Pokud chcete požadovat použití vícefaktorového ověřování pro rizikové přihlášení, proveďte následující kroky:

1. Povolit [zásady registrace služby Multi-Factor authentication](#multi-factor-authentication-registration-policy) pro příslušné uživatele.
2. Vyžadovat ovlivněných uživatelů na přihlášení v relaci rizikové k provedení registrace MFA

Dokončení těchto kroků zajistí, že je vyžadované pro rizikové přihlášení vícefaktorové ověřování.

#### <a name="best-practices"></a>Osvědčené postupy
Výběr **vysokou** prahová hodnota snižuje počet zásady se aktivuje a minimalizuje dopad na uživatele.  

Ale vyloučí **nízká** a **střední** přihlášení příznakem rizik ze zásad, které nemusí blokovat útočník z zneužitím ohrožení zabezpečení identity.

Při nastavení zásad,

* Vyloučit uživatele, kteří nepodporují / nemůže mít vícefaktorového ověřování
* Vyloučit uživatele v národní prostředí, kde není praktické povolení zásad (například žádný přístup na technickou podporu)
* Vyloučení uživatelé, kteří jsou pravděpodobně vygeneroval velké množství false pozitivních (vývojáři, analytikům zabezpečení)
* Použití **vysokou** prahová hodnota během počáteční zásadách, nebo pokud minimalizujete musí výzvy pohledu koncové uživatele.
* Použití **nízká** prahovou hodnotu, pokud vaše organizace vyžaduje vyšší úroveň zabezpečení. Výběr **nízká** prahová hodnota zavádí další uživatelské přihlašovací výzvy, ale zvýšení zabezpečení.

Doporučené výchozí hodnota pro většinu organizací je nakonfigurovat pravidlo pro **střední** prahovou hodnotu na vytvořit rovnováhu mezi využitelností a zabezpečení.

Zásady přihlášení riziko je:

* Použít pro všechny přenosy prohlížeče a přihlášení pomocí moderní ověřování.
* Nevztahuje se na aplikace, které používají starší protokoly zabezpečení zakázáním WS-Trust koncový bod na federované rozšíření IDP, jako je například služba AD FS.

**Rizikových událostech** stránky v konzole Identity Protection zobrazuje všechny události:

* Použití této zásady
* Můžete zkontrolovat aktivity a zjistit, zda byla akce odpovídající

Přehled související uživatelské prostředí najdete v tématu:

* [Obnovení rizikové přihlášení](active-directory-identityprotection-flows.md#risky-sign-in-recovery)
* [Rizikové přihlášení blokován](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  
* [Možnosti přihlášení s Azure AD Identity Protection](active-directory-identityprotection-flows.md)  

**Tím otevřete dialogové okno Konfigurace související**:

- Na **Azure AD Identity Protection** okno v **konfigurace** klikněte na tlačítko **zásad přihlašování riziko**.

    ![Zásady uživatele ridk](./media/active-directory-identityprotection/1014.png "ridk zásady uživatele")



## <a name="users-flagged-for-risk"></a>Uživatelé označení příznakem rizika

Všechny aktivní [rizik události](active-directory-identity-protection-risk-events.md) , byly zjištěny službou Azure Active Directory pro uživatele můžete přispět k logický pojem, který volá riziko pro uživatele. Uživatele s příznakem pro riziko je indikátorem pro uživatelský účet, který byl napaden.

![Uživatelé označení příznakem rizika](./media/active-directory-identityprotection/1200.png)


### <a name="user-risk-level"></a>Úroveň rizika uživatele

Úroveň rizika uživatel je označením (vysoká, střední nebo nízká) pravděpodobnost, že byl napaden identitu uživatele. Se počítá na základě událostí riziko uživatele, které jsou přidružené k identitě uživatele.

Stav události riziko je buď **Active** nebo **uzavřeno**. Pouze riziko události, které jsou **Active** podílet se na uživatelské úrovni výpočet riziko.

Úroveň rizika uživatele se počítá pomocí následující zadání:

* Aktivní rizikových událostech, které mají vliv uživatele
* Úroveň rizika tyto události
* Jestli nebyly provedeny žádné nápravné akce

![Uživatel rizika](./media/active-directory-identityprotection/1031.png "rizika uživatele")

Můžete použít k vytvoření zásady podmíněného přístupu, které zablokovat rizikové uživatelům přihlášení úrovní rizika uživatele nebo vynutit, aby bezpečně změnit své heslo.

### <a name="closing-risk-events-manually"></a>Zavřením rizikových událostí ručně

Ve většině případů bude trvat nápravné akce, jako například zabezpečené heslo resetovat automaticky uzavřít rizikových událostech. Ale to nemusí být vždy možné.  
Toto je, například případě, když:

* Uživatel s Active rizikových událostí byl odstraněn.
* Šetření zjistí, že byla událostí vykazuje riziko provést legitimní uživatel

Protože rizikových událostech, které jsou **Active** přispívat k výpočtu riziko uživatele, možná budete muset ručně nižší úroveň rizika ukončením rizikových událostí ručně.  
Během šetření můžete provést některou z těchto akcí pro změnu stavu riziko události:

![Akce](./media/active-directory-identityprotection/34.png "akce")

* **Vyřešte** – Pokud po prozkoumání riziko událostí, trvalo akce odpovídající nápravu mimo ochrany identit a budete mít dojem, že riziko události by se měly zvažovat zavřená, označte událostí jako Vyřešeno. Přeložit události na uzavřený nastaví stav riziko události a události riziko už přispějí k riziko pro uživatele.
* **Označit jako falešně pozitivní** – v některých případech můžete prozkoumat události riziko a zjistit, že byla nesprávně označena jako rizikové. Můžete snížit počet takových výskytů označením riziko událostí jako falešně pozitivní. To vám pomůže zlepšit klasifikaci podobné události v budoucnu algoritmů strojového učení. Stav falešně pozitivní událostí je **uzavřeno** a už přispívají k riziko pro uživatele.
* **Ignorovat** – Pokud jste ještě nevstoupilo žádnou akci nápravy, ale má být odebrán ze seznamu active událost riziko můžete označit riziko událostí ignorovat a bude uzavřen stav události. Ignoruje události nepřispívají k riziko pro uživatele. Tato možnost by měla být použita pouze za neobvyklé okolnosti.
* **Znovu aktivovat** -riziko události, které byly ručně (výběrem **vyřešit**, **falešně pozitivní**, nebo **Ignorovat**) lze znovu aktivovat, nastavení události stavu zpět do **Active**. Opětovně aktivovaných rizikových událostech podílet se na uživatelské úrovni výpočet riziko. Nelze znovu aktivovat, rizikových událostech (například zabezpečené heslo resetovat) uzavřeny prostřednictvím nápravy.

**Tím otevřete dialogové okno Konfigurace související**:

1. Na **Azure AD Identity Protection** okno, v části **prošetření**, klikněte na tlačítko **rizik události**.

    ![Resetování hesla ruční](./media/active-directory-identityprotection/1002.png "resetování hesla ruční")
2. V **rizik události** klikněte na riziko.

    ![Resetování hesla ruční](./media/active-directory-identityprotection/1003.png "resetování hesla ruční")
3. V okně riziko klikněte pravým tlačítkem na uživatele.

    ![Resetování hesla ruční](./media/active-directory-identityprotection/1004.png "resetování hesla ruční")

### <a name="closing-all-risk-events-for-a-user-manually"></a>Zavřít všechny události riziko pro uživatele ručně
Místo ručně zavřete riziko události pro uživatele jednotlivě, Azure Active Directory Identity Protection také poskytuje metodu zavřete všechny události pro uživatele s jedním kliknutím.

![Akce](./media/active-directory-identityprotection/2222.png "akce")

Když kliknete na tlačítko **zavřít všechny události**, jsou zavřeny všechny události a ovlivněného uživatele jsou již v ohrožení.

### <a name="remediating-user-risk-events"></a>Nekompatibilních rizikových událostí uživatele

Nápravy je akci, kterou chcete zabezpečit identity nebo zařízení, která byla dříve by mohly vzbuzovat podezření nebo známé došlo k narušení. Akce nápravy obnoví identity nebo zařízení do bezpečného stavu a odstraňuje předchozí rizikových událostech spojených s identity nebo zařízení.

Chcete-li opravit událostí riziko uživatele, můžete:

* Zabezpečené heslo resetovat k nápravě uživatele rizikových událostí ručně
* Konfigurace uživatelských zásad zabezpečení riziko zmírnit nebo automaticky napravovat riziko událostí uživatele
* Znovu přeinstalovat image nakažených zařízení  

#### <a name="manual-secure-password-reset"></a>Resetování ruční zabezpečeného hesla
Resetování zabezpečeného hesla je efektivní nápravy pro mnoho událostí riziko a při provádění automaticky zavře tyto události riziko přepočítá úroveň rizika uživatele. Řídicí panel ochrany identit můžete zahájit obnovení hesla pro rizikové uživatele.

Související dialogové okno obsahuje dvě různé metody pro obnovení hesla:

**Resetovat heslo** – vyberte **vyžadovat, aby uživatel změnit heslo** chcete umožnit uživatelům samoobslužné obnovení, pokud má uživatel zaregistrován u služby Multi-Factor authentication. Při jeho příštím přihlášení bude potřeba řešit výzvy ověřování Multi-Factor authentication úspěšně a pak přinucení změnit heslo uživatele. Tato možnost není dostupná, pokud uživatelský účet ještě není registrované služby Multi-Factor authentication.

**Dočasné heslo** – vyberte **generovat dočasné heslo** okamžitě zneplatní stávající heslo a vytvořit nové dočasné heslo pro uživatele. Odeslání nové dočasné heslo na alternativní e-mailovou adresu pro uživatele nebo pro uživatele správce. Protože je dočasné heslo, uživatel se vyzve k změnit heslo při přihlášení.

![Zásady](./media/active-directory-identityprotection/1005.png "zásad")

**Tím otevřete dialogové okno Konfigurace související**:

1. Na **Azure AD Identity Protection** okně klikněte na tlačítko **uživatelé označení příznakem rizik**.

    ![Resetování hesla ruční](./media/active-directory-identityprotection/1006.png "resetování hesla ruční")
2. V seznamu uživatelů vyberte uživatele, který má alespoň jeden rizikových událostí.

    ![Resetování hesla ruční](./media/active-directory-identityprotection/1007.png "resetování hesla ruční")
3. V okně uživatele klikněte na **resetovat heslo**.

    ![Resetování hesla ruční](./media/active-directory-identityprotection/1008.png "resetování hesla ruční")

### <a name="user-risk-security-policy"></a>Zásada zabezpečení riziko uživatelů
Zásady uživatele rizik zabezpečení je zásadu podmíněného přístupu, která vyhodnotí úroveň rizika pro konkrétního uživatele a použije nápravy a zmírnění akce na základě předem definované podmínky a pravidla.

![Zásady uživatele ridk](./media/active-directory-identityprotection/1009.png "ridk zásady uživatele")

Azure AD Identity Protection pomáhá spravovat omezení rizik a náprava uživatelé označení příznakem rizik a to:

* Nastavte uživatele a skupiny, které zásady platí pro:

    ![Zásady uživatele ridk](./media/active-directory-identityprotection/1010.png "ridk zásady uživatele")
* Nastavte uživatele riziko úrovně prahovou hodnotu (nízká, střední nebo vysokou), která spustí zásady:

    ![Zásady uživatele ridk](./media/active-directory-identityprotection/1011.png "ridk zásady uživatele")
* Nastavte ovládací prvky vynutit, pokud aktivuje zásady:

    ![Zásady uživatele ridk](./media/active-directory-identityprotection/1012.png "ridk zásady uživatele")
* Přepnutí stavu zásad:

    ![Zásady uživatele ridk](./media/active-directory-identityprotection/403.png "registrace MFA")
* Kontrola a vyhodnocení dopad změny před aktivací ho:

    ![Zásady uživatele ridk](./media/active-directory-identityprotection/1013.png "ridk zásady uživatele")

Výběr **vysokou** prahová hodnota snižuje počet zásady se aktivuje a minimalizuje dopad na uživatele.
Ale vyloučí **nízká** a **střední** uživatelé označení příznakem rizik ze zásad, které nemusí zabezpečit identity nebo zařízení, měla by mohly vzbuzovat podezření nebo známé došlo k narušení.

Při nastavení zásad,

* Vyloučení uživatelé, kteří jsou pravděpodobně vygeneroval velké množství false pozitivních (vývojáři, analytikům zabezpečení)
* Vyloučit uživatele v národní prostředí, kde není praktické povolení zásad (například žádný přístup na technickou podporu)
* Použití **vysokou** prahová hodnota během počáteční zásadách, nebo pokud minimalizujete musí výzvy pohledu koncové uživatele.
* Použití **nízká** prahovou hodnotu, pokud vaše organizace vyžaduje vyšší úroveň zabezpečení. Výběr **nízká** prahová hodnota zavádí další uživatelské přihlašovací výzvy, ale zvýšení zabezpečení.

Doporučené výchozí hodnota pro většinu organizací je nakonfigurovat pravidlo pro **střední** prahovou hodnotu na vytvořit rovnováhu mezi využitelností a zabezpečení.

Přehled související uživatelské prostředí najdete v tématu:

* [Dojde k ohrožení tok obnovení účtu](active-directory-identityprotection-flows.md#compromised-account-recovery).  
* [Dojde k ohrožení účet byl uzamčen toku](active-directory-identityprotection-flows.md#compromised-account-blocked).  

**Tím otevřete dialogové okno Konfigurace související**:

- Na **Azure AD Identity Protection** okno v **konfigurace** klikněte na tlačítko **zásady uživatele riziko**.

    ![Zásady uživatele ridk](./media/active-directory-identityprotection/1009.png "ridk zásady uživatele")

### <a name="mitigating-user-risk-events"></a>Zmírnění rizik událostí uživatele
Správci mohou nastavit zásady uživatele riziko zabezpečení pro blokování uživatele při přihlášení v závislosti na úroveň rizika.

Blokování přihlášení:

* Zabrání generování nových událostí riziko uživatele pro ovlivněného uživatele
* Umožňuje správcům ručně opravit rizikových událostech, které mají vliv na identitu uživatele a obnovte ji do zabezpečené stavu



## <a name="multi-factor-authentication-registration-policy"></a>Zásady registrace pro vícefaktorové ověřování
Ověřování Azure Multi-Factor authentication je metoda ověřování, který jste, která vyžaduje použití víc věcí než jenom uživatelské jméno a heslo. Poskytuje druhou vrstvu zabezpečení uživatelská přihlášení a transakce.  
Doporučujeme vyžadovat ověřování Azure Multi-Factor authentication pro přihlášení uživatele, protože ho:

* Poskytuje silné ověřování s celou řadu možností snadno ověření
* Hraje důležitou roli při přípravě vaší organizace k ochraně a obnovování z účtu ohrožení

![Zásady uživatele ridk](./media/active-directory-identityprotection/1019.png "ridk zásady uživatele")

Další podrobnosti najdete v tématu [co je Azure Multi-Factor Authentication?](authentication/multi-factor-authentication.md)

Azure AD Identity Protection pomáhá spravovat zavádění registrace služby Multi-Factor authentication tím, že nakonfigurujete zásadu, která umožňuje:

* Nastavte uživatele a skupiny, které zásady platí pro:

    ![Zásady vícefaktorového ověřování](./media/active-directory-identityprotection/1020.png "zásad vícefaktorového ověřování")
* Nastavit ovládací prvky vynutit, pokud zásady aktivuje::  

    ![Zásady vícefaktorového ověřování](./media/active-directory-identityprotection/1021.png "zásad vícefaktorového ověřování")
* Přepnutí stavu zásad:

    ![Zásady vícefaktorového ověřování](./media/active-directory-identityprotection/403.png "zásad vícefaktorového ověřování")
* Zobrazte aktuální stav registrace:

    ![Zásady vícefaktorového ověřování](./media/active-directory-identityprotection/1022.png "zásad vícefaktorového ověřování")

Přehled související uživatelské prostředí najdete v tématu:

* [Postup registrace služby Multi-Factor authentication](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  
* [Přihlášení vyskytne s Azure AD Identity Protection](active-directory-identityprotection-flows.md).  

**Tím otevřete dialogové okno Konfigurace související**:

- Na **Azure AD Identity Protection** okno v **konfigurace** klikněte na tlačítko **registrace služby Multi-Factor authentication**.

    ![Zásady vícefaktorového ověřování](./media/active-directory-identityprotection/1019.png "zásad vícefaktorového ověřování")

## <a name="next-steps"></a>Další postup
* [Kanál 9: Azure AD a Identity zobrazení: Identity Protection verze Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

* [Povolení ochrany identit Azure Active Directory](active-directory-identityprotection-enable.md)

* [Chyb zabezpečení detekovaných službou Azure Active Directory Identity Protection](active-directory-identityprotection-vulnerabilities.md)

* [Azure Active Directory rizikových událostí](active-directory-identity-protection-risk-events.md)

* [Azure oznámení ochrany identit služby Active Directory](active-directory-identityprotection-notifications.md)

* [Azure seznam strategií ochrany identit Active Directory](active-directory-identityprotection-playbook.md)

* [Azure slovník ochrany identit služby Active Directory](active-directory-identityprotection-glossary.md)

* [Možnosti přihlášení s Azure AD Identity Protection](active-directory-identityprotection-flows.md)

* [Azure Active Directory identitu ochrana – jak odblokovat uživatele](active-directory-identityprotection-unblock-howto.md)

* [Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
