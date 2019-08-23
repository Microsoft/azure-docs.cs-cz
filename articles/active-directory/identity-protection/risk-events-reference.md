---
title: Referenční informace o rizikových událostech Azure Active Directory Identity Protection | Microsoft Docs
description: Azure Active Directory Identity Protection odkaz na rizikové události
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
ms.openlocfilehash: 5d2247aab872a71f250bd0b4b52714e402d2102d
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905202"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Přehled Azure Active Directory Identity Protection rizikových událostí

Pokud útočníci získají přístup k prostředí tím, že ukrást identitu uživatele, dojde k převážné většině narušení zabezpečení. Zjišťování ohrožených identit není jednoduché. Azure Active Directory používá k detekci podezřelých akcí, které souvisejí s vašimi uživatelskými účty, algoritmus adaptivního strojového učení a heuristiky. Každá zjištěná podezřelá akce je uložená v záznamu s názvem riziková událost.

## <a name="anonymous-ip-address"></a>Anonymní IP adresa

**Typ detekce:** V reálném čase  
**Starý název:** Přihlášení z anonymní IP adresy

Tento typ rizikové události označuje přihlášení z anonymní IP adresy (například prohlížeč služby pro Anonymizer VPN).
Tyto IP adresy obvykle používají aktéri, kteří chtějí skrýt svou telemetrii přihlašování (IP adresa, umístění, zařízení atd.) pro potenciálně škodlivý záměr.

## <a name="atypical-travel"></a>Neobvyklá cesta

**Typ detekce:** Offline  
**Starý název:** Nemožná cesta do netypických míst

Tento typ rizikové události identifikuje dvě přihlášení pocházející z geograficky vzdálených umístění, kde aspoň jedno z umístění může být pro uživatele neobvyklá, a to s ohledem na minulé chování. Kromě několika dalších faktorů tento algoritmus strojového učení bere v úvahu čas mezi dvěma přihlášeními a časem, kdy by uživatel musel přejít z prvního umístění do druhé, což značí, že jiný uživatel používá stejné. přihlašovací údaje.

Algoritmus ignoruje zjevné "falešně pozitivní", které přispívají k nemožným cestovním podmínkám, jako jsou sítě VPN a lokality pravidelně používané jinými uživateli v organizaci. Systém má počáteční období učení nejstarších 14 dní nebo 10 přihlášení, během kterých se učí chování přihlášení nového uživatele.

## <a name="leaked-credentials"></a>Nevrácená pověření

**Typ detekce:** Offline  
**Starý název:** Uživatelé s uniklými přihlašovacími údaji

Tento typ rizikové události indikuje, že byly Nevráceny platné přihlašovací údaje uživatele.
Pokud cybercriminals ohrozit platná hesla legitimních uživatelů, zločiny často tyto přihlašovací údaje sdílejí. To se obvykle provádí publikováním veřejně na tmavém webu nebo vložením webů nebo obchodováním nebo prodejem přihlašovacích údajů na černém trhu. Služba nevrácených přihlašovacích údajů Microsoftu získává páry uživatelského jména a hesla, protože sleduje veřejné a tmavé weby a pracuje s těmito službami:

- Výzkumní pracovníci
- Vynucování zákonů
- Týmy zabezpečení v Microsoftu
- Jiné důvěryhodné zdroje

Když služba získá přihlašovací údaje uživatele z tmavého webu, vloží weby nebo výše uvedené zdroje, porovná se s aktuálními platnými přihlašovacími údaji uživatelů Azure AD, aby našli platné shody.

## <a name="malware-linked-ip-address"></a>IP adresy související s malwarem

**Typ detekce:** Offline  
**Starý název:** Přihlášení z nakažených zařízení

Tento typ rizikové události indikuje přihlášení z IP adres nakažených malwarem, u kterých je známo, že aktivně komunikuje se serverem robot. To je určeno korelacemi IP adres zařízení uživatele proti IP adresám, které se nacházely v kontaktu se serverem robota, zatímco byl server robot aktivní.

## <a name="unfamiliar-sign-in-properties"></a>Neznámou vlastnost přihlášení

**Typ detekce:** V reálném čase  
**Starý název:** Přihlášení z neznámých míst

Tento typ rizikové události zvažuje minulou historii přihlášení (IP, zeměpisná šířka/délka a ASN), kde se hledají neobvyklé přihlášení. Systém ukládá informace o předchozích umístěních používaných uživatelem a považuje se za "známá" umístění. Riziková událost se aktivuje, když se přihlásí z umístění, které ještě není v seznamu známých umístění. Nově vytváře uživatelé budou v "výukovém režimu" po určitou dobu, během které se neznámé rizikové vlastnosti přihlašování vypnou, zatímco se naše algoritmy učí chování uživatele. Doba trvání výukového režimu je dynamická a závisí na tom, kolik času má algoritmus získat dostatek informací o vzorech přihlášení uživatele. Minimální doba trvání je pět dní. Uživatel se může po dlouhou době nečinnosti vrátit do výukového režimu. Systém také ignoruje přihlášení ze známých zařízení a umístění, která jsou geograficky blízko známého umístění. 

Tuto detekci také spouštíme pro základní ověřování (nebo starší protokoly). Vzhledem k tomu, že tyto protokoly nemají moderní vlastnosti, jako je například ID klienta, je k dispozici omezená telemetrie k omezení falešně pozitivních hodnot. Zákazníkům doporučujeme, aby přešli na moderní ověřování.

## <a name="azure-ad-threat-intelligence"></a>Analýza hrozeb Azure AD

**Typ detekce:** Offline <br>
**Starý název:** Tato detekce se zobrazí ve starších verzích Azure AD Identity Protection (uživatelé s příznakem rizika, rizikové události) jako uživatelé s nevrácenými přihlašovacími údaji.

Tento typ rizikové události označuje aktivitu uživatele, která je pro daného uživatele neobvyklá, nebo je konzistentní se známými vzory útoku na základě interních a externích zdrojů analýzy hrozeb Microsoftu.

## <a name="admin-confirmed-user-compromised"></a>Správce potvrdil napadení uživatele

**Typ detekce:** Offline <br>
Tato detekce indikuje, že správce v uživatelském rozhraní rizikové uživatele nebo pomocí rozhraní riskyUsers API vybral možnost potvrdit zneužití uživatele. Pokud chcete zjistit, který správce potvrdil ohrožení tohoto uživatele, Zkontrolujte historii rizika uživatele (prostřednictvím uživatelského rozhraní nebo rozhraní API).
