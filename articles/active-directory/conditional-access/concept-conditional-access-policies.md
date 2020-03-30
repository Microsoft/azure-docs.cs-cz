---
title: Vytváření zásad podmíněného přístupu – Služba Azure Active Directory
description: Jaké jsou všechny možnosti, které jsou k dispozici pro vytvoření zásad podmíněného přístupu a co znamenají?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 907ad8705742e4b2e38b13c3c675ebd333bd27d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295329"
---
# <a name="building-a-conditional-access-policy"></a>Vytváření zásad podmíněného přístupu

Jak je vysvětleno v článku [Co je podmíněný přístup](overview.md), zásada podmíněného přístupu je příkaz if-then ovládacích prvků **přiřazení** a **přístupu**. Zásady podmíněného přístupu spojuje signály, činí rozhodnutí a vynucují zásady organizace.

Jak organizace tyto zásady vytváří? Co je požadováno?

![Podmíněný přístup (signály + rozhodnutí + prosazování = zásady)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>Přiřazení

Část přiřazení určuje, kdo, co a kde zásady podmíněného přístupu.

### <a name="users-and-groups"></a>Uživatelé a skupiny

[Uživatelé a skupiny](concept-conditional-access-users-groups.md) přiřazují, koho bude zásada obsahovat nebo vyloučit. Toto přiřazení může zahrnovat všechny uživatele, určité skupiny uživatelů, role adresáře nebo externí uživatele typu Host. 

### <a name="cloud-apps-or-actions"></a>Cloudové aplikace nebo akce

[Cloudové aplikace nebo akce](concept-conditional-access-cloud-apps.md) mohou zahrnovat nebo vyloučit cloudové aplikace nebo akce uživatelů, které budou podléhat zásadám.

### <a name="conditions"></a>Podmínky

Zásada může obsahovat více [podmínek](concept-conditional-access-conditions.md).

#### <a name="sign-in-risk"></a>Riziko přihlášení

Pro organizace s [Azure AD Identity Protection](../identity-protection/overview.md), detekce rizik generované tam může ovlivnit zásady podmíněného přístupu.

#### <a name="device-platforms"></a>Platformy zařízení

Organizace s více platformami operačního systému zařízení mohou chtít vynutit konkrétní zásady na různých platformách. 

Informace použité k výpočtu platformy zařízení pochází z neověřených zdrojů, jako jsou řetězce uživatelského agenta, které lze změnit.

#### <a name="locations"></a>Umístění

Údaje o poloze jsou poskytovány geolokačními údaji IP. Správci se mohou rozhodnout definovat umístění a označit některá jako důvěryhodná, jako jsou místa v síti jejich organizace.

#### <a name="client-apps"></a>Klientské aplikace

Ve výchozím nastavení se zásady podmíněného přístupu vztahují na aplikace prohlížeče, mobilní aplikace a klienty klasické pracovní plochy, které podporují moderní ověřování. 

Tato podmínka přiřazení umožňuje zásadám podmíněného přístupu cílit na konkrétní klientské aplikace, které nepoužívají moderní ověřování. Mezi tyto aplikace patří klienti Exchange ActiveSync, starší aplikace Office, které nepoužívají moderní ověřování, a poštovní protokoly, jako je IMAP, MAPI, POP a SMTP.

#### <a name="device-state"></a>Stav zařízení

Tento ovládací prvek se používá k vyloučení zařízení, která jsou hybridní Azure AD připojen nebo označené kompatibilní v Intune. Toto vyloučení lze provést k zablokování nespravovaných zařízení. 

## <a name="access-controls"></a>Řízení přístupu

Přístup řídí část zásad podmíněného přístupu řídí, jak je zásada vynucena.

### <a name="grant"></a>Oprávnění

[Grant](concept-conditional-access-grant.md) poskytuje správcům prostředky pro vynucení zásad, kde mohou blokovat nebo udělit přístup.

#### <a name="block-access"></a>Blokovat přístup

Blokový přístup dělá právě to, že bude blokovat přístup v rámci zadaných přiřazení. Řízení bloku je silné a mělo by být ovládáno odpovídajícími znalostmi.

#### <a name="grant-access"></a>Udělení přístupu

Grantový ovládací prvek může aktivovat vynucení jednoho nebo více ovládacích prvků. 

- Vyžadovat vícefaktorové ověřování (Azure Multi-Factor Authentication)
- Vyžadovat, aby zařízení bylo označeno jako vyhovující (Intune)
- Vyžadovat hybridní zařízení azure ad připojeno
- Vyžadovat schválenou klientskou aplikaci
- Vyžadování zásad ochrany aplikací

Správci se mohou rozhodnout vyžadovat jeden z předchozích nebo všech vybraných ovládacích prvků pomocí následujících možností. Výchozí hodnota pro více ovládacích prvků je vyžadovat všechny.

- Vyžadovat všechny vybrané ovládací prvky (řízení a řízení)
- Vyžadovat jeden z vybraných ovládacích prvků (ovládací prvek nebo ovládací prvek)

### <a name="session"></a>Relace

[Ovládací prvky relace](concept-conditional-access-session.md) mohou omezit prostředí 

- Použití omezení vynucených aplikací
   - Momentálně funguje jen se Exchangem Online a SharePointem Online.
      - Předává informace o zařízení, které umožňují řídit prostředí udělující úplný nebo omezený přístup.
- Použití řízení aplikací podmíněného přístupu
   - Používá signály z Microsoft Cloud App Security k například k: 
      - Blokovat stahování, vyjmutí, kopírování a tisk citlivých dokumentů.
      - Sledujte rizikové chování relace.
      - Vyžadovat označování citlivých souborů.
- Frekvence přihlášení
   - Možnost změnit výchozí frekvenci přihlášení pro moderní ověřování.
- Trvalá relace prohlížeče
   - Umožňuje uživatelům zůstat přihlášeni po zavření a znovuotevření okna prohlížeče.

## <a name="simple-policies"></a>Jednoduché zásady

Zásady podmíněného přístupu musí obsahovat minimálně následující, aby byly vynuceny:

- **Název** zásady.
- **Přiřazení**
   - **Uživatelé nebo skupiny,** na které se mají zásady použít.
   - **Cloudové aplikace nebo akce,** na které se mají zásady uplatňovat.
- **Řízení přístupu**
   - **Udělit** nebo **blokovat** ovládací prvky

![Zásady prázdného podmíněného přístupu](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

Článek [Zásady společného podmíněného přístupu](concept-conditional-access-policy-common.md) obsahují některé zásady, o kterých si myslíme, že by byly užitečné pro většinu organizací.

## <a name="next-steps"></a>Další kroky

[Simulovat chování přihlášení pomocí nástroje Co-li podmíněného přístupu](troubleshoot-conditional-access-what-if.md)

[Plánování cloudového nasazení Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

[Správa kompatibility zařízení s Intune](/intune/device-compliance-get-started)

[Zabezpečení aplikací microsoftu a podmíněný přístup](/cloud-app-security/proxy-intro-aad)
