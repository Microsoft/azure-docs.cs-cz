---
title: Slovník ochrany identit Azure Active Directory | Microsoft Docs
description: Slovník ochrany identit Azure Active Directory
services: active-directory
keywords: ochrany identit Azure active directory, cloud app discovery,. Správa aplikací, zabezpečení, rizik, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení, Glosář
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 833119a5-33d6-4482-adda-fa35218c72c3
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: a9cc4a914091c91f4b2ae1c152644fe8e01747e8
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713387"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Slovník ochrany identit Azure Active Directory
### <a name="at-risk-user"></a>Riziko (uživatel)
Uživatel se jeden nebo více událostí active riziko. 

### <a name="atypical-sign-in-location"></a>Umístění netypických přihlášení
Přihlášení z zeměpisné polohy, který není typické pro konkrétního uživatele, podobně jako uživatele nebo klienta.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Modul zabezpečení služby Azure Active Directory, která poskytuje ucelený přehled o rizikových událostech a potenciální ohrožení zabezpečení, které ovlivňují identity organizace.

### <a name="conditional-access"></a>Podmíněný přístup
Zásadu zabezpečení přístupu k prostředkům. Pravidla podmíněného přístupu se ukládají ve službě Azure Active Directory a vyhodnocují se službou Azure Active Directory před udělením přístupu k prostředku.  Příklad pravidla zahrnují také omezení přístupu podle umístění uživatele, zařízení stavu nebo metodu ověřování uživatele.

### <a name="credentials"></a>Přihlašovací údaje
Informace, které zahrnují identifikace a ověření identifikaci, který slouží k získání přístupu k místním a síťovým prostředkům. Příkladem přihlašovací údaje jsou uživatelská jména a hesla, čipové karty a certifikáty.

### <a name="event"></a>Událost
Záznam aktivity v Azure Active Directory.

### <a name="false-positive-risk-event"></a>Falešně pozitivní (riziko událost)
Stav události riziko podle ochrany identit uživatelů, označující, že událost riziko byl prozkoumat a byla nesprávně označený jako událost riziko nastavit ručně.

### <a name="identity"></a>Identita
Osoba nebo entita, které se musí ověřit prostřednictvím ověřování, na základě kritérií, například heslo nebo certifikát.

### <a name="identity-risk-event"></a>Identity riziko událostí
AAD událost, která byla označena jako neobvyklé službou Identity Protection a může znamenat, že byl napaden identity.

### <a name="ignored-risk-event"></a>Ignorovat (riziko událost)
Stav události riziko podle ochrany identit uživatelů, která určuje, že událost riziko je uzavřena bez nutnosti převádět akci automatické nápravy nastavit ručně.

### <a name="impossible-travel-from-atypical-locations"></a>Neuskutečnitelná cesta z netypických míst
Událost riziko, aktivuje se při zjištění dvě přihlášení pro stejného uživatele, kde alespoň jeden z nich je z umístění netypických přihlášení, a čas mezi přihlášení je kratší než minimální hodnota času, které by byly třeba k fyzicky dostavit mezi těchto umístění.  

### <a name="investigation"></a>Šetření
Proces kontroly aktivity, protokoly a další důležité informace vztahující se k události riziko rozhodnout, zda jsou nutné, kroky ke zmírnění nebo nápravy porozumění, zda a jak identity došlo k ohrožení a pochopit použití ohroženými identity.

### <a name="leaked-credentials"></a>Uniklé přihlašovací údaje
Událost riziko, aktivuje, když přihlašovací údaje uživatele (uživatelské jméno a heslo) se nacházejí ve tmavý webu veřejně odeslány naše výzkumní pracovníci.

### <a name="mitigation"></a>Omezení rizik
Akci, kterou chcete omezit nebo odstranit schopnost útočník zneužít ohroženými identity nebo zařízení bez obnovení identity nebo zařízení do bezpečného stavu. Zmírnění nevyřeší předchozí rizikových událostech spojených s identity nebo zařízení.

### <a name="multi-factor-authentication"></a>Ověřování pomocí služby Multi-Factor Authentication
Metoda ověřování, která vyžaduje dva nebo více metod ověřování, které mohou zahrnovat něco, aby uživatel má tento certifikát; něco uživatel zná, například uživatelská jména, hesla či fráze průchodu; fyzické atributy, jako je například thumbprint; a osobní atributy, jako například osobní podpis.

### <a name="offline-detection"></a>Offline detekce
Detekce anomálií a vyhodnocení rizik na událost, jako je například pokus o přihlášení ve skutečnosti, událost, která již došlo.

### <a name="policy-condition"></a>Stav zásad
Část zásad zabezpečení, které definuje entity (skupiny uživatelů, aplikace, platformy zařízení, stavy zařízení, rozsahy IP adres, typů klientů) obsažena v zásadách nebo z něj vyloučeny.

### <a name="policy-rule"></a>Pravidlo zásad
Část zásad zabezpečení, která popisuje okolnosti, které by aktivovat zásady a akce prováděné při aktivaci zásady.

### <a name="prevention"></a>Prevention (Prevence)
Akci, která má nedošlo k poškození organizace prostřednictvím zneužití identity nebo zařízení by mohly vzbuzovat podezření nebo vědět, došlo k narušení. Prevence akce nezabezpečuje identity nebo zařízení a nevyřeší předchozí rizikových událostech.

### <a name="privileged-user"></a>Privilegovaný (uživatel)
Uživatel, který v době události riziko, měli oprávnění správce trvalé nebo dočasné na jeden nebo více prostředků v Azure Active Directory, jako je například globální správce, správce fakturace, Správce služby, Správce uživatelů a správce hesel. 

### <a name="real-time"></a>V reálném čase
V tématu detekce v reálném čase.

### <a name="real-time-detection"></a>Detekce v reálném čase
Detekce anomálií a vyhodnocení rizik na událost, jako je například pokus přihlášení před událostí může pokračovat.

### <a name="remediated-risk-event"></a>Opravené (riziko událost)
Stav události riziko automaticky nastavuje ochranu Identity, která určuje, že událost riziko nápravy pomocí standardní nápravy akce pro tento typ události riziko. Například když se resetuje heslo uživatele, mnoho událostí rizik, které označují, že došlo k ohrožení předchozí heslo napravují automaticky.

### <a name="remediation"></a>Náprava
Akce, abyste mohli zabezpečit identity nebo zařízení, které byly dříve by mohly vzbuzovat podezření nebo známé došlo k narušení. Akce nápravy obnoví identity nebo zařízení do bezpečného stavu a odstraňuje předchozí rizikových událostech spojených s identity nebo zařízení.

### <a name="resolved-risk-event"></a>Vyřešit (riziko událost)
Stav události riziko nastavit ručně uživatelem ochrany identit, indikující, že by uživatel provedl akci odpovídající nápravu mimo ochranu Identity a, měli byste zvážit událostí riziko uzavřít.

### <a name="risk-event-status"></a>Riziko stav události.
Vlastnosti události riziko, která udává, zda je událost aktivní a pokud zavřená, důvod zavřením.

### <a name="risk-event-type"></a>Typ rizikové události
Kategorie události riziko, označující typ anomálií, která způsobila, že události považovat za rizikové.

### <a name="risk-level-risk-event"></a>Úroveň rizika (riziko událost)
Označení (vysoká, střední nebo nízká) závažnosti události riziko pomoci uživatelům Identity Protection prioritu akce vstoupení pro snížení rizika pro jejich organizaci. 

### <a name="risk-level-sign-in"></a>Úroveň rizika (sign-in)
Uvedení (vysoká, střední nebo nízká) pravděpodobnost, že pro konkrétní sign-in, někdo se pokouší použít identitu uživatele.

### <a name="risk-level-user-compromise"></a>Úroveň rizika (ohrožení zabezpečení uživatele)
Uvedení (vysoká, střední nebo nízká) pravděpodobnost, že byl napaden identity.

### <a name="risk-level-vulnerability"></a>Úroveň rizika (chyba)
Uvedení (vysoká, střední nebo nízká) závažnost ohrožení zabezpečení, aby pomohla uživatelům Identity Protection prioritu akce vstoupení pro snížení rizika pro jejich organizaci.

### <a name="secure-identity"></a>Zabezpečení (identity)
Akce nápravy třeba změnit heslo nebo počítač obnovování obnovení potenciálně ohroženými identity neohrožený stavu.

### <a name="security-policy"></a>Zásady zabezpečení
Kolekce pravidel zásad a podmínku. Zásady je použít pro entity, jako jsou uživatelé, skupiny, aplikace, zařízení, platformy zařízení, stavy zařízení, rozsahy IP adres a Auth2.0 typů klientů. Pokud je povoleno zásadu, vyhodnotí se vždy, když je entita obsažena v zásadách vystaví token pro prostředek.

### <a name="sign-in-v"></a>Přihlaste se (v)
K ověření identity v Azure Active Directory.

### <a name="sign-in-n"></a>Přihlášení (ne)
Proces nebo akce ověřování identity v Azure Active Directory a událostí, který zachycuje tuto operaci.

### <a name="sign-in-from-anonymous-ip-address"></a>Přihlášení z anonymních IP adresy
Riziko událost se spustí po úspěšného přihlášení z IP adresy, které bylo zjištěno, že IP adresa anonymního proxy serveru.

### <a name="sign-in-from-infected-device"></a>Přihlášení z nakažených zařízení
Událost riziko, aktivuje, když přihlášení pochází z IP adresy, která se označuje použije jeden nebo více ohroženými zařízeními, které jsou aktivně pokouší komunikovat se serverem robota.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Přihlásit z IP adres s podezřelou aktivitou
Riziko události aktivované po úspěšného přihlášení z IP adres s vysoký počet neúspěšných pokusů o přihlášení několika uživatelským účtům během krátké doby času.

### <a name="sign-in-from-unfamiliar-location"></a>Přihlášení z neznámého umístění
Událost riziko, aktivuje, když se uživatel úspěšně přihlásí z nového místa (IP, zeměpisnou šířku a délku a ASN).

### <a name="sign-in-risk"></a>Riziko přihlášení
V tématu riziko úroveň (sign-in)

### <a name="sign-in-risk-policy"></a>Zásady rizik přihlašování
Zásady podmíněného přístupu, která vyhodnotí riziko pro konkrétní přihlášení a použije způsoby zmírnění rizik na základě předem definované podmínky a pravidla.

### <a name="user-compromise-risk"></a>Riziko ohrožení zabezpečení pro uživatele
V tématu riziko úroveň (ohrožení zabezpečení uživatele)

### <a name="user-risk"></a>Riziko uživatele
V tématu riziko úroveň (ohrožení zabezpečení uživatele).

### <a name="user-risk-policy"></a>Zásady rizik uživatelů
Zásady podmíněného přístupu, který zvažuje přihlášení a způsoby zmírnění rizik na základě předem definované podmínky a pravidla se vztahují.

### <a name="users-flagged-for-risk"></a>Uživatelé označení příznakem rizika
Uživatelé, kteří mají rizikových událostech, které jsou aktivní nebo napravených

### <a name="vulnerability"></a>Chyba zabezpečení
Konfigurace nebo podmínku v Azure Active Directory, takže adresáři náchylné k zneužití nebo hrozeb.

## <a name="see-also"></a>Další informace najdete v tématech
* [Ochrany identit Azure Active Directory](active-directory-identityprotection.md)

