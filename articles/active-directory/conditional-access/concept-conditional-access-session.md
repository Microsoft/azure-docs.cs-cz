---
title: Ovládací prvky relací v zásadách podmíněného přístupu – Azure Active Directory
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
ms.openlocfilehash: e99b9b87f939d614679fdecf24c9d36d99bf2938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671884"
---
# <a name="conditional-access-session"></a>Podmíněný přístup: Relace

V rámci zásad podmíněného přístupu může správce využít ovládací prvky relace k povolení omezených možností v rámci konkrétních cloudových aplikací.

![Zásady podmíněného přístupu s ovládacím prvkem udělení, který vyžaduje vícefaktorové ověřování](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Omezení vynucená aplikací

Organizace můžou pomocí tohoto ovládacího prvku vyžadovat, aby Azure AD předává informace o zařízení vybraným cloudovým aplikacím. Informace o zařízení umožňují cloudovým aplikacím zjistit, jestli je připojení inicializováno ze zařízení s předpisy nebo pro připojení k doméně. Tento ovládací prvek podporuje jenom SharePoint Online a Exchange Online jako vybrané cloudové aplikace. Když je tato možnost vybrána, cloudová aplikace používá informace o zařízení k tomu, aby uživatelům v závislosti na stavu zařízení poskytla omezené nebo úplné prostředí.

Další informace o použití a konfiguraci omezení vynucených aplikací najdete v následujících článcích:

- [Povolení omezeného přístupu pomocí SharePointu Online](/sharepoint/control-access-from-unmanaged-devices)
- [Povolení omezeného přístupu pomocí Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Řízení aplikace podmíněného přístupu

Řízení aplikací podmíněného přístupu používá architekturu reverzního proxy serveru a je jedinečně integrované s podmíněným přístupem Azure AD. Podmíněný přístup Azure AD umožňuje vynutit ovládací prvky přístupu v aplikacích vaší organizace na základě určitých podmínek. Podmínky definují, kdo (uživatel nebo skupina uživatelů) a co (které cloudové aplikace) a kde (která umístění a sítě) se zásady podmíněného přístupu použijí. Po určení podmínek můžete uživatele směrovat do [služby Microsoft Cloud App Security,](/cloud-app-security/what-is-cloud-app-security) kde můžete chránit data pomocí řízení aplikací podmíněného přístupu pomocí řízení přístupu a relace.

Řízení aplikací podmíněného přístupu umožňuje přístup k uživatelským aplikacím a relacím monitorovat a řídit v reálném čase na základě zásad přístupu a relací. Zásady přístupu a relací se používají v rámci portálu Cloud App Security k dalšímu upřesnění filtrů a nastavení akcí, které mají být přijaty na uživatele. Pomocí zásad přístupu a relace můžete:

- Zabránit exfiltraci dat: Stahování, vyjmutí, kopírování a tisk citlivých dokumentů můžete blokovat například na nespravovaných zařízeních.
- Chraňte při stahování: Místo blokování stahování citlivých dokumentů můžete vyžadovat, aby byly dokumenty označeny a chráněny službou Azure Information Protection. Tato akce zajistí, že dokument je chráněn a přístup uživatelů je omezen v potenciálně rizikové relaci.
- Zabránit nahrávání souborů bez popisku: Před nahráním, distribucí a používáním citlivého souboru jinými uživateli je důležité zajistit, aby soubor měl správný popisek a ochranu. Můžete zajistit, aby neoznačené soubory s citlivým obsahem byly blokovány, dokud uživatel obsah neklasifikuje.
- Sledování dodržování předpisů uživatelů: Rizikoví uživatelé jsou sledováni při přihlášení k aplikacím a jejich akce jsou zaznamenány z relace. Můžete prozkoumat a analyzovat chování uživatelů, abyste pochopili, kde a za jakých podmínek by měly být zásady relace použity v budoucnu.
- Blokovat přístup: Přístup pro konkrétní aplikace a uživatele můžete granulálně zablokovat v závislosti na několika rizikových faktorech. Můžete je například zablokovat, pokud používají klientské certifikáty jako formu správy zařízení.
- Blokovat vlastní aktivity: Některé aplikace mají jedinečné scénáře, které nesou riziko, například odesílání zpráv s citlivým obsahem v aplikacích, jako je Microsoft Teams nebo Slack. V těchto typech scénářů můžete skenovat zprávy pro citlivý obsah a blokovat je v reálném čase.

Další informace naleznete v článku [Nasazení řízení aplikací podmíněného přístupu pro doporučené aplikace](/cloud-app-security/proxy-deployment-aad).

## <a name="sign-in-frequency-preview"></a>Frekvence přihlášení (náhled)

Frekvence přihlášení definuje časové období, než je uživatel vyzván k novému přihlášení při pokusu o přístup k prostředku.

Nastavení frekvence přihlášení funguje s aplikacemi, které implementovaly protokoly OAUTH2 nebo OIDC podle standardů. S tímto nastavením je v souladu většina nativních aplikací Microsoftu pro Windows, Mac a Mobile, včetně následujících webových aplikací.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portál pro správu O365
- Exchange Online
- SharePoint a OneDrive
- Webový klient Teams
- Dynamics CRM Online
- portál Azure

Další informace naleznete v článku [Konfigurace správy relací ověřování pomocí podmíněného přístupu](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session-preview"></a>Trvalá relace prohlížeče (náhled)

Trvalá relace prohlížeče umožňuje uživatelům zůstat přihlášeni po zavření a opětovném otevření okna prohlížeče.

Další informace naleznete v článku [Konfigurace správy relací ověřování pomocí podmíněného přístupu](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="next-steps"></a>Další kroky

- [Podmíněné přístupové běžné zásady](concept-conditional-access-policy-common.md)

- [Režim pouze sestav](concept-conditional-access-report-only.md)
