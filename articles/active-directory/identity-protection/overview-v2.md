---
title: Co je Azure Active Directory Identity Protection (Aktualizovat)? | Dokumenty Microsoft
description: Co je Azure Active Directory Identity Protection (Aktualizovat)?
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 9cb1c5ebf8809ca3f4f977b513ecaa7909b401c6
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55452273"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>Co je Azure Active Directory Identity Protection (Aktualizovat)?

Byl aktualizován prostředí Identity Protection k lepší ochraně identity ve vaší organizaci. Tato aktualizace prostředí nabízí:

- Přepracovaná správce prostředí, který otáčí kolem riziko/jí nejvíce hodilo you¬ uživatelského rizika a riziko přihlášení

- Výkonné vyšetřování prostředí s podporou pro filtrování, řazení a inteligentní soubory ke stažení

- Vylepšené výpočet rizika uživatele umožňují stanovit priority vašeho úsilí na uživatele, kteří se s největší pravděpodobností u něho ohrožena bezpečnost

- Nové rozhraní API podporují povolit programový přístup k datům rizika

- Proces zpětné vazby zjednodušenou správu, který umožňuje okamžitě chránit vaši uživatelé

- Nová pod dohledem strojového učení pro zvýšení přesnosti vyhodnocení rizik



Zabezpečení je velmi důležité pro organizace ještě dnes. Většina trvat porušením zabezpečení umístěte Pokud útočníci získají přístup k prostředí krádeží identity uživatele. V průběhu let staly útočníci stále častěji efektivní využití porušení třetích stran a pomocí sofistikovaných útoků phishing útoky. Jakmile útočníci získají přístup k i nízkou privilegovaných uživatelské účty, je poměrně snadné pro jim umožní získat přístup k prostředkům společnosti důležité prostřednictvím taktiky Lateral Movement. 

Reakce na ně, Azure AD Identity Protection vám dává možnost: 

- Proaktivně ohrožení zabezpečení identity zabránit se zneužít 

- Automaticky zmírnění rizik, když zjistí podezřelou aktivitu 

- Prozkoumat rizikových uživatelů a přihlášení do adresy potenciální ohrožení zabezpečení  

- Dostat oznámení, když rizika uživatele dosáhne zadanou prahovou hodnotu 

 

Azure AD Identity Protection je funkce Azure Active Directory Premium P2, která vám umožní nakonfigurovat zásady, které automaticky reagují, když dojde k ohrožení identity uživatele nebo když je někdo jiný než vlastník účtu se pokouší přihlásit pomocí svých Identita. Tyto zásady, kromě jiných ovládacích prvků podmíněný přístup k dispozici službou Azure AD, můžete buď automaticky blokovat přístup nebo inicializace zmírňující opatření, jako je například resetování hesla nebo vynucení služby Multi-Factor authentication. Kromě toho Identity Protection poskytuje možnosti monitorování a vytváření sestav získat podrobnější přehled řízení rizik a potenciální ohrožení ve vaší organizaci. 



## <a name="risk-events"></a>Rizikové události

Azure AD Identity Protection zjistí následujících rizikových událostí: 

 

| Typ rizikové události | Popis | Typ detekce |
| ---             | ---         | ---            |
| Neobvyklá cesta | Přihlášení z neobvyklé umístění podle uživatele poslední přihlášení. | Offline |
| Anonymní IP adresa | Přihlášení z anonymní IP adresy (například: Tor prohlížeče, programu anonymizer VPN). | V reálném čase |
| Neznámé vlastnosti přihlášení | Přihlaste se pomocí vlastnosti, které nejsou zaznamenali nedávno pro daného uživatele. | V reálném čase |
| IP adresy související s malwarem | Přihlášení z IP adresy propojené malwaru | Offline |
| Úniku přihlašovacích údajů | Tato riziková událost znamená, že unikly platné přihlašovací údaje uživatele. | Offline |





## <a name="types-of-risk"></a>Typy rizik 

Identity Protection je založena na dva druhy rizika:

- Riziko přihlášení

- Riziko uživatele

### <a name="sign-in-risk"></a>Riziko přihlášení

Riziko přihlášení představuje pravděpodobnost, že žádost o ověření dané nemá oprávnění od vlastníka identity.

Existují dva hodnocení rizik přihlašování: 

- **Riziko přihlášení (v reálném čase)** -rizika přihlášení (v reálném čase) je založena na všechna nalezení v reálném čase, které aktivují během zpracování přihlášení.  

- **Riziko přihlášení (agregace)** -rizika přihlášení (agregace) je celkový počet riziko, že přihlášení. Počítá se službou machine learning model, který bere v úvahu:

    - V reálném čase detekce (popsaných výše)
    
    - Offline detekce, (, které se aktivují po přihlášení proběhla) 
    
    - Všechny ostatní funkce přihlášení


### <a name="user-risk"></a>Riziko uživatele

Riziko uživatele představuje pravděpodobnost, že dojde k ohrožení danou identitu. 

Riziko uživatele se vypočte tak, že vzhledem k tomu všechna rizika spojená s uživatelem:

- Všechna riziková přihlášení
- Všechny rizikové události není propojená se u přihlášení
- Aktuální rizika uživatele
- Všechny rizikové propouštění nebo nápravy akce prováděné na uživatele, pokladny data



## <a name="how-identity-protection-detects-risk"></a>Zjistí, jak Identity Protection rizikem  

Azure AD pomocí strojového učení detekovat anomálie a podezřelé aktivity, pomocí obou signály zjistil v v reálném čase během přihlášení stejně jako jiné reálném čase signál související pro uživatele a jejich aktivit přihlašování. Na základě těchto dat Identity Protection vypočítá v reálném čase rizika přihlašování při pokaždé, když uživatel přihlásí, a také určení celkového úroveň rizika uživatele pro uživatele dosah. Ochrana identity umožňuje automaticky provádět akce na tyto detekce rizik konfigurace uživatelského rizika Identity Protection a její zásady rizik přihlašování.  

 

Abyste pochopili, jak zjistí Identity Protection rizikem, existují dva důležité koncepty: riziko uživatele a riziko přihlášení. Riziko přihlášení odráží pravděpodobnost, že žádost o ověření dané nemá oprávnění od vlastníka identity. Existují dva typy rizik přihlašování: v reálném čase a celkový počet. V reálném čase rizika přihlášení je zjištěna v době dané pokus o přihlášení (například přihlásil z anonymních IP adres). Celkový počet rizika přihlášení je agregace zjištěných v reálném čase přihlášení rizika stejně jako jakékoli následné reálném čase rizikové události přidružené k uživateli přihlášení (například neuskutečnitelná cesta). Riziko uživatele odráží celkové pravděpodobnost, že má chybný actor ohrožena danou identitu. Riziko uživatele obsahuje všechny aktivity riziko pro daného uživatele, včetně:

- V reálném čase riziko přihlášení
- Další rizika přihlašování
- Detekce rizikový uživatel.   

 

 
 ![Tok](./media/overview-v2/01.png)
 

 

Na obrázku výše je automaticky shrnutý směrného plánu flow pro Identity Protection rizikem detekce a reakce pro jakékoli dané přihlášení.  

 

 

 

## <a name="common-scenarios"></a>Obvyklé scénáře 

Pojďme se podívat na příklad Sarah, Zaměstnanec společnosti Contoso. 

1. Sarah se pokusí přihlásit k Exchangi Online z prohlížeče sítě Tor. Při přihlašování Azure AD detekuje v reálném čase rizikové události. 

2. Azure AD zjistí, že Sarah je přihlášení z anonymní IP adresy, aktivuje se úroveň střední riziko přihlášení. 

3. Sarah je vystaven výzvu k MFA, protože společnosti Contoso IT správce nakonfiguroval zásady podmíněného přístupu Identity Protection rizika přihlášení. Tato zásada vyžaduje vícefaktorové ověřování pro střední a vyšší riziko přihlášení. 

4. Sarah prochází vícefaktorové ověřování výzvy a má přístup k Exchangi Online a úroveň rizika uživatele na Sarah se nemění. 

Co se stalo na pozadí? Požadavek na přihlášení v prohlížeči Tor aktivuje v reálném čase rizika přihlašování ve službě Azure AD pro anonymní IP adresy. Po zpracování požadavku služby Azure AD použít zásady rizik přihlašování nakonfigurovat ve službě Identity Protection, protože úroveň rizika přihlášení Sarah pro splnění prahové hodnoty (střední). Protože Sarah měl dříve zaregistrovaný pro MFA, byla moct reagovat na a předat ověřovacím testem MFA. Její schopnost úspěšně ověřovacím testem MFA předat signál, aby Azure AD, že byla pravděpodobně oprávněné identity vlastníka a její úroveň rizika uživatele nezvyšuje. 


Ale co když Sarah nebyl jeden pokoušel se přihlásit? 

1. Škodlivý objekt actor s přihlašovacími údaji vaší Sarah pokusí přihlásit k účtu Exchange Online pro Sarah z prohlížeče Tor vzhledem k tomu, že se snaží skrýt jejich IP adresu. 

2. Azure AD zjistí, že pokus o přihlášení z anonymní IP adresy, aktivuje se v reálném čase rizika přihlášení. 

3. Škodlivý objekt actor je vystaven výzvu k MFA, protože správce společnosti Contoso IT nakonfigurované Identity Protection přihlášení riziko zásady podmíněného přístupu tak, aby vyžadovala vícefaktorové ověřování při střední a vyšší riziko přihlášení. 

4. Škodlivý objekt actor ověřovacím testem MFA se nezdaří a nemá přístup k účtu Sarah pro Exchange Online. 

5. Neúspěšné MFA řádku aktivuje rizikovou událost, aby se zaznamenávaly, vyvolání jeho Sarah uživatelského rizika pro budoucí přihlášení. 

Teď, když škodlivý objekt actor došlo k pokusu o přístup k účtu na Sarah, Podívejme se, co se stane při příštím Sarah se pokusí přihlásit. 

1. Sarah se pokusí přihlásit k Exchangi Online z Outlooku. Při přihlašování Azure AD detekuje v reálném čase rizikové události, stejně jako jakékoli předchozí uživatelského rizika. 

2. Azure AD nezjistí v reálném čase rizika přihlášení, ale zachytí vysokého uživatelského rizika z důvodu posledních rizikovou aktivitu v předchozí scénáře.  

3. Sarah je vystaven výzva k zadání hesla resetovat, protože společnosti Contoso na správce IT nakonfiguroval zásady rizik uživatelů Identity Protection vyžadovat změnu hesla při přihlášení uživatele s vysokým rizikem. 

4. Protože Sarah je zaregistrovaný pro samoobslužné resetování HESLA a vícefaktorové ověřování, které úspěšně obnoví své heslo. 

5. Resetovat své heslo, přihlašovací údaje pro Sarah už dojde k ohrožení a vrátí jeho identita do bezpečného stavu. 

6. Sarah na předchozí rizikové události jsou vyřešeny a její úroveň rizika uživatele je automaticky obnovit jako odpověď na zmírnění ohrožení zabezpečení přihlašovacích údajů. 

## <a name="how-do-i-configure-identity-protection"></a>Jak konfigurovat Identity Protection? 

Abyste mohli začít s Identity Protection, je nutné nejprve nakonfigurujte zásady rizik uživatelů a zásad rizika přihlašování. Jakmile tyto zásady jsou nakonfigurované a použít u testovací skupiny, můžete simulovat rizikových událostí, abyste pochopili, jakým způsobem bude odpovídat Identity Protection ve vašem prostředí. Následující rychlý start příručky obsahují návod o tom, jak nastavit výše uvedené zásady a testování ve vašem prostředí. 

 

Identity Protection podporuje 3 role ve službě Azure AD pro aktivity správy kolem vašeho nasazení: 

| Role | Můžete provést | Nelze provést. |
| --- | --- | --- |
| Globální správce | Úplný přístup k Identity Protection, připojit Identity Protection | |
| Správce zabezpečení | Úplný přístup k Identity Protection | Připojení Identity Protection, resetujte hesla pro uživatele |
| Čtenář zabezpečení | Přístup k Identity Protection jen pro čtení | Oprava uživatelů připojení Identity Protection, nakonfigurovat zásady, resetování hesel| 

Další podrobnosti najdete v tématu [přiřazení rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)

 
## <a name="licensing"></a>Licencování

>[!NOTE]
> Ve verzi public preview služby Identity Protection (Aktualizovat) pouze zákazníci Azure AD Premium P2 bude mít přístup k sestavě rizikových uživatelů a sestavy rizikových přihlášení.



| Schopnost | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/zdarma |
| --- | --- | --- | --- |
| Zásady rizik uživatelů | Ano | Ne | Ne |
| Zásady rizik přihlašování | Ano | Ne | Ne |
| Sestava o rizikových uživatelích | Úplný přístup | Omezené informace | Omezené informace |
| Sestava rizikových přihlášení | Úplný přístup | Omezené informace | Omezené informace |
| Zásady registrace MFA | Ano | Ne | Ne |







## <a name="next-steps"></a>Další postup 

Začínáme s Identity Protection, najdete v článku [zásady rizik přihlašování konfigurovat](quickstart-sign-in-risk-policy.md). 






