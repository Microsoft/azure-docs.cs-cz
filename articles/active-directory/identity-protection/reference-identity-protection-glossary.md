---
title: Glosář ochrany identity služby Azure Active Directory
description: Glosář ochrany identity služby Azure Active Directory
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
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232348"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Glosář ochrany identity služby Azure Active Directory

### <a name="at-risk-user"></a>V ohrožení (Uživatel)
Uživatel s jedním nebo více aktivními detekcemi rizik. 

### <a name="atypical-sign-in-location"></a>Atypické přihlašovací umístění
Přihlášení ze zeměpisného umístění, které není typické pro konkrétního uživatele, podobné uživatele nebo klienta.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Modul zabezpečení služby Azure Active Directory, který poskytuje konsolidované zobrazení detekce rizik a potenciálních chyb zabezpečení ovlivňujících identitu organizace.

### <a name="conditional-access"></a>Podmíněný přístup
Zásady pro zabezpečení přístupu k prostředkům. Pravidla podmíněného přístupu jsou uložená ve službě Azure Active Directory a jsou vyhodnocována službou Azure AD před udělením přístupu k prostředku.  Mezi příklady pravidel patří omezení přístupu na základě umístění uživatele, stavu zařízení nebo metody ověřování uživatele.

### <a name="credentials"></a>Přihlašovací údaje
Informace, které zahrnují identifikaci a doklad totožnosti, který se používá k získání přístupu k místním a síťovým prostředkům. Příklady pověření jsou uživatelská jména a hesla, čipové karty a certifikáty.

### <a name="event"></a>Událost
Záznam aktivity ve službě Azure Active Directory.

### <a name="false-positive-risk-detection"></a>Falešně pozitivní (detekce rizika)
Stav detekce rizik nastavený ručně uživatelem ochrany identity, což znamená, že detekce rizika byla prošetřena a byla nesprávně označena jako detekce rizik.

### <a name="identity"></a>Identita
Osoba nebo subjekt, který musí být ověřen pomocí ověřování, na základě kritérií, jako je heslo nebo certifikát.

### <a name="identity-risk-detection"></a>Detekce rizika identity
Událost Azure AD, která byla označena jako neobvyklé podle identity protection a může znamenat, že identita byla ohrožena.

### <a name="ignored-risk-detection"></a>Ignorováno (detekce rizika)
Stav detekce rizik nastavený ručně uživatelem služby Identity Protection, který označuje, že zjišťování rizika je ukončeno bez nutnosti nápravy.

### <a name="impossible-travel-from-atypical-locations"></a>Nemožné cestování z atypických míst
Detekce rizika se spustí, když jsou zjištěna dvě přihlášení pro stejného uživatele, kde alespoň jeden z nich pochází z atypického místa přihlášení a kde doba mezi přihlášeními je kratší než minimální doba, kterou by bylo nutné fyzicky cestovat mezi těmito Umístění.  

### <a name="investigation"></a>Šetření
Proces kontroly aktivit, protokolů a dalších relevantních informací souvisejících s detekcí rizik, aby se rozhodlo, zda jsou nutné nápravné nebo zmírňující kroky, pochopit, zda a jak byla identita ohrožena, a pochopit, jak byla ohrožena identita byla použita.

### <a name="leaked-credentials"></a>Uniklá pověření
Detekce rizik a detekce se spustila, když naši výzkumní pracovníci našli veřejně zveřejněná aktuální uživatelská pověření (uživatelské jméno a heslo).

### <a name="mitigation"></a>Omezení rizik
Akce k omezení nebo vyloučení schopnosti útočníka zneužít ohroženou identitu nebo zařízení bez obnovení identity nebo zařízení do bezpečného stavu. Zmírnění nevyřeší předchozí detekce rizik a související s identitou nebo zařízením.

### <a name="multi-factor-authentication"></a>Ověřování pomocí služby Multi-Factor Authentication
Metoda ověřování, která vyžaduje dvě nebo více metod ověřování, které mohou zahrnovat něco, co má uživatel, takový certifikát; něco, co uživatel ví, jako jsou uživatelská jména, hesla nebo heslo; fyzické atributy, jako je například kryptografický otisk; osobních vlastností, jako je osobní podpis.

### <a name="offline-detection"></a>Offline detekce
Detekce anomálií a vyhodnocení rizika události, jako je například pokus o přihlášení po faktu, pro událost, která již proběhla.

### <a name="policy-condition"></a>Podmínka zásad
Část zásad zabezpečení, která definuje entity (skupiny, uživatele, aplikace, platformy zařízení, stavy zařízení, rozsahy IP adres, typy klientů), které jsou součástí zásady nebo z nich vyloučeny.

### <a name="policy-rule"></a>Pravidlo zásad
Část zásady zabezpečení, která popisuje okolnosti, které by spustily zásady a akce provedené při spuštění zásady.

### <a name="prevention"></a>Prevention (Prevence)
Akce, která zabrání poškození organizace zneužitím identity nebo zařízení, které má podezření nebo ví, že je ohroženo. Preventivní akce nezabezpečuje zařízení nebo identitu a neřeší předchozí detekce rizik.

### <a name="privileged-user"></a>Privilegované (uživatel)
Uživatel, který měl v době zjišťování rizik oprávnění k jednomu nebo více prostředkům správce ve službě Azure Active Directory, jako je globální správce, správce fakturace, správce služeb, správce uživatelů, správce uživatelů a heslo. Správce. 

### <a name="real-time"></a>Reálný čas
Viz detekce v reálném čase.

### <a name="real-time-detection"></a>Detekce v reálném čase
Detekce anomálií a vyhodnocení rizika události, jako je například pokus o přihlášení před událostí je povoleno pokračovat.

### <a name="remediated-risk-detection"></a>Opraveno (detekce rizik)
Stav detekce rizik nastavený automaticky službou Identity Protection, což znamená, že detekce rizika byla opravena pomocí standardní nápravné akce pro tento typ detekce rizik. Například při resetování hesla uživatele, mnoho detekce rizik, které označují, že předchozí heslo bylo ohroženo jsou automaticky opraveny.

### <a name="remediation"></a>Odstranění rizika
Akce k zabezpečení identity nebo zařízení, které byly dříve podezřelé nebo známo, že byla ohrožena. Nápravná akce obnoví identitu nebo zařízení do bezpečného stavu a řeší předchozí detekce rizik spojených s identitou nebo zařízením.

### <a name="resolved-risk-detection"></a>Vyřešeno (detekce rizik)
Stav detekce rizik nastavený ručně uživatelem ochrany identity, který označuje, že uživatel provedl příslušnou nápravnou akci mimo ochranu identity a že detekce rizika by měla být považována za uzavřenou.

### <a name="risk-detection-status"></a>Stav detekce rizik
Vlastnost detekce rizik, označující, zda je událost aktivní a pokud je uzavřena, důvod pro její uzavření.

### <a name="risk-detection-type"></a>Typ detekce rizik
Kategorie pro detekci rizik a označující typ anomálie, která způsobila, že událost byla považována za rizikovou.

### <a name="risk-level-risk-detection"></a>Úroveň rizika (detekce rizika)
Indikace (Vysoká, Střední nebo Nízká) závažnosti detekce rizik, která uživatelům ochrany identity pomáhá upřednostňovat akce, které propotřebují ke snížení rizika pro svou organizaci. 

### <a name="risk-level-sign-in"></a>Úroveň rizika (přihlášení)
Označení (Vysoká, Střední nebo Nízká) pravděpodobnost, že pro konkrétní přihlášení, někdo jiný se pokouší použít identitu uživatele.

### <a name="risk-level-user-compromise"></a>Úroveň rizika (kompromis uživatele)
Označení (Vysoká, Střední nebo Nízká) pravděpodobnost, že identita byla ohrožena.

### <a name="risk-level-vulnerability"></a>Úroveň rizika (zranitelnost)
Označení (vysoká, střední nebo nízká) závažnosti chyby zabezpečení, která uživatelům ochrany identity pomáhá upřednostňovat akce, které provází, aby snížili riziko pro svou organizaci.

### <a name="secure-identity"></a>Zabezpečeno (identita)
Provádějte nápravnou akci, jako je změna hesla nebo obnovení zpracování počítače, abyste obnovili potenciálně ohroženou identitu do nekompromisního stavu.

### <a name="security-policy"></a>Zásady zabezpečení
Kolekce pravidel zásad a podmínky. Zásadu lze použít na entity, jako jsou uživatelé, skupiny, aplikace, zařízení, platformy zařízení, stavy zařízení, rozsahy IP adres a typy klientů Auth2.0. Pokud je zásada povolena, vyhodnotí se vždy, když je entita zahrnutá v zásadě vydána jako token pro prostředek.

### <a name="sign-in-v"></a>Přihlášení (v)
Ověření identity ve službě Azure Active Directory.

### <a name="sign-in-n"></a>Přihlášení (n)
Proces nebo akce ověřování identity ve službě Azure Active Directory a událost, která zachycuje tuto operaci.

### <a name="sign-in-from-anonymous-ip-address"></a>Přihlášení z anonymní IP adresy
Zjišťování rizika se spustilo po úspěšném přihlášení z IP adresy, která byla identifikována jako anonymní ip adresa proxy.

### <a name="sign-in-from-infected-device"></a>Přihlášení z infikovaného zařízení
Zjišťování rizika se spustilo, když přihlášení pochází z IP adresy, o které je známo, že ji používá jedno nebo více ohrožených zařízení, která se aktivně pokoušejí komunikovat se serverem robota.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Přihlášení z IP adresy s podezřelou aktivitou
Detekce rizik a aktivaci po úspěšném přihlášení z IP adresy s vysokým počtem neúspěšných pokusů o přihlášení napříč více uživatelskými účty v krátkém časovém období.

### <a name="sign-in-from-unfamiliar-location"></a>Přihlášení z neznámého místa
Detekce rizika se spustí, když se uživatel úspěšně přihlásí z nového umístění (IP, Zeměpisná šířka/zeměpisná délka a ASN).

### <a name="sign-in-risk"></a>Riziko přihlášení
Viz Úroveň rizika (přihlášení)

### <a name="sign-in-risk-policy"></a>Zásady rizik pro přihlášení
Zásady podmíněného přístupu, které vyhodnocuje riziko pro konkrétní přihlášení a platí skutečnosti snižující závažnost rizika na základě předdefinovaných podmínek a pravidel.

### <a name="user-compromise-risk"></a>Ohrožení rizika pro uživatele
Viz Úroveň rizika (ohrožení zabezpečení uživatelem)

### <a name="user-risk"></a>Riziko uživatele
Viz Úroveň rizika (ohrožení zabezpečení uživatelem).

### <a name="user-risk-policy"></a>Zásady rizika pro uživatele
Zásady podmíněného přístupu, která bere v úvahu přihlášení a používá skutečnosti snižující závažnost rizika na základě předdefinovaných podmínek a pravidel.

### <a name="users-flagged-for-risk"></a>Uživatelé označení příznakem rizika
Uživatelé, kteří mají detekci rizik, kteří jsou aktivní nebo opraveni

### <a name="vulnerability"></a>Ohrožení zabezpečení
Konfigurace nebo podmínka ve službě Azure Active Directory, která umožňuje adresář imitace zneužití nebo hrozby.

## <a name="see-also"></a>Viz také

- [Ochrana identit služby Azure Active Directory](../active-directory-identityprotection.md)
