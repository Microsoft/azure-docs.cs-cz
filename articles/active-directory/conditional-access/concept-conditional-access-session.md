---
title: Ovládací prvky relace v zásadách podmíněného přístupu – Azure Active Directory
description: Co jsou ovládací prvky relace v zásadách podmíněného přístupu Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a0089d246169ad4215075662500794e7143940e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90601805"
---
# <a name="conditional-access-session"></a>Podmíněný přístup: relace

V rámci zásad podmíněného přístupu může správce využít ovládací prvky relace k umožnění omezeného prostředí v rámci konkrétních cloudových aplikací.

![Zásada podmíněného přístupu s uděleným ovládacím prvkem, který vyžaduje vícefaktorové ověřování](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Omezení vynucované aplikací

Organizace můžou pomocí tohoto ovládacího prvku vyžadovat, aby služba Azure AD předávala informace o zařízení vybraným cloudovým aplikacím. Informace o zařízení umožňuje cloudovým aplikacím zjistit, jestli je připojení iniciované z kompatibilního zařízení nebo zařízení připojeného k doméně. Tento ovládací prvek podporuje pouze SharePoint Online a Exchange Online jako vybrané cloudové aplikace. Když vyberete tuto možnost, cloudová aplikace použije informace o zařízení k poskytování uživatelů v závislosti na stavu zařízení s omezeným nebo úplným prostředím.

Další informace o tom, jak používat a konfigurovat omezení pro uplatnění aplikace, najdete v následujících článcích:

- [Povolení omezeného přístupu na SharePointu Online](/sharepoint/control-access-from-unmanaged-devices)
- [Povolení omezeného přístupu pomocí Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Řízení aplikací podmíněného přístupu

Řízení podmíněného přístupu k aplikacím používá architekturu reverzního proxy serveru a je jedinečnou integrací s podmíněným přístupem Azure AD. Podmíněný přístup Azure AD umožňuje vynutilit řízení přístupu v aplikacích vaší organizace na základě určitých podmínek. Podmínky definují, kdo (uživatel nebo skupina uživatelů) a co (které cloudové aplikace) a kde kde (která umístění a sítě) jsou aplikovány na zásady podmíněného přístupu. Po určení podmínek můžete uživatele směrovat do [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) , kde můžete chránit data pomocí řízení podmíněného přístupu k aplikacím pomocí řízení přístupu a relací.

Řízení podmíněného přístupu k aplikacím umožňuje monitorovat a kontrolovat přístup k uživatelským aplikacím v reálném čase na základě zásad přístupu a relací. Zásady přístupu a relace se používají na portálu Cloud App Security k dalšímu upřesnění filtrů a nastavení akcí, které mají být provedeny pro uživatele. Pomocí zásad přístupu a relací můžete:

- Zakázat data exfiltrace: můžete blokovat stahování, vyjmutí, kopírování a tisk citlivých dokumentů, například nespravované zařízení.
- Chránit při stažení: místo blokování stahování citlivých dokumentů můžete vyžadovat, aby dokumenty byly označené a chráněné pomocí Azure Information Protection. Tato akce zajistí, že je dokument chráněný a že přístup uživatelů je omezený v potenciálně rizikové relaci.
- Zabránit nahrání souborů bez popisků: před nahráním, distribucí a používání citlivých souborů je důležité zajistit, aby měl soubor pravý popisek a ochranu. Můžete zajistit, aby se neoznačené soubory s citlivým obsahem blokovaly, dokud uživatel neklasifikuje obsah.
- Monitorování uživatelských relací pro dodržování předpisů: rizikové uživatele se monitorují, když se přihlásí k aplikacím a jejich akce se protokolují v rámci relace. Můžete prozkoumat a analyzovat chování uživatelů a pochopit, kde a za jakých podmínek by se měly zásady relace v budoucnu použít.
- Blokovat přístup: můžete členit blok přístupu pro konkrétní aplikace a uživatele v závislosti na několika rizikových faktorech. Můžete je například blokovat, pokud používají klientské certifikáty jako formu správy zařízení.
- Blokovat vlastní aktivity: některé aplikace mají jedinečné scénáře, které mají riziko, například posílání zpráv s citlivým obsahem v aplikacích, jako je Microsoft Teams nebo časová rezerva. V těchto druzích scénářů můžete kontrolovat zprávy citlivého obsahu a zablokovat je v reálném čase.

Další informace najdete v článku [nasazení řízení podmíněného přístupu k aplikacím pro vybrané aplikace](/cloud-app-security/proxy-deployment-aad).

## <a name="sign-in-frequency"></a>Frekvence přihlášení

Frekvence přihlášení definuje časový interval před tím, než se uživateli zobrazí výzva k opětovnému přihlášení při pokusu o přístup k prostředku.

Nastavení četnosti přihlašování funguje s aplikacemi, které implementovaly protokoly OAUTH2 nebo OIDC podle standardů. Většina nativních aplikací Microsoftu pro Windows, Mac a mobilní zařízení, včetně těchto webových aplikací, dodržuje toto nastavení.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portál pro správu Microsoft 365
- Exchange Online
- SharePoint a OneDrive
- Webový klient pro týmy
- Dynamics CRM Online
- portál Azure

Další informace najdete v článku [Konfigurace správy relace ověřování pomocí podmíněného přístupu](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session"></a>Trvalá relace prohlížeče

Trvalá relace prohlížeče umožňuje uživatelům zůstat přihlášeni po zavření a opětovném otevření okna prohlížeče.

Další informace najdete v článku [Konfigurace správy relace ověřování pomocí podmíněného přístupu](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="next-steps"></a>Další kroky

- [Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

- [Režim pouze sestav](concept-conditional-access-report-only.md)
