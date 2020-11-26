---
title: Vytvoření zásady podmíněného přístupu – Azure Active Directory
description: Jaké jsou všechny možnosti k dispozici pro vytvoření zásad podmíněného přístupu a co znamenají?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28d58c476a805b672a6ec8b4d8ec465eba17e559
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169677"
---
# <a name="building-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Jak je vysvětleno v článku [co je podmíněný přístup](overview.md), zásada podmíněného přístupu je příkaz if-then, který slouží k **přiřazování** a **řízení přístupu**. Zásada podmíněného přístupu přináší signály dohromady, provede rozhodnutí a vynutila zásady organizace.

Jak organizace vytváří tyto zásady? Co je potřeba? Jak se používají?

![Podmíněný přístup (signály + rozhodnutí + vynucení = zásady)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

V každém okamžiku se může vztahovat na jednotlivé uživatele víc zásad podmíněného přístupu. V takovém případě musí být splněné všechny zásady, které platí. Pokud například jedna zásada vyžaduje vícefaktorové ověřování (MFA) a jiná vyžaduje vyhovující zařízení, je nutné provést ověřování MFA a použít vyhovující zařízení. Všechna přiřazení jsou logicky **ANDed**. Pokud máte nakonfigurované více než jedno přiřazení, musí být pro aktivaci zásady splněné všechna přiřazení.

Všechny zásady se vynutily ve dvou fázích:

- Fáze 1: shromáždění podrobností relace 
   - Shromážděte podrobnosti o relaci, jako je síťové umístění a identita zařízení, které budou nezbytné pro vyhodnocení zásad. 
   - Fáze 1 vyhodnocení zásad probíhá pro povolené zásady a zásady v [režimu pouze sestavy](concept-conditional-access-report-only.md).
- Fáze 2: vynucení 
   - Pomocí podrobností o relacích shromážděných ve fázi 1 identifikujte všechny požadavky, které nebyly splněny. 
   - Pokud máte zásadu, která je nakonfigurovaná tak, aby blokovala přístup, pomocí ovládacího prvku udělení bloku zastavte vynucení a uživatel se zablokuje. 
   - Uživatel bude vyzván k dokončení dalších požadavků na řízení udělení, které nebyly splněny během fáze 1 v následujícím pořadí, dokud nesplní zásady:  
      - Ověřování pomocí služby Multi-Factor Authentication 
      - Schválená klientská aplikace/zásada ochrany aplikací 
      - Spravované zařízení (s odpovídajícím nebo hybridním připojením k Azure AD) 
      - Podmínky použití 
      - Vlastní ovládací prvky  
   - Jakmile jsou všechny ovládací prvky grantu splněné, použijte ovládací prvky relace (vynutila aplikace, Microsoft Cloud App Security a životnost tokenu). 
   - Fáze 2 vyhodnocení zásad probíhá u všech povolených zásad. 

## <a name="assignments"></a>Přiřazení

Část přiřazení řídí, kdo, co a kde je zásada podmíněného přístupu.

### <a name="users-and-groups"></a>Uživatelé a skupiny

[Uživatelé a skupiny](concept-conditional-access-users-groups.md) přiřadí, kdo bude zásada zahrnovat nebo vyloučit. Toto přiřazení může zahrnovat všechny uživatele, konkrétní skupiny uživatelů, role adresáře nebo externí uživatele typu Host. 

### <a name="cloud-apps-or-actions"></a>Cloudové aplikace nebo akce

[Cloudové aplikace nebo akce](concept-conditional-access-cloud-apps.md) můžou zahrnovat nebo vyloučit cloudové aplikace nebo uživatelské akce, které budou platit pro tyto zásady.

### <a name="conditions"></a>Podmínky

Zásada může obsahovat více [podmínek](concept-conditional-access-conditions.md).

#### <a name="sign-in-risk"></a>Riziko přihlášení

Pro organizace, které mají [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md), může detekce rizik ovlivnit vaše zásady podmíněného přístupu.

#### <a name="device-platforms"></a>Platformy zařízení

Organizace s více platformami operačních systémů na zařízeních si můžou vymáhat konkrétní zásady na různých platformách. 

Informace, které se používají k výpočtu platformy zařízení, pocházejí z neověřených zdrojů, například řetězců uživatelských agentů, které je možné změnit.

#### <a name="locations"></a>Umístění

Data o poloze jsou poskytována daty geografického umístění IP. Správci se můžou rozhodnout definovat umístění a označit některé jako důvěryhodné pro síťová umístění organizace.

#### <a name="client-apps"></a>Klientské aplikace

Ve výchozím nastavení se zásady podmíněného přístupu vztahují na aplikace prohlížeče, mobilní aplikace a desktopové klienty, které podporují moderní ověřování. 

Tato podmínka přiřazení umožňuje zásadám podmíněného přístupu cílit na konkrétní klientské aplikace, které nepoužívají moderní ověřování. Mezi tyto aplikace patří klienti Exchange ActiveSync, starší aplikace Office, které nepoužívají moderní ověřování, a poštovní protokoly jako IMAP, MAPI, POP a SMTP.

#### <a name="device-state"></a>Stav zařízení

Tento ovládací prvek slouží k vyloučení zařízení, která jsou připojená k hybridní službě Azure AD, nebo označení kompatibilního v Intune. Toto vyloučení se dá udělat pro blokování nespravovaných zařízení. 

## <a name="access-controls"></a>Řízení přístupu

Část řízení přístupu v zásadách podmíněného přístupu řídí způsob, jakým se zásady vynutily.

### <a name="grant"></a>Oprávnění

[Udělení oprávnění](concept-conditional-access-grant.md) správcům poskytuje prostředky pro vynucení zásad, kde můžou zablokovat nebo udělit přístup.

#### <a name="block-access"></a>Blokování přístupu

Blok přístupu zablokuje přístup pouze v rámci zadaných přiřazení. Ovládací prvek blokování je výkonný a měl by být wielded s příslušným vědomím.

#### <a name="grant-access"></a>Udělení přístupu

Řízení grant může aktivovat vynucení jednoho nebo více ovládacích prvků. 

- Vyžadovat Multi-Factor Authentication (Azure AD Multi-Factor Authentication)
- Vyžadovat, aby zařízení byla označená jako vyhovující (Intune)
- Vyžadovat zařízení připojené k hybridní službě Azure AD
- Vyžadovat klientskou aplikaci schválenou
- Vyžadování zásad ochrany aplikací

Správci se mohou rozhodnout, že budou vyžadovat jeden z předchozích ovládacích prvků nebo všechny vybrané ovládací prvky pomocí následujících možností. Výchozí nastavení pro více ovládacích prvků vyžaduje všechny.

- Vyžadovat všechny vybrané ovládací prvky (ovládací prvek a ovládací prvek)
- Vyžadovat jeden z vybraných ovládacích prvků (ovládací prvek nebo ovládací prvek)

### <a name="session"></a>Relace

[Řízení relací](concept-conditional-access-session.md) může omezit prostředí 

- Použít omezení pro uplatnění aplikace
   - V současné době funguje pouze s Exchange Online a SharePoint Online.
      - Předá informace o zařízení, aby bylo umožněno řízení přístupu na základě úplného nebo omezeného přístupu.
- Použít Řízení podmíněného přístupu k aplikacím
   - Používá signály z Microsoft Cloud App Security k provádění akcí jako: 
      - Blokuje stahování, vyjmutí, kopírování a tisk citlivých dokumentů.
      - Monitorovat nebezpečné chování relace.
      - Vyžaduje označení citlivých souborů.
- Frekvence přihlášení
   - Možnost změnit výchozí frekvenci přihlašování pro moderní ověřování.
- Trvalá relace prohlížeče
   - Umožňuje uživatelům zůstat přihlášení po zavření a opětovném otevření okna prohlížeče.

## <a name="simple-policies"></a>Jednoduché zásady

Zásada podmíněného přístupu musí obsahovat minimálně následující podmínky, které se mají vyhovět:

- **Název** zásady
- **Přiřazení**
   - **Uživatelé a skupiny** , u kterých se má zásada použít.
   - **Cloudové aplikace nebo akce** , na které se má zásada uplatnit
- **Ovládací prvky přístupu**
   - **Udělit** nebo **blokovat** ovládací prvky

![Prázdné zásady podmíněného přístupu](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

V článku [společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md) jsou některé zásady, které považujeme za užitečné pro většinu organizací.

## <a name="next-steps"></a>Další kroky

[Vytvoření zásady podmíněného přístupu](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json#create-a-conditional-access-policy)

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)

[Plánování nasazení služby Azure AD založené na cloudu Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

[Správa dodržování předpisů zařízením pomocí Intune](/intune/device-compliance-get-started)

[Microsoft Cloud App Security a podmíněný přístup](/cloud-app-security/proxy-intro-aad)