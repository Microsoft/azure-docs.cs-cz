---
title: Podpora ověřování bez hesla s FIDO2 klíči v aplikacích, které vyvíjíte | Azure
titleSuffix: Microsoft identity platform
description: Tento průvodce nasazením vysvětluje, jak podporovat ověřování bez hesla pomocí klíčů zabezpečení FIDO2 v aplikacích, které vyvíjíte.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 1/29/2021
ms.author: nichola
ms.custom: aaddev
ms.openlocfilehash: f63d7aed75b14f5f008a639d667d8806b233b9fa
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174594"
---
# <a name="support-passwordless-authentication-with-fido2-keys-in-apps-you-develop"></a>Podpora ověřování bez hesla s FIDO2 klíči v aplikacích, které vyvíjíte

Tyto konfigurace a osvědčené postupy vám pomůžou se vyhnout běžným scénářům, které blokují [FIDO2 ověřování bez hesla](../../active-directory/authentication/concept-authentication-passwordless.md) pro uživatele vašich aplikací.

## <a name="general-best-practices"></a>Obecné osvědčené postupy

### <a name="domain-hints"></a>Pomocné parametry domény

Nepoužívejte doporučení domény pro obejít [zjišťování domovské sféry](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md). Tato funkce je určena k efektivnějšímu přihlášení, ale federovaný poskytovatel identity nemusí podporovat ověřování bez hesla.

### <a name="requiring-specific-credentials"></a>Vyžadování specifických přihlašovacích údajů

Pokud používáte SAML, nezadávejte, že je vyžadováno heslo [pomocí elementu RequestedAuthnContext](single-sign-on-saml-protocol.md#requestauthncontext).

Element RequestedAuthnContext je nepovinný, takže pokud ho chcete vyřešit, můžete ho odebrat ze svých požadavků na ověření SAML. Toto je obecný osvědčený postup, protože použití tohoto prvku může také zabránit dalším možnostem ověřování, jako je vícefaktorové ověřování, aby fungovalo správně.

### <a name="using-the-most-recently-used-authentication-method"></a>Použití naposledy použité metody ověřování

Přihlašovací metoda, která byla naposledy používána uživatelem, bude uvedena jako první. To může způsobit nejasnost, pokud se uživatelé domnívají, že musí použít první prezentovanou možnost. Nicméně si můžou vybrat jinou možnost výběrem možnosti "Další způsoby přihlášení", jak je znázorněno níže.

:::image type="content" source="./media/support-fido2-authentication/most-recently-used-method.png" alt-text="Obrázek uživatelského prostředí pro ověřování, které zvýrazňuje tlačítko, které uživateli umožňuje změnit metodu ověřování.":::

## <a name="platform-specific-best-practices"></a>Osvědčené postupy specifické pro platformu

### <a name="desktop"></a>Plocha

Doporučené možnosti pro implementaci ověřování jsou v uvedeném pořadí:

- Desktopové aplikace .NET, které používají knihovnu Microsoft Authentication Library (MSAL), by měly používat Správce ověřování systému Windows (WAM). Tato integrace a její výhody jsou [zdokumentovány na GitHubu](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/wam).
- [WebView2](/microsoft-edge/webview2/) můžete použít k podpoře FIDO2 v vloženém prohlížeči.
- Použijte prohlížeč systému. Tyto metody standardně používají knihovny MSAL pro stolní platformy. Můžete se obrátit na naši stránku kompatibility prohlížeče FIDO2 a ujistit se, že používaný prohlížeč podporuje ověřování FIDO2.

### <a name="mobile"></a>Mobilní

Od února 2021 se FIDO2 v současné době nepodporuje pro nativní aplikace pro iOS nebo Android, ale je ve vývoji.

Aby bylo možné připravit aplikace na její dostupnost a jako obecné osvědčené postupy by měly aplikace pro iOS a Android používat MSAL s výchozí konfigurací webového prohlížeče systému.

Pokud nepoužíváte MSAL, měli byste k ověřování používat i systémový webový prohlížeč. Funkce, jako je jednotné přihlašování a podmíněný přístup, spoléhají na sdílený webový povrch, který poskytuje webový prohlížeč systému. To znamená použití [vlastních karet](https://developer.chrome.com/docs/multidevice/android/customtabs/) (Android) pro Chrome nebo [ověřování uživatele prostřednictvím webové služby | Dokumentace pro vývojáře Apple](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service) (iOS)

### <a name="web-and-single-page-apps"></a>Webové a jednostránkové aplikace

Dostupnost FIDO2 ověřování pomocí hesla pro aplikace, které běží ve webovém prohlížeči, bude záviset na kombinaci prohlížeče a platformy. Můžete si prohlédnout naši [FIDO2ou kompatibilitu](../authentication/fido2-compatibility.md) , kde zjistíte, jestli se podporuje kombinace vašich uživatelů.

## <a name="next-steps"></a>Další kroky

[Možnosti ověřování neheslem pro Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)