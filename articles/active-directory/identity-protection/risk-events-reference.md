---
title: Referenční informace o Azure Active Directory Identity Protection detekci rizik | Microsoft Docs
description: Odkaz na Azure Active Directory Identity Protection detekce rizik
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1005ac1853568222dea83a0f12293945825cf6d
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127578"
---
# <a name="azure-active-directory-identity-protection-risk-detections-reference"></a>Referenční informace o Azure Active Directory Identity Protection detekci rizik

Pokud útočníci získají přístup k prostředí tím, že ukrást identitu uživatele, dojde k převážné většině narušení zabezpečení. Zjišťování ohrožených identit není jednoduché. Azure Active Directory používá k detekci podezřelých akcí, které souvisejí s vašimi uživatelskými účty, algoritmus adaptivního strojového učení a heuristiky. Každá zjištěná podezřelá akce je uložená v záznamu s názvem zjišťování rizik.

## <a name="anonymous-ip-address"></a>Anonymní IP adresa

**Typ detekce:** V reálném čase  
**Starý název:** Přihlášení z anonymní IP adresy

Tento typ detekce rizika označuje přihlášení z anonymní IP adresy (například prohlížeč služby pro Anonymizer VPN).
Tyto IP adresy obvykle používají aktéri, kteří chtějí skrýt svou telemetrii přihlašování (IP adresa, umístění, zařízení atd.) pro potenciálně škodlivý záměr.

## <a name="atypical-travel"></a>Neobvyklá cesta

**Typ detekce:** Offline  
**Starý název:** Nemožná cesta do netypických míst

Tento typ detekce rizika identifikuje dvě přihlášení pocházející z geograficky vzdálených umístění, kde aspoň jedno z umístění může být pro uživatele neobvyklá, a to s ohledem na minulé chování. Kromě několika dalších faktorů tento algoritmus strojového učení bere v úvahu čas mezi dvěma přihlášeními a časem, kdy by uživatel musel přejít z prvního umístění do druhé, což značí, že jiný uživatel používá stejné. přihlašovací údaje.

Algoritmus ignoruje zjevné "falešně pozitivní", které přispívají k nemožným cestovním podmínkám, jako jsou sítě VPN a lokality pravidelně používané jinými uživateli v organizaci. Systém má počáteční období učení nejstarších 14 dní nebo 10 přihlášení, během kterých se učí chování přihlášení nového uživatele.

## <a name="leaked-credentials"></a>Nevrácená pověření

**Typ detekce:** Offline  
**Starý název:** Uživatelé s uniklými přihlašovacími údaji

Tento typ detekce rizika označuje, že došlo k úniku platných přihlašovacích údajů uživatele.
Pokud cybercriminals ohrozit platná hesla legitimních uživatelů, zločiny často tyto přihlašovací údaje sdílejí. To se obvykle provádí publikováním veřejně na tmavém webu nebo vložením webů nebo obchodováním nebo prodejem přihlašovacích údajů na černém trhu. Služba nevrácených přihlašovacích údajů Microsoftu získává páry uživatelského jména a hesla, protože sleduje veřejné a tmavé weby a pracuje s těmito službami:

- Výzkumní pracovníci
- Vynucování zákonů
- Týmy zabezpečení v Microsoftu
- Jiné důvěryhodné zdroje

Když služba získá přihlašovací údaje uživatele z tmavého webu, vloží weby nebo výše uvedené zdroje, porovná se s aktuálními platnými přihlašovacími údaji uživatelů Azure AD, aby našli platné shody.

## <a name="malware-linked-ip-address"></a>IP adresy související s malwarem

**Typ detekce:** Offline  
**Starý název:** Přihlášení z nakažených zařízení

Tento typ detekce rizika označuje přihlášení z IP adres nakažených malwarem, u kterých je známo, že aktivně komunikuje se serverem robot. To je určeno korelacemi IP adres zařízení uživatele proti IP adresám, které se nacházely v kontaktu se serverem robota, zatímco byl server robot aktivní.

## <a name="unfamiliar-sign-in-properties"></a>Neznámou vlastnost přihlášení

**Typ detekce:** V reálném čase  
**Starý název:** Přihlášení z neznámých míst

Tento typ detekce rizika považuje předchozí historii přihlášení (IP, zeměpisná šířka/délka a ASN), aby vyhledal neobvyklé přihlášení. Systém ukládá informace o předchozích umístěních používaných uživatelem a považuje se za "známá" umístění. Zjišťování rizik se aktivuje, když se přihlásí z umístění, které ještě není v seznamu známých umístění. Nově vytvoření uživatelé budou v "výukovém režimu" po určitou dobu, během které se neznámé detekce rizik při neznámých vlastnostech přihlášení vypne, zatímco se naše algoritmy budou učit chování uživatele. Doba trvání výukového režimu je dynamická a závisí na tom, kolik času má algoritmus získat dostatek informací o vzorech přihlášení uživatele. Minimální doba trvání je pět dní. Uživatel se může po dlouhou době nečinnosti vrátit do výukového režimu. Systém také ignoruje přihlášení ze známých zařízení a umístění, která jsou geograficky blízko známého umístění. 

Tuto detekci také spouštíme pro základní ověřování (nebo starší protokoly). Vzhledem k tomu, že tyto protokoly nemají moderní vlastnosti, jako je například ID klienta, je k dispozici omezená telemetrie k omezení falešně pozitivních hodnot. Zákazníkům doporučujeme, aby přešli na moderní ověřování.

## <a name="azure-ad-threat-intelligence"></a>Analýza hrozeb Azure AD

**Typ detekce:** Offline <br>
**Starý název:** Tato detekce se zobrazí ve starších sestavách Azure AD Identity Protection (uživatelé s označením rizika, detekce rizik) jako uživatelé s nevrácenými přihlašovacími údaji.

Tento typ detekce rizika označuje aktivitu uživatele, která je pro daného uživatele neobvyklá nebo je konzistentní se známými vzorci útoku na základě interních a externích zdrojů analýzy hrozeb Microsoftu.

## <a name="admin-confirmed-user-compromised"></a>Správce potvrdil napadení uživatele

**Typ detekce:** Offline <br>
Tato detekce indikuje, že správce v uživatelském rozhraní rizikové uživatele nebo pomocí rozhraní riskyUsers API vybral možnost potvrdit zneužití uživatele. Pokud chcete zjistit, který správce potvrdil ohrožení tohoto uživatele, Zkontrolujte historii rizika uživatele (prostřednictvím uživatelského rozhraní nebo rozhraní API).

## <a name="malicious-ip-address"></a>Škodlivá IP adresa

**Typ detekce:** Offline <br>
Tato detekce indikuje přihlášení ze škodlivé IP adresy. IP adresa je považována za škodlivou v závislosti na následujících příkladech:
-   Vysoké míry selhání (kvůli neplatným přihlašovacím údajům přijatým z IP adresy)
-   Další zdroje reputace IP

## <a name="additional-risk-detected"></a>Zjistilo se další riziko

**Typ detekce:** V reálném čase nebo offline <br>
Toto zjištění indikuje, že bylo zjištěno jedno z výše uvedených detekcí Premium. Vzhledem k tomu, že zjišťování úrovně Premium je viditelné jenom pro zákazníky Azure AD Premium P2, jsou pro zákazníky, kteří nejsou P2, "zjištěné" dodatečné riziko ".
