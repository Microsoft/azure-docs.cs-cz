---
title: Co je Azure Active Directory Identity Protection (Aktualizováno)? | Dokumenty Microsoft
description: Co je Azure Active Directory Identity Protection (Aktualizováno)?
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 08/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3129027da0f28d9c89f7afe75d9531df9bae499e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125634"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>Co je Azure Active Directory Identity Protection (Aktualizováno)?

Prostředí ochrany identit bylo aktualizováno, aby lépe chránilo identity vaší organizace. Tento aktualizovaný zážitek nabízí:

- Předesignované prostředí pro správu, které se kolem rizika nejvíce hodí pro vaše riziko a riziko přihlašování uživatelů
- Výkonné prostředí pro vyšetřování s podporou pro filtrování, řazení a inteligentní stahování
- Vylepšené výpočty rizik uživatelů, které vám pomohou určit prioritu vašeho úsilí směrem k uživatelům, kteří jsou pravděpodobně napadeni.
- Nová podpora rozhraní API, která umožňuje programový přístup k rizikovým datům
- Zjednodušený proces zpětné vazby správců, který vám umožní okamžitě chránit vaše uživatele
- Nové pod dohledem strojového učení pro zlepšení přesnosti hodnocení rizik

Zabezpečení je v současnosti nejdůležitějším problémem pro organizace. Většina narušení zabezpečení probíhají, pokud útočníci získají přístup k prostředí tím, že odcizit identitu uživatele. V průběhu let se útočníci stále ještě více dostanou používat při využívání narušení třetích stran a používání sofistikovaných útoků phishing. Jakmile útočníci získají přístup i k uživatelským účtům s nízkou úrovní oprávnění, je poměrně snadné získat přístup k důležitým firemním prostředkům prostřednictvím bočního pohybu. 

K tomu, abyste na tyto hrozby odpověděli, vám Azure AD Identity Protection pomoct: 

- Proaktivní zabránění zneužití ohrožených identit 
- Automaticky zmírnit riziko při zjištění podezřelé aktivity 
- Prozkoumat rizikové uživatele a přihlášení a vyřešit potenciální ohrožení zabezpečení  
- Upozornění v případě, že riziko uživatele dosáhne zadané prahové hodnoty 

Azure AD Identity Protection je funkce Azure Active Directory Premium P2, která umožňuje nakonfigurovat zásady tak, aby automaticky reagovaly, když dojde k ohrožení identity uživatele nebo když se někdo jiný než vlastník účtu pokouší přihlásit pomocí svého odcizen. Tyto zásady, kromě dalších ovládacích prvků podmíněného přístupu poskytovaných službou Azure AD, můžou buď automaticky blokovat přístup nebo iniciovat zmírňující akce, jako je resetování hesla nebo vynucení vícefaktorového ověřování. Navíc poskytuje funkce pro monitorování a vytváření sestav, které umožňují získat hlubší přehled o rizikech a potenciálních ohroženích ve vaší organizaci. 

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWsS6Q]

## <a name="risk-detections"></a>Detekce rizik

Azure AD Identity Protection detekuje následující detekce rizik: 

| Typ detekce rizika | Popis | Typ detekce |
| --- | --- | --- |
| Neobvyklá cesta | Přihlaste se z neobvyklých míst na základě nedávných přihlášení uživatele. | Offline |
| Anonymní IP adresa | Přihlaste se z anonymní IP adresy (například: Prohlížeč pro Anonymizer, sítě VPN. | V reálném čase |
| Neznámou vlastnost přihlášení | Přihlaste se pomocí vlastností, které pro daného uživatele nevidíme nedávno. | V reálném čase |
| IP adresy související s malwarem | Přihlášení z IP adresy propojené s malwarem | Offline |
| Nevrácená pověření | Toto zjišťování rizik indikuje, že došlo k úniku platných přihlašovacích údajů uživatele. | Offline |

## <a name="types-of-risk"></a>Typy rizik 

Identity Protection vychází ze dvou typů rizika:

- Riziko přihlášení
- Riziko uživatele

### <a name="sign-in-risk"></a>Riziko přihlášení

Riziko přihlášení představuje pravděpodobnost, že daný požadavek na ověření není autorizovaný vlastníkem identity.

Existují dvě hodnocení rizika přihlašování: 

- **Riziko přihlášení (v reálném čase)** – riziko přihlášení (v reálném čase) vychází ze všech zjištění v reálném čase, které se aktivují během zpracování přihlášení.  
- **Riziko přihlašování (agregované)** – riziko přihlašování (agregované) představuje celkové riziko přihlášení. Počítá se podle modelu strojového učení, který je považován za:
   - Detekce v reálném čase (popsané výše)
   - Detekce offline (které se aktivují po přihlášení) 
   - Všechny ostatní funkce přihlášení

### <a name="user-risk"></a>Riziko uživatele

Riziko uživatele představuje pravděpodobnost, že daná identita je ohrožena. 

Riziko uživatele je vypočítáváno pomocí úvahy o všech rizicích spojených s uživatelem:

- Všechna riziková přihlášení
- Všechna zjišťování rizik, která nejsou propojená s přihlášením
- Riziko aktuálního uživatele
- Jakékoli akce týkající se nápravy nebo neúspěšných akcí provedených uživateli do dne

## <a name="how-identity-protection-detects-risk"></a>Jak Identity Protection detekuje riziko  

Azure AD využívá Machine Learning k detekci anomálií a podezřelých aktivit pomocí obou signálů zjištěných v reálném čase během přihlašování a také nereálné signály, které souvisejí s uživateli a jejich přihlašovacími aktivitami. Pomocí těchto dat aplikace Identity Protection vypočítá riziko přihlášení v reálném čase pokaždé, když se uživatel ověří, a určí celkovou úroveň rizika uživatele pro každého uživatele. Služba Identity Protection umožňuje automaticky provádět akce s těmito rozpoznáváními rizik konfigurací rizika uživatele ochrany identity a rizikových zásad pro přihlašování.  

Pro pochopení, jak Identity Protection detekuje riziko, existují dva důležité koncepty: riziko pro uživatele a riziko přihlášení. Riziko přihlašování odráží pravděpodobnost, že vlastník identity daný požadavek na ověření neautorizovaný. Existují dva typy rizik přihlašování: v reálném čase a celkem. V době daného pokusu o přihlášení se zjistilo riziko přihlášení v reálném čase (například přihlášení z anonymních IP adres). Celkové riziko přihlašování je agregované z zjištěných rizik při přihlašování v reálném čase a všech dalších zjišťování rizik, která nejsou v reálném čase přidružena k přihlašovacím údajům uživatele (například nemožným cestám). Riziko uživatele odráží celkovou pravděpodobnost, že špatný objekt actor ohrozí danou identitu. Riziko uživatele obsahuje všechny rizikové aktivity pro daného uživatele, včetně:

- Riziko přihlášení v reálném čase
- Další riziko při přihlašování
- Detekce rizikových uživatelů.   

 ![Tok](./media/overview-v2/01.png)

Základní tok pro detekci rizika ochrany identity a reakci pro každé dané přihlášení je shrnutý ve výše uvedeném obrázku.  

## <a name="common-scenarios"></a>Obvyklé scénáře 

Pojďme se podívat na příklad zaměstnance společnosti Contoso. 

1. Zaměstnanec se pokusí přihlásit k Exchangi Online z prohlížeče pro mandát. V době přihlášení Azure AD detekuje zjišťování rizik v reálném čase. 
2. Azure AD zjistí, že se zaměstnanec přihlašuje z anonymní IP adresy a aktivuje úroveň rizika pro přihlášení do střední úrovně. 
3. Pracovník je vyzván na výzvu MFA, protože správce IT společnosti Contoso nakonfiguroval zásady podmíněného přístupu Identity Protection. Zásady vyžadují MFA pro rizikové riziko střední nebo vyšší úrovně. 
4. Zaměstnanec předá výzvu MFA a přistupuje k Exchangi Online a jejich úroveň rizika uživatele se nemění. 

Co se stalo na pozadí? Při pokusu o přihlášení z prohlížeče pro mandát se aktivovalo riziko přihlášení v reálném čase v Azure AD pro anonymní IP adresu. Jelikož služba Azure AD zpracovala požadavek, používala zásady rizik přihlašování nakonfigurované v ochraně identity, protože úroveň rizika přihlašování zaměstnanců dosáhla prahové hodnoty (střední). Vzhledem k tomu, že se zaměstnanec dřív zaregistroval pro MFA, mohl odpovědět na výzvu MFA a předat ji. Jejich schopnost úspěšně předat výzvu MFA signalizaci službě Azure AD, že by se jednalo o legitimního vlastníka identity a jejich úroveň rizika uživatele se nezvyšuje. 

Ale co když se zaměstnanec nesnažil přihlásit? 

1. Škodlivý objekt actor s přihlašovacími údaji zaměstnance se pokusí přihlásit k účtu Exchange Online z prohlížeče služby mandát, protože se pokouší skrýt svou IP adresu. 
2. Azure AD zjistí, že pokus o přihlášení pochází z anonymní IP adresy, což aktivuje riziko přihlášení v reálném čase. 
3. Škodlivý objekt actor je výzvou na výzvu MFA, protože správce IT společnosti Contoso nakonfiguroval zásady podmíněného přístupu identity ochrany identity, aby vyžadoval MFA, pokud je riziko při přihlašování střední nebo vyšší. 
4. Škodlivý objekt actor neprojde výzvu MFA a nemůže získat přístup k účtu Exchange Online daného zaměstnance. 
5. Výzva k selhání MFA aktivovala detekci rizik, která se má zaznamenat, a zvyšuje riziko jejich uživatele pro budoucí přihlášení. 

Teď, když se škodlivý objekt actor pokusil získat přístup k účtu Sarah, se podívejme na to, co se stane při příštím pokusu o přihlášení zaměstnancem. 

1. Zaměstnanec se pokusí přihlásit k Exchangi Online z Outlooku. V době přihlášení služba Azure AD detekuje zjišťování rizik v reálném čase a také všechna předchozí rizika uživatele. 
2. Azure AD nedetekuje žádné riziko při přihlašování v reálném čase, ale detekuje vysoké riziko pro uživatele z důvodu poslední rizikové aktivity v předchozích scénářích.  
3. Zaměstnanec je vyzván na výzvu k resetování hesla, protože správce IT společnosti Contoso nakonfiguroval zásady rizik uživatele ochrany identity, aby vyžadoval změnu hesla, když uživatel s vysokým rizikovým protokolem. 
4. Vzhledem k tomu, že je zaměstnanec registrovaný pro SSPR a MFA, úspěšně resetuje heslo. 
5. Když resetujete své heslo, přihlašovací údaje zaměstnanců už nebudou ohrožené a jejich identita se vrátí do bezpečného stavu. 
6. Vyřešily se předchozí detekce rizik zaměstnanců a jejich úroveň rizika uživatele se automaticky resetuje jako odpověď na zmírnění ohrožení zabezpečení přihlašovacích údajů. 

## <a name="how-do-i-configure-identity-protection"></a>Návody konfiguraci Identity Protection? 

Pokud chcete začít s ochranou identity, nejdřív nakonfigurujte zásady rizik uživatelů a zásady rizik přihlašování. Jakmile tyto zásady nakonfigurujete a nasadíte na testovací skupinu, můžete simulovat detekci rizik a pochopit, jak bude ochrana identity ve vašem prostředí reagovat. Níže uvedené příručky pro rychlý Start poskytují návod, jak nastavit výše uvedené zásady a testovat ve vašem prostředí. 

Identity Protection podporuje tři role v Azure AD, aby vyrovnala aktivity správy v rámci vašeho nasazení: 

| Role | Může | Nelze provést |
| --- | --- | --- |
| Globální správce | Úplný přístup k Identity Protection a registraci Identity Protection | |
| Správce zabezpečení | Úplný přístup k Identity Protection | Zaregistrování Identity Protection, resetování hesel pro uživatele |
| Čtenář zabezpečení | Přístup k Identity Protection jen pro čtení | Zaregistrování Identity Protection, napravení uživatelů, konfigurace zásad, resetování hesel| 

Další informace najdete v tématu [přiřazení rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md) .
 
## <a name="licensing"></a>Licencování

>[!NOTE]
> Během veřejné verze Preview aplikace Identity Protection (aktualizované) budou mít přístup k sestavě rizikových uživatelů a rizikových přihlášení pouze Azure AD Premium zákazníci P2.

| Funkce | Podrobnosti | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/Free |
| --- | --- | --- | --- | --- |
| Rizikové zásady | Zásady rizik uživatelů (prostřednictvím Identity Protection) | Ano | Ne | Ne |
| Rizikové zásady | Zásady rizik přihlašování (přes Identity Protection nebo podmíněný přístup) | Ano | Ne | Ne |
| Sestavy zabezpečení | Přehled | Ano | Ne | Ne |
| Sestavy zabezpečení | Rizikové uživatele | Úplný přístup | Omezené informace | Omezené informace |
| Sestavy zabezpečení | Riziková přihlášení | Úplný přístup | Omezené informace | Omezené informace |
| Sestavy zabezpečení | Detekce rizik | Úplný přístup | Omezené informace | Ne |
| Oznámení | Upozornění na zjištěné ohrožené uživatele | Ano | Ne | Ne |
| Oznámení | Týdenní přehled | Ano | Ne | Ne |
| | Zásady registrace MFA | Ano | Ne | Ne |

## <a name="next-steps"></a>Další postup 

Informace o tom, jak začít s ochranou identity, najdete v tématu [Konfigurace zásad rizik přihlašování](quickstart-sign-in-risk-policy.md). 
