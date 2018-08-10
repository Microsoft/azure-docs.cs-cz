---
title: Slovník ochrany identit Azure Active Directory | Dokumentace Microsoftu
description: Slovník ochrany identit Azure Active Directory
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení, Glosář
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 833119a5-33d6-4482-adda-fa35218c72c3
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 3ea57ac39d48f51c7bbee1d149597c459fc5d547
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005510"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Slovník ochrany identit Azure Active Directory
### <a name="at-risk-user"></a>Riziko (uživatel)
Uživatel se nejmíň jeden aktivní rizikové události. 

### <a name="atypical-sign-in-location"></a>Neobvyklé přihlášení, místo
U přihlášení z zeměpisné umístění, která není typický pro konkrétního uživatele, podobně jako uživatele nebo tenanta.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
Modul zabezpečení služby Azure Active Directory, která poskytuje umožní získat přehled o rizikových událostech a potenciálních chybách zabezpečení dopadem na identity vaší organizace.

### <a name="conditional-access"></a>Podmíněný přístup
Zásady pro zabezpečení přístupu k prostředkům. Pravidla podmíněného přístupu se ukládají ve službě Azure Active Directory a jsou vyhodnocovány pomocí služby Azure AD před udělením přístupu k prostředku.  Příklad pravidla zahrnují také omezení přístupu na základě umístění uživatele, stav zařízení nebo metody ověřování uživatele.

### <a name="credentials"></a>Přihlašovací údaje
Informace, které zahrnuje identifikaci a ověření identifikace, která se používá k získání přístupu k místním a síťovým prostředkům. Příkladem přihlašovací údaje jsou uživatelská jména a hesla, čipové karty a certifikáty.

### <a name="event"></a>Událost
Záznam aktivity ve službě Azure Active Directory.

### <a name="false-positive-risk-event"></a>Falešně pozitivní (riziková událost)
Stav události rizika Identity Protection uživatelem, označující, že rizikové události se prověřit a byla nesprávně označena jako rizikovou událost, nastavit ručně.

### <a name="identity"></a>Identita
Osoba nebo entita, která musí být ověřen pomocí ověřování na základě kritérií, jako je například heslo nebo certifikát.

### <a name="identity-risk-event"></a>Události rizika identity
Událost AAD, která byla označena jako neobvyklé služba Identity Protection a může znamenat, že došlo k napadení o identitu.

### <a name="ignored-risk-event"></a>Ignoruje (riziková událost)
Stav události rizika Identity Protection uživatelem, která udává, že zavření rizikové události bez provedení nápravné akce nastavit ručně.

### <a name="impossible-travel-from-atypical-locations"></a>Neuskutečnitelná cesta od netypických míst
Riziková událost aktivuje, když se zjistí dvě přihlášení pro stejného uživatele, kde alespoň jeden z nich je z neobvyklé místo přihlášení, a doba mezi přihlášení je kratší než minimální hodnota času, které by to obnášelo fyzicky projít mezi tato místa.  

### <a name="investigation"></a>Šetření
Proces kontroly aktivity, protokoly a další relevantní informace související s riziková událost se rozhodnout, zda jsou nezbytné kroky ke zmírnění nebo nápravy pochopit, pokud a jak došlo k ohrožení identity a seznamte se s jak k ohrožení identity byl použit.

### <a name="leaked-credentials"></a>Uniklé přihlašovací údaje
Riziková událost aktivuje, když pověření aktuálního uživatele (uživatelské jméno a heslo) se nacházejí ve Dark web veřejně odeslány naše odborníky.

### <a name="mitigation"></a>Omezení rizik
Akce omezení nebo vyloučení schopnost útočníka zneužít ohrožení zabezpečení identity nebo zařízení bez obnovení identity nebo zařízení do bezpečného stavu. Omezení rizik nevyřeší předchozí rizikové události související s identity nebo zařízení.

### <a name="multi-factor-authentication"></a>Ověřování pomocí služby Multi-Factor Authentication
Metodu ověřování, dva nebo více metod ověřování, které mohou zahrnovat něco vyžaduje, aby uživatel má certifikátu; něco uživatel zná, například uživatelská jména, hesla nebo fráze pass; fyzické atributy, jako je například thumbprint; a osobní atributy, jako je například osobní podpis.

### <a name="offline-detection"></a>Offline zjišťování
Detekce anomálií a hodnocení rizik události, například pokus o přihlášení po jejich výskytu události, ke kterým už došlo.

### <a name="policy-condition"></a>Podmínky zásad
Část zásad zabezpečení, která definuje entity (skupin, uživatelé, aplikace, platformy zařízení, stavy zařízení, rozsahy IP adres, typy klientů) v zásadách zahrnuty nebo vyloučeny z něj.

### <a name="policy-rule"></a>Pravidlo zásad
Část zásad zabezpečení, která popisuje okolnosti, které aktivuje zásady a akce provede, když zásada se aktivuje.

### <a name="prevention"></a>Prevention (Prevence)
Akce pro organizaci prostřednictvím zneužití identity nebo zařízení nedošlo k poškození vzbuzovat podezření na nebo znát u něho ohrožena bezpečnost. Akce ochrany před únikem informací nezabezpečuje zařízení nebo identitu a neodstraní předchozí rizikové události.

### <a name="privileged-user"></a>Privilegovaný (uživatel)
Uživatel, který v době rizikové události, měl oprávnění správce trvalé nebo dočasné na jeden nebo více prostředků v Azure Active Directory, jako je například globální správce, správce fakturace, Správce služeb, Správce uživatelů a heslo správce. 

### <a name="real-time"></a>V reálném čase
V tématu detekce v reálném čase.

### <a name="real-time-detection"></a>Detekce v reálném čase
Detekce anomálií a hodnocení rizika, například pokus o přihlášení před událostí událost může pokračovat.

### <a name="remediated-risk-event"></a>Opravené (riziková událost)
Stav události rizika nastaví automaticky podle Identity Protection, označující, že rizikové události nápravy pomocí standardní nápravné akce pro tento typ rizikové události. Například pokud je resetování hesel uživateli, mnoho rizikové události, které označují, že došlo k napadení předchozí hesla napravují automaticky.

### <a name="remediation"></a>Náprava
Akci, kterou chcete zabezpečit identitu nebo zařízení, které byly dříve podezřelý nebo známé u něho ohrožena bezpečnost. Nápravné akce obnoví identity nebo zařízení do bezpečného stavu a odstraňuje předchozí rizikové události související s identity nebo zařízení.

### <a name="resolved-risk-event"></a>Vyřešeno (riziková událost)
Stav události rizika Identity Protection uživatel nastavit ručně, označující, že by uživatel provedl akci odpovídajících nápravných mimo Identity Protection. proto, že by měly považovat za riziková událost uzavřen.

### <a name="risk-event-status"></a>Stav události rizik
Vlastnost rizikovou událost, označující, zda je aktivní události a pokud zavřená, důvod zavření.

### <a name="risk-event-type"></a>Typ rizikové události
Kategorie pro rizikové události, určující typ anomálií, aby bylo považováno za riziková událost způsobila.

### <a name="risk-level-risk-event"></a>Úroveň rizika (riziková událost)
Označení (vysoká, střední nebo nízká) závažnost rizikové události, k poskytování pomoci uživatelům Identity Protection stanovit priority akce, které přebírají aby se snížilo riziko jejich organizace. 

### <a name="risk-level-sign-in"></a>Úroveň rizika (přihlášení)
Označení (vysoká, střední nebo nízká) pravděpodobnost, že pro u konkrétní přihlášení, někdo jiný se pokouší použít identitu uživatele.

### <a name="risk-level-user-compromise"></a>Úroveň rizika (ohrožení zabezpečení uživatele)
Označení (vysoká, střední nebo nízká) pravděpodobnost, že došlo k napadení o identitu.

### <a name="risk-level-vulnerability"></a>Úroveň rizika (chyba)
Označení (vysoká, střední nebo nízká) závažnost ohrožení zabezpečení, k poskytování pomoci uživatelům Identity Protection stanovit priority akce, které přebírají aby se snížilo riziko jejich organizace.

### <a name="secure-identity"></a>Zabezpečení (identity)
Proveďte nápravné akce, jako je změna hesla nebo počítač, obnovení potenciálně ohroženými identity neohrožený stavu obnovování z Image.

### <a name="security-policy"></a>Zásady zabezpečení
Kolekce pravidel zásad a podmínky. Zásadu lze použít k entitám, například uživatelé, skupiny, aplikace, zařízení, platformy zařízení, stavy zařízení, rozsahy IP adres a Auth2.0 typy klientů. Když je povolené zásady, vyhodnotí se pokaždé, když se entita obsažena v zásadách vystaví token pro prostředek.

### <a name="sign-in-v"></a>Přihlaste se (v)
K ověření identity ve službě Azure Active Directory.

### <a name="sign-in-n"></a>Přihlášení (n)
Proces nebo akce ověřování identity v Azure Active Directory a události, která zachycuje tuto operaci.

### <a name="sign-in-from-anonymous-ip-address"></a>Přihlášení z anonymní IP adresy
Riziková událost se aktivuje po u úspěšné přihlášení z IP adresy, která se identifikovala jako IP adresa anonymního proxy serveru.

### <a name="sign-in-from-infected-device"></a>Přihlášení z nakažených zařízení
Riziková událost aktivuje v případě, že přihlášení pochází z IP adresy, který je označován použije jeden nebo více Ohrožená zařízení, které jsou aktivně pokusu o komunikaci se serverem bot.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Přihlášení z IP adresy s podezřelou aktivitou
Riziková událost se aktivuje, když u úspěšné přihlášení z IP adres s vysokým počtem neúspěšných pokusů o přihlášení několika uživatelským účtům po krátkou dobu.

### <a name="sign-in-from-unfamiliar-location"></a>Přihlášení z neznámého umístění
Riziková událost aktivuje, když se uživatel úspěšně přihlásí z nového místa (IP, zeměpisné šířky a délky a číslo ASN).

### <a name="sign-in-risk"></a>Riziko přihlášení
Zobrazit riziko úroveň (přihlášení)

### <a name="sign-in-risk-policy"></a>Zásady rizik přihlašování
Zásady podmíněného přístupu, který vyhodnotí riziko pro konkrétní přihlášení a použije omezení rizik na základě předem definované podmínky a pravidla.

### <a name="user-compromise-risk"></a>Riziko ohrožení zabezpečení pro uživatele
Zobrazit riziko úroveň (ohrožení zabezpečení uživatele)

### <a name="user-risk"></a>Riziko uživatele
Zobrazit riziko úroveň (ohrožení zabezpečení uživatele).

### <a name="user-risk-policy"></a>Zásady rizik uživatelů
Zásady podmíněného přístupu, který bere v úvahu přihlášení a použije omezení rizik na základě předem definované podmínky a pravidla.

### <a name="users-flagged-for-risk"></a>Uživatelé označení příznakem rizika
Uživatelé, kteří mají rizikové události, které jsou aktivní nebo opravená

### <a name="vulnerability"></a>Chyba zabezpečení
Konfigurace nebo stav ve službě Azure Active Directory, náchylné k zneužití nebo hrozby díky adresáři.

## <a name="see-also"></a>Další informace najdete v tématech
* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

