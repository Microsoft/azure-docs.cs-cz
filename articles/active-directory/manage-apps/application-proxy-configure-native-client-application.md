---
title: Publikování nativních klientských aplikací – Azure AD | Dokumenty společnosti Microsoft
description: Popisuje, jak povolit nativní klientské aplikace pro komunikaci s Konektor proxy aplikací Azure AD poskytovat zabezpečený vzdálený přístup k místním aplikacím.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d2a2bb9dd543da7455a276075a829ef06032edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159280"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Jak povolit nativní klientské aplikace pro interakci s proxy aplikacemi

Proxy aplikace Azure Active Directory (Azure AD) můžete použít k publikování webových aplikací, ale taky se dá použít k publikování nativních klientských aplikací, které jsou nakonfigurované pomocí Azure AD Authentication Library (ADAL). Nativní klientské aplikace se liší od webových aplikací, protože jsou nainstalované na zařízení, zatímco webové aplikace jsou přístupné prostřednictvím prohlížeče.

Pro podporu nativní chod klientských aplikací, proxy aplikace přijímá tokeny vydané Azure AD, které jsou odesílány v záhlaví. Služba Proxy aplikace provádí ověřování pro uživatele. Toto řešení nepoužívá tokeny aplikace pro ověřování.

![Vztah mezi koncovými uživateli, Azure AD a publikovanými aplikacemi](./media/application-proxy-configure-native-client-application/richclientflow.png)

Chcete-li publikovat nativní aplikace, použijte azure ad ověřování knihovny, která se stará o ověřování a podporuje mnoho klientských prostředí. Proxy aplikace se vejde do [scénáře nativní aplikace pro webové rozhraní API](../azuread-dev/native-app.md).

Tento článek vás provede čtyřmi kroky k publikování nativní aplikace s proxy aplikací a Azure AD ověřování knihovny.

## <a name="step-1-publish-your-proxy-application"></a>Krok 1: Publikování proxy aplikace

Publikujte proxy aplikaci stejně jako jakoukoli jinou aplikaci a přiřaďte uživatelům přístup k vaší aplikaci. Další informace naleznete v [tématu Publish applications with Application Proxy](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Krok 2: Registrace nativní aplikace

Teď je potřeba zaregistrovat svou aplikaci ve službě Azure AD takto:

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/). Zobrazí se **řídicí panel** centra **pro správu Služby Azure Active Directory.**
1. Na postranním panelu vyberte **Azure Active Directory**. Zobrazí se stránka **přehledu služby Azure Active Directory.**
1. Na postranním panelu přehledu Azure AD vyberte **registrace aplikací**. Zobrazí se seznam všech registrací aplikací.
1. Vyberte **možnost Nová registrace**. Zobrazí se stránka **Registrovat aplikaci.**

   ![Vytvoření nové registrace aplikace na webu Azure Portal](./media/application-proxy-configure-native-client-application/create.png)

1. V záhlaví **Název** zadejte zobrazovaný název aplikace směřující k uživateli.
1. V části **Podporované typy účtů** vyberte úroveň přístupu pomocí těchto pokynů:

   - Chcete-li cílit pouze na účty, které jsou interní pro vaši organizaci, vyberte **možnost Účty pouze v tomto organizačním adresáři**.
   - Chcete-li cílit pouze na obchodní nebo vzdělávací zákazníky, vyberte **možnost Účty v libovolném organizačním adresáři**.
   - Chcete-li cílit na nejširší sadu identit Microsoftu, vyberte **účty v libovolném organizačním adresáři a osobních účtech Microsoft**.

1. V záhlaví **Identifikátor URI přesměrování** vyberte možnost Veřejný klient **(mobilní & plochy)** a zadejte identifikátor URI přesměrování pro vaši aplikaci.
1. Vyberte a přečtěte si **zásady platformy Microsoft**a pak vyberte **Registrovat**. Vytvoří se a zobrazí stránka s přehledem pro registraci nové aplikace.

Podrobnější informace o vytvoření nové registrace aplikace najdete v [tématu Integrace aplikací se službou Azure Active Directory](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Krok 3: Udělení přístupu k aplikaci proxy

Nyní, když jste zaregistrovali nativní aplikaci, můžete jí udělit přístup k jiným aplikacím ve vašem adresáři, v tomto případě pro přístup k proxy aplikaci. Chcete-li povolit nativní aplikace, které mají být vystaveny proxy aplikace:

1. V postranním panelu nové stránky registrace aplikace vyberte **oprávnění rozhraní API**. Zobrazí se stránka **oprávnění rozhraní API** pro novou registraci aplikace.
1. Vyberte **přidat oprávnění**. Zobrazí se stránka **Oprávnění rozhraní API požadavku.**
1. V části **Vyberte rozhraní API** vyberte **rozhraní API, která moje organizace používá**. Zobrazí se seznam obsahující aplikace v adresáři, které zveřejňují rozhraní API.
1. Zadejte do vyhledávacího pole nebo posunutím vyhledejte proxy aplikaci, kterou jste publikovali v [kroku 1: Publikujte proxy aplikaci](#step-1-publish-your-proxy-application)a vyberte aplikaci proxy.
1. V záhlaví **Jaký typ oprávnění aplikace vyžaduje?** Pokud vaše nativní aplikace potřebuje přístup k rozhraní API proxy aplikace jako přihlášenému uživateli, zvolte **Delegovaná oprávnění**.
1. V záhlaví **Vybrat oprávnění** vyberte požadovaná oprávnění a vyberte **Přidat oprávnění**. Stránka **oprávnění rozhraní API** pro nativní aplikaci nyní zobrazuje aplikaci proxy a rozhraní API oprávnění, které jste přidali.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Krok 4: Úprava knihovny ověřování služby Active Directory

Upravte nativní kód aplikace v kontextu ověřování služby ALOK (ADAL) a zahrňte tak následující text:

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

Požadované informace v ukázkovém kódu najdete na portálu Azure AD takto:

| Požadované informace | Jak ji najít na portálu Azure AD |
| --- | --- |
| \<> ID klienta | **Azure Active Directory** > **ID adresáře vlastností** **služby** > Azure Active Directory |
| \<Externí adresa URL> aplikace proxy | **Podnikové aplikace,** > *vaše proxy aplikace* > **Aplikační proxy externí** > **url** |
| \<ID aplikace nativní aplikace> | **Podnikové aplikace** > *iD* > aplikace**Vlastnosti** > **aplikace** |
| \<Přesměrovat identifikátor URI nativní> aplikace | **Registrace** > aplikací Azure Active Directory pro > *nativní aplikaci* > **Přesměrovat** **identifikátory**URI |
| \<Adresa URL rozhraní PROXY App API> | **Registrace** > aplikací Azure Active Directory**pro** > *nativní rozhraní* > **API permissions** > API rozhraní API nebo**oprávnění** |

Po úpravě ADAL s těmito parametry, vaši uživatelé mohou ověřit nativní klientské aplikace, i když jsou mimo podnikovou síť.

## <a name="next-steps"></a>Další kroky

Další informace o toku nativních aplikací najdete [v tématu Nativní aplikace ve službě Azure Active Directory](../azuread-dev/native-app.md).

Přečtěte si o nastavení [jednotného přihlašování k aplikacím ve službě Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
