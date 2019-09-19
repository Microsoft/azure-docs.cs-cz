---
title: Vytvoření zásady podmíněného přístupu – Azure Active Directory
description: Jaké jsou všechny možnosti k dispozici pro vytvoření zásad podmíněného přístupu a co znamenají?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87f3e815f541ad4cfabc22d917ca9cecba47b50f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077604"
---
# <a name="building-a-conditional-access-policy"></a>Vytvoření zásady podmíněného přístupu

Jak je vysvětleno v článku [co je podmíněný přístup](overview.md), zásada podmíněného přístupu je příkaz if-then, který slouží k **přiřazování** a **řízení přístupu**. Zásada podmíněného přístupu přináší signály dohromady, provede rozhodnutí a vynutila zásady organizace.

Jak organizace vytváří tyto zásady? Co je potřeba?

![Podmíněný přístup (signály + rozhodnutí + vynucení = zásady)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>Přiřazení

Část přiřazení řídí, kdo, co a kde je zásada podmíněného přístupu.

### <a name="users-and-groups"></a>Uživatelé a skupiny

Uživatelé a skupiny přiřadí, kdo bude zásada zahrnovat nebo vyloučit. Toto přiřazení může zahrnovat všechny uživatele, konkrétní skupiny uživatelů, role adresáře nebo externí uživatele typu Host. 

### <a name="cloud-apps-or-actions"></a>Cloudové aplikace nebo akce

Cloudové aplikace nebo akce můžou zahrnovat nebo vyloučit cloudové aplikace nebo uživatelské akce, které budou platit pro tyto zásady.

### <a name="conditions"></a>Podmínky

Zásada může obsahovat více podmínek.

#### <a name="sign-in-risk"></a>Riziko přihlášení

Pro organizace, které mají [Azure AD Identity Protection](../identity-protection/overview.md), může detekce rizik ovlivnit vaše zásady podmíněného přístupu.

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

## <a name="access-controls"></a>Ovládací prvky přístupu

Část řízení přístupu v zásadách podmíněného přístupu řídí způsob, jakým se zásady vynutily.

### <a name="grant"></a>Udělení

#### <a name="block-access"></a>Blokovat přístup

Blok přístupu zablokuje přístup pouze v rámci zadaných přiřazení. Ovládací prvek blokování je výkonný a měl by být wielded s příslušným vědomím.

#### <a name="grant-access"></a>Udělit přístup

Řízení grant může aktivovat vynucení jednoho nebo více ovládacích prvků. 

- Vyžadovat Multi-Factor Authentication (Azure Multi-Factor Authentication)
- Vyžadovat, aby zařízení byla označená jako vyhovující (Intune)
- Vyžadovat zařízení připojené k hybridní službě Azure AD
- Vyžaduje se klientem schválená aplikace.
- Vyžadovat zásady ochrany aplikací

Správci se mohou rozhodnout, že budou vyžadovat jeden z předchozích ovládacích prvků nebo všechny vybrané ovládací prvky pomocí následujících možností. Výchozí nastavení pro více ovládacích prvků vyžaduje všechny.

- Vyžadovat všechny vybrané ovládací prvky (ovládací prvek a ovládací prvek)
- Vyžadovat jeden z vybraných ovládacích prvků (ovládací prvek nebo ovládací prvek)

### <a name="session"></a>Relace

Řízení relací může omezit prostředí 

- Používat omezení vynucená aplikací
   - V současné době funguje pouze s Exchange Online a SharePoint Online.
      - Předá informace o zařízení, aby bylo umožněno řízení přístupu na základě úplného nebo omezeného přístupu.
- Používat Řízení podmíněného přístupu k aplikacím
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
- **Řízení přístupu**
   - **Udělit** nebo **blokovat** ovládací prvky

![Prázdné zásady podmíněného přístupu](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

## <a name="next-steps"></a>Další postup

[Simulace chování při přihlašování pomocí nástroje pro What If podmíněného přístupu](troubleshoot-conditional-access-what-if.md)

[Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

[Plánování cloudového nasazení Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

[Správa dodržování předpisů zařízením pomocí Intune](https://docs.microsoft.com/intune/device-compliance-get-started)

[Microsoft Cloud App Security a podmíněný přístup](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
