---
title: Glosář Azure Active Directory Identity Protection
description: Glosář Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a17f52e519a84c58c9ed5b75259cc0b96d0a175
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88949170"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Glosář Azure Active Directory Identity Protection

### <a name="at-risk-user"></a>Ohroženo (uživatel)
Uživatel s jedním nebo více aktivními detekci rizik. 

### <a name="atypical-sign-in-location"></a>Neobvyklý přihlašovací umístění
Přihlášení z geografického umístění, které není typické pro konkrétního uživatele, podobné uživatele nebo tenanta.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Modul zabezpečení Azure Active Directory, který poskytuje konsolidovanou pohled na detekci rizik a potenciální ohrožení zabezpečení ovlivňující identity organizace.

### <a name="conditional-access"></a>Podmíněný přístup
Zásada pro zabezpečení přístupu k prostředkům. Pravidla podmíněného přístupu se ukládají v Azure Active Directory a před udělením přístupu k prostředku se vyhodnocují ve službě Azure AD.  Příklady pravidel zahrnují omezení přístupu na základě umístění uživatele, stavu zařízení nebo metody ověřování uživatelů.

### <a name="credentials"></a>Přihlašovací údaje
Informace, které zahrnují identifikaci a ověření identifikace, která se používá k získání přístupu k místním a síťovým prostředkům. Příklady přihlašovacích údajů jsou uživatelská jména a hesla, čipové karty a certifikáty.

### <a name="event"></a>Událost
Záznam aktivity v Azure Active Directory.

### <a name="false-positive-risk-detection"></a>Falešně pozitivní (zjišťování rizik)
Stav detekce rizik nastavený ručně uživatelem ochrany identity, což značí, že se zjistilo, že bylo detekováno riziko a bylo nesprávně označeno jako detekce rizika.

### <a name="identity"></a>Identita
Osoba nebo entita, která musí být ověřena pomocí ověřování, na základě kritérií, jako je například heslo nebo certifikát.

### <a name="identity-risk-detection"></a>Detekce rizik identity
Událost Azure AD, která byla označena jako neobvyklé podle Identity Protection, a může znamenat, že došlo k ohrožení identity.

### <a name="ignored-risk-detection"></a>Ignorováno (zjišťování rizik)
Stav detekce rizik nastavený ručně uživatelem ochrany identity, což znamená, že zjišťování rizik je uzavřeno bez provedení nápravné akce.

### <a name="impossible-travel-from-atypical-locations"></a>Nemožná cesta z netypických míst
Rozpoznání rizika aktivované při zjištění dvou přihlášení stejného uživatele, kde alespoň jedna z nich pochází z neobvyklých přihlašovacích umístění a kde je doba mezi přihlášeními kratší než minimální čas, který by vyžadoval fyzické cestování mezi těmito umístěními.  

### <a name="investigation"></a>Šetření
Proces kontroly aktivit, protokolů a dalších relevantních informací souvisejících s detekcí rizik pro rozhodování o tom, jestli je potřeba vyřešit a jak by se jednalo o ohrožení identity, a pochopit, jak se používala ohrožená identita.

### <a name="leaked-credentials"></a>Uniklé přihlašovací údaje
K detekci rizik aktivovanému v případě, že se aktuální přihlašovací údaje uživatele (uživatelské jméno a heslo) zveřejňují veřejně v tmavém webu našimi výzkumníky.

### <a name="mitigation"></a>Omezení rizik
Akce omezující nebo eliminující schopnost útočníka zneužít ohroženou identitu nebo zařízení bez obnovení identity nebo zařízení do bezpečného stavu. Zmírnění rizika neřeší předchozí zjištění rizik přidružená k identitě nebo zařízení.

### <a name="multi-factor-authentication"></a>Ověřování pomocí služby Multi-Factor Authentication
Metoda ověřování, která vyžaduje dvě nebo víc metod ověřování, které můžou zahrnovat něco, co uživatel má, třeba certifikát; text, který zná uživatel, například uživatelská jména, hesla nebo fráze Pass. fyzické atributy, jako je například kryptografický otisk; a osobní atributy, jako je osobní podpis.

### <a name="offline-detection"></a>Detekce offline
Detekci anomálií a hodnocení rizika události, jako je například pokus o přihlášení po faktu, pro událost, která již proběhla.

### <a name="policy-condition"></a>Podmínka zásady
Součást zásad zabezpečení, která definuje entity (skupiny, uživatele, aplikace, platformy zařízení, stavy zařízení, rozsahy IP adres, typy klientů) zahrnuté v zásadě nebo z ní vyloučené.

### <a name="policy-rule"></a>Pravidlo zásad
Část zásady zabezpečení, která popisuje okolnosti, které by tuto zásadu aktivovaly, a akce provedené při aktivaci zásady.

### <a name="prevention"></a>Prevention (Prevence)
Akce, která zabrání poškození v organizaci prostřednictvím zneužití identity nebo zařízení podezřelého z důvodu ohrožení bezpečnosti. Akce prevence nezabezpečuje zařízení nebo identitu a neřeší předchozí zjišťování rizik.

### <a name="privileged-user"></a>Privilegované (uživatel)
Uživatel, který v době detekce rizik má trvalá nebo dočasná oprávnění správce k jednomu nebo více prostředkům v Azure Active Directory, jako je globální správce, správce fakturace, správce služeb, Správce uživatelů a správce hesel. 

### <a name="real-time"></a>Reálný čas
Viz zjišťování v reálném čase.

### <a name="real-time-detection"></a>Zjišťování v reálném čase
Detekce anomálií a vyhodnocení rizika události, jako je například pokus o přihlášení, než bude moci událost pokračovat.

### <a name="remediated-risk-detection"></a>Opraveno (zjišťování rizik)
Stav detekce rizik nastavený automaticky pomocí Identity Protection, což znamená, že detekce rizik byla opravena pomocí standardní nápravné akce pro tento typ zjišťování rizik. Například při resetování hesla uživatele se automaticky opraví mnoho detekcí rizika, které ukazují, že předchozí heslo bylo ohroženo.

### <a name="remediation"></a>Náprava
Akce zabezpečující identitu nebo zařízení, které byly dříve podezřelé nebo známé jako ohrožené. Nápravná akce obnoví identitu nebo zařízení do bezpečného stavu a vyřeší předchozí zjištění rizik související s identitou nebo zařízením.

### <a name="resolved-risk-detection"></a>Vyřešené (zjišťování rizik)
Stav detekce rizik nastavený ručně uživatelem ochrany identity, který indikuje, že uživatel přijal příslušnou akci nápravy mimo ochranu identity a že by mělo být považováno za uzavřeno.

### <a name="risk-detection-status"></a>Stav detekce rizika
Vlastnost detekce rizika, která označuje, jestli je událost aktivní, a pokud je uzavřená, důvod uzavření.

### <a name="risk-detection-type"></a>Typ detekce rizika
Kategorie pro detekci rizik, která indikuje typ anomálie, která způsobila, že se událost považuje za rizikové.

### <a name="risk-level-risk-detection"></a>Úroveň rizika (detekce rizik)
Indikace (vysoká, střední nebo nízká) závažnost detekce rizik, která uživatelům pomůže chránit identity, přijímají akce, které přijmou, aby snížily riziko pro jejich organizaci. 

### <a name="risk-level-sign-in"></a>Úroveň rizika (přihlášení)
Indikace (vysoká, střední nebo nízká úroveň) pravděpodobnosti, že pro konkrétní přihlašování se někdo jiný pokouší použít identitu uživatele.

### <a name="risk-level-user-compromise"></a>Úroveň rizika (ohrožení zabezpečení uživatele)
Indikace (vysoká, střední nebo nízká úroveň) pravděpodobnosti ohrožení identity.

### <a name="risk-level-vulnerability"></a>Úroveň rizika (ohrožení zabezpečení)
Indikace (vysoká, střední nebo nízká) závažnost ohrožení zabezpečení, která uživatelům pomůže zajistit, aby se snížila rizika pro jejich organizaci.

### <a name="secure-identity"></a>Zabezpečení (identita)
Proveďte nápravné akce, jako je třeba Změna hesla nebo obnovení bitové kopie počítače, aby se obnovila potenciálně ohrožená identita v neohroženém stavu.

### <a name="security-policy"></a>Zásady zabezpečení
Kolekce pravidel a podmínek zásad Zásady je možné použít u entit, jako jsou uživatelé, skupiny, aplikace, zařízení, platformy zařízení, stavy zařízení, rozsahy IP adres a typy klientů ověřování 2.0. Pokud je zásada povolená, vyhodnotí se vždy, když je entita, která je součástí zásady, vystavena tokenu pro prostředek.

### <a name="sign-in-v"></a>Přihlásit (v)
Ověření identity v Azure Active Directory.

### <a name="sign-in-n"></a>Přihlášení (n)
Proces nebo akce ověřování identity v Azure Active Directory a událost, která zachycuje tuto operaci.

### <a name="sign-in-from-anonymous-ip-address"></a>Přihlášení z anonymní IP adresy
Rozpoznávání rizik aktivované po úspěšném přihlášení z IP adresy, která byla identifikována jako IP adresa anonymního proxy serveru.

### <a name="sign-in-from-infected-device"></a>Přihlášení z nakaženého zařízení
Zjišťování rizik aktivované při přihlášení, které pochází z IP adresy, která se používá pro jedno nebo více ohrožených zařízení, která se aktivně snaží o komunikaci se serverem bot.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Přihlášení z IP adresy s podezřelou aktivitou
Rozpoznávání rizik aktivované po úspěšném přihlášení z IP adresy s vysokým počtem neúspěšných pokusů o přihlášení v rámci několika uživatelských účtů za krátkou dobu.

### <a name="sign-in-from-unfamiliar-location"></a>Přihlášení z neznámého místa
Rozpoznání rizika aktivované v případě, že se uživatel úspěšně přihlásí z nového umístění (IP, zeměpisná šířka/zeměpisná délka a ASN).

### <a name="sign-in-risk"></a>Riziko přihlášení
Zobrazit úroveň rizika (přihlášení)

### <a name="sign-in-risk-policy"></a>Zásady rizik přihlašování
Zásada podmíněného přístupu, která vyhodnotí riziko pro konkrétní přihlášení a aplikuje omezení na základě předdefinovaných podmínek a pravidel.

### <a name="user-compromise-risk"></a>Riziko ohrožení uživatele
Zobrazit úroveň rizika (ohrožení zabezpečení uživatele)

### <a name="user-risk"></a>Riziko uživatele
Viz úroveň rizika (ohrožení zabezpečení uživatele).

### <a name="user-risk-policy"></a>Zásady rizik uživatelů
Zásady podmíněného přístupu, které přistupují k přihlášení a využívají omezení na základě předdefinovaných podmínek a pravidel.

### <a name="users-flagged-for-risk"></a>Uživatelé označení příznakem rizika
Uživatelé s detekcí rizik, které jsou buď aktivní, nebo opravené

### <a name="vulnerability"></a>Ohrožení zabezpečení
Konfigurace nebo podmínka v Azure Active Directory, což umožňuje, aby byl adresář vnímav k zneužití nebo hrozbám.

## <a name="see-also"></a>Viz také

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)