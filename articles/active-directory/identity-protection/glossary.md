---
title: Glosář Azure Active Directory Identity Protection | Microsoft Docs
description: Glosář Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6751fe74bfd9b8a07aec2263582d9f1a4bc0b2ac
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333984"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Glosář Azure Active Directory Identity Protection

### <a name="at-risk-user"></a>Ohroženo (uživatel)
Uživatel s jednou nebo více aktivními rizikovými událostmi. 

### <a name="atypical-sign-in-location"></a>Neobvyklý přihlašovací umístění
Přihlášení z geografického umístění, které není typické pro konkrétního uživatele, podobné uživatele nebo tenanta.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Modul zabezpečení Azure Active Directory, který poskytuje konsolidované zobrazení rizikových událostí a potenciálních ohrožení zabezpečení, které mají vliv na identity organizace.

### <a name="conditional-access"></a>Podmíněný přístup
Zásada pro zabezpečení přístupu k prostředkům. Pravidla podmíněného přístupu se ukládají v Azure Active Directory a před udělením přístupu k prostředku se vyhodnocují ve službě Azure AD.  Příklady pravidel zahrnují omezení přístupu na základě umístění uživatele, stavu zařízení nebo metody ověřování uživatelů.

### <a name="credentials"></a>Pověření
Informace, které zahrnují identifikaci a ověření identifikace, která se používá k získání přístupu k místním a síťovým prostředkům. Příklady přihlašovacích údajů jsou uživatelská jména a hesla, čipové karty a certifikáty.

### <a name="event"></a>Událost
Záznam aktivity v Azure Active Directory.

### <a name="false-positive-risk-event"></a>Falešně pozitivní (riziková událost)
Stav rizikové události nastaven ručně uživatelem ochrany identity, což znamená, že riziková událost byla prozkoumána a byla nesprávně označena jako riziková událost.

### <a name="identity"></a>Identita
Osoba nebo entita, která musí být ověřena pomocí ověřování, na základě kritérií, jako je například heslo nebo certifikát.

### <a name="identity-risk-event"></a>Událost rizikové identity
Událost AAD, která byla označena jako neobvyklé podle Identity Protection, může znamenat, že došlo k ohrožení identity.

### <a name="ignored-risk-event"></a>Ignorováno (riziková událost)
Stav rizikové události nastavené ručně uživatelem ochrany identity, což značí, že je riziková událost uzavřená bez provedení nápravné akce.

### <a name="impossible-travel-from-atypical-locations"></a>Nemožná cesta z netypických míst
Riziková událost aktivovaná při zjištění dvou přihlášení stejného uživatele, kde aspoň jedna z nich pochází z neobvyklých přihlašovacích umístění a kde je doba mezi přihlášeními kratší než minimální čas, který by vyžadoval fyzické cestování mezi těmito umístěními.  

### <a name="investigation"></a>Šetření
Proces kontroly aktivit, protokolů a dalších relevantních informací týkajících se rizikových událostí a rozhodování o tom, jestli jsou nezbytné kroky pro nápravu nebo zmírnění potíží, zjistěte, jestli a jak byla identita ohrožená, a porozumět tomu, jak ohrožená identita bylo použito.

### <a name="leaked-credentials"></a>Uniklé přihlašovací údaje
Riziková událost aktivovaná v případě, že jsou aktuální přihlašovací údaje uživatele (uživatelské jméno a heslo) na tmavém webu naši výzkumníki k dispozici.

### <a name="mitigation"></a>Omezení rizik
Akce omezující nebo eliminující schopnost útočníka zneužít ohroženou identitu nebo zařízení bez obnovení identity nebo zařízení do bezpečného stavu. Zmírnění rizika neřeší předchozí rizikové události přidružené k identitě nebo zařízení.

### <a name="multi-factor-authentication"></a>Vícefaktorové ověřování
Metoda ověřování, která vyžaduje dvě nebo víc metod ověřování, které můžou zahrnovat něco, co uživatel má, třeba certifikát; text, který zná uživatel, například uživatelská jména, hesla nebo fráze Pass. fyzické atributy, jako je například kryptografický otisk; a osobní atributy, jako je osobní podpis.

### <a name="offline-detection"></a>Detekce offline
Detekci anomálií a hodnocení rizika události, jako je například pokus o přihlášení po faktu, pro událost, která již proběhla.

### <a name="policy-condition"></a>Podmínka zásady
Součást zásad zabezpečení, která definuje entity (skupiny, uživatele, aplikace, platformy zařízení, stavy zařízení, rozsahy IP adres, typy klientů) zahrnuté v zásadě nebo z ní vyloučené.

### <a name="policy-rule"></a>Pravidlo zásad
Část zásady zabezpečení, která popisuje okolnosti, které by tuto zásadu aktivovaly, a akce provedené při aktivaci zásady.

### <a name="prevention"></a>Prevence
Akce, která zabrání poškození v organizaci prostřednictvím zneužití identity nebo zařízení podezřelého z důvodu ohrožení bezpečnosti. Akce prevence nezabezpečuje zařízení nebo identitu a neřeší předchozí rizikové události.

### <a name="privileged-user"></a>Privilegované (uživatel)
Uživatel, který je v okamžiku rizikové události, měl trvalá nebo dočasná oprávnění správce k jednomu nebo více prostředkům v Azure Active Directory, jako je globální správce, správce fakturace, správce služeb, Správce uživatelů a správce hesel. 

### <a name="real-time"></a>V reálném čase
Viz zjišťování v reálném čase.

### <a name="real-time-detection"></a>Zjišťování v reálném čase
Detekce anomálií a vyhodnocení rizika události, jako je například pokus o přihlášení, než bude moci událost pokračovat.

### <a name="remediated-risk-event"></a>Opraveno (riziková událost)
Stav rizikové události, který je automaticky nastaven pomocí Identity Protection, což značí, že riziková událost byla opravena pomocí standardní nápravné akce pro tento typ rizikové události. Pokud je například resetování hesla uživatele, mnoho rizikových událostí, které signalizují, že předchozí heslo bylo ohroženo, bude automaticky opraveno.

### <a name="remediation"></a>Náprava
Akce zabezpečující identitu nebo zařízení, které byly dříve podezřelé nebo známé jako ohrožené. Nápravná akce obnoví identitu nebo zařízení do bezpečného stavu a vyřeší předchozí rizikové události přidružené k identitě nebo zařízení.

### <a name="resolved-risk-event"></a>Vyřešené (riziková událost)
Stav rizikové události nastavený ručně uživatelem ochrany identity, který indikuje, že uživatel trval příslušnou akci nápravy mimo ochranu identity a že by měla být tato riziková událost považována za uzavřenou.

### <a name="risk-event-status"></a>Stav rizikové události
Vlastnost rizikové události, která označuje, jestli je událost aktivní, a pokud je uzavřená, důvod uzavření.

### <a name="risk-event-type"></a>Typ rizikové události
Kategorie pro rizikovou událost, která označuje typ anomálie, která způsobila, že se událost považuje za rizikové.

### <a name="risk-level-risk-event"></a>Úroveň rizika (riziková událost)
Indikace (vysoká, střední nebo nízká úroveň) závažnosti rizikové události, která pomůže uživatelům ochrany totožnosti určit, jaké akce mají snížit riziko pro jejich organizaci. 

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
Riziková událost aktivovaná po úspěšném přihlášení z IP adresy, která byla identifikována jako IP adresa anonymního proxy serveru.

### <a name="sign-in-from-infected-device"></a>Přihlášení z nakaženého zařízení
Riziková událost aktivovaná při přihlášení pocházející z IP adresy, která se používá pro jedno nebo více ohrožených zařízení, která se aktivně pokouší komunikovat se serverem bot.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Přihlášení z IP adresy s podezřelou aktivitou
Riziková událost aktivovaná po úspěšném přihlášení z IP adresy s vysokým počtem neúspěšných pokusů o přihlášení v rámci několika uživatelských účtů za krátkou dobu.

### <a name="sign-in-from-unfamiliar-location"></a>Přihlášení z neznámého místa
Riziková událost aktivovaná v případě, že se uživatel úspěšně přihlásí z nového umístění (IP, zeměpisná šířka/zeměpisná délka a ASN).

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
Uživatelé, kteří mají rizikové události, které jsou buď aktivní, nebo opravené

### <a name="vulnerability"></a>Chyba zabezpečení
Konfigurace nebo podmínka v Azure Active Directory, což umožňuje, aby byl adresář vnímav k zneužití nebo hrozbám.

## <a name="see-also"></a>Viz také:
* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
