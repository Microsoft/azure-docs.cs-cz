---
title: Publikování aplikací pro nativní klient systému – Azure AD | Dokumentace Microsoftu
description: Popisuje, jak povolit nativní klientské aplikace pro komunikaci s konektoru Proxy aplikace Azure AD poskytovat zabezpečený vzdálený přístup k místním aplikacím.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4614d8190436ad89faa200f83b1a71bde10a8acb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684945"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Jak povolit nativní klientské aplikace pro interakci s proxy aplikací

Proxy aplikací Azure Active Directory (Azure AD) můžete použít k publikování webových aplikací, ale je také lze použít k publikování nativní klientské aplikace, které jsou nakonfigurovány s Azure AD Authentication Library (ADAL). Nativní klientské aplikace se liší od webové aplikace, protože jejich instalaci na zařízení, zatímco webové aplikace jsou přístupné prostřednictvím prohlížeče.

Pro podporu nativních klientských aplikací, přijímá Proxy aplikací Azure AD vydané tokeny, které se odesílají v záhlaví. Služba Proxy aplikace provádí ověřování pro uživatele. Toto řešení nepoužívá pro ověřování tokenů aplikace.

![Vztah mezi koncovým uživatelům, Azure Active Directory a publikované aplikace](./media/application-proxy-configure-native-client-application/richclientflow.png)

Pokud chcete publikovat nativních aplikací, použijte knihovna ověřování Azure AD, která se postará o ověřování a podporuje mnoho prostředí klienta. Proxy aplikací zapadá do [nativní aplikace pro webové rozhraní API scénář](../develop/native-app.md).

Tento článek vás provede čtyři kroky k publikování nativní aplikace s Proxy aplikace a knihovny pro ověřování Azure AD.

## <a name="step-1-publish-your-proxy-application"></a>Krok 1: Publikování aplikace proxy serveru

Publikování aplikace proxy serveru, stejně jako jakékoli jiné aplikace a přiřadit uživatele pro přístup k aplikaci. Další informace najdete v tématu [publikování aplikací pomocí Proxy aplikace](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Krok 2: Registrace vaší nativní aplikace

Teď musíte zaregistrovat aplikaci v Azure AD, následujícím způsobem:

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/). **Řídicí panel** pro **centra pro správu Azure Active Directory** se zobrazí.
2. Na bočním panelu vyberte **Azure Active Directory**. **Azure Active Directory** se zobrazí stránka s přehledem.
3. Na bočním panelu Přehled služby Azure AD, vyberte **registrace aplikací**. Zobrazí se seznam registrace všech aplikací.
4. Vyberte **registrace nové**. **Zaregistrovat aplikaci** se zobrazí stránka.

   ![Vytvoření registrace nové aplikace](./media/application-proxy-configure-native-client-application/create.png)
5. V **název** záhlaví, zadejte název zobrazení přístupných pro vaši aplikaci.
6. V části **podporovaných typů účtu** záhlaví, vyberte úroveň přístupu pomocí následujících pokynů:
   - Pokud chcete zaměřit pouze účty, které jsou interní v organizaci, vyberte **účty v tomto adresáři organizace jenom**.
   - Chcete-li cílit, pouze obchodní nebo vzdělávací zákazníků, vyberte **účty v libovolném adresáři organizace**.
   - Chcete-li cílit na nejširší sadu identit společnosti Microsoft, vyberte **účty v jakékoli organizaci adresáři a osobní účty Microsoft**.
7. V **identifikátor URI pro přesměrování** záhlaví, vyberte **veřejným klientem (mobilních a desktopových)**, a pak zadejte identifikátor URI přesměrování pro aplikaci.
8. Vyberte a čtení **zásady platformy Microsoftu**a pak vyberte **zaregistrovat**. Stránka s přehledem pro registrace nové aplikace se vytvoří a zobrazí.

Podrobné informace o vytváření registrace nové aplikace, najdete v článku [integrace aplikací s Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Krok 3: Udělení přístupu k proxy aplikace

Teď, když jste nativní aplikaci zaregistrujete, můžete jí přístup k ostatním aplikacím ve vašem adresáři, v tomto případě před přístupem k aplikaci proxy serveru. Pokud chcete povolit nativní aplikace zpřístupní aplikaci proxy serveru:

1. Na bočním panelu na nový registrační stránku aplikace, vyberte **oprávnění k rozhraní API**. **Oprávnění k rozhraní API** stránka registrace nové aplikace se zobrazí.
2. Vyberte **přidat oprávnění**. **Žádosti rozhraní API oprávnění** se zobrazí stránka.
3. V části **vyberte rozhraní API** vyberte **naše organizace bude využívat rozhraní API**. Zobrazí se seznam obsahující aplikace, které zveřejňují rozhraní API ve vašem adresáři.
4. Typ v poli vyhledávání nebo přejděte k vyhledání proxy aplikace, kterou jste publikovali v [krok 1: Proxy aplikace můžete publikovat](#step-1-publish-your-proxy-application)a pak vyberte aplikaci, proxy server.
5. V **jaký typ oprávnění aplikace vyžaduje?** záhlaví, vyberte typ oprávnění. Pokud vaše nativní aplikace potřebuje přístup k rozhraní API application proxy jako přihlášeného uživatele, zvolte **delegovaná oprávnění**. Pokud vaše nativní aplikace běží jako služba na pozadí nebo proces démon bez přihlášeného uživatele, zvolte **oprávnění aplikace**.
6. V **vyberte oprávnění** záhlaví, vyberte požadované oprávnění a vyberte **přidat oprávnění**. **Oprávnění k rozhraní API** stránky pro vaše nativní aplikace nyní zobrazuje proxy aplikace a oprávnění rozhraní API, které jste přidali.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Krok 4: Upravit Active Directory Authentication Library

Upravte kód nativní aplikace v rámci ověřování z Active Directory Authentication Library (ADAL) zahrnout následující text:

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

Požadované informace, které ve vzorovém kódu najdete na portálu Azure AD, následujícím způsobem:

| Informace o požadované | Jak najít na portálu Azure AD |
| --- | --- |
| \<ID tenanta > | **Azure Active Directory** > **vlastnosti** > **ID adresáře** |
| \<Externí adresa Url aplikace Proxy App > | **Podnikové aplikace** > *proxy aplikací* > **proxy aplikací** > **externí adresa Url** |
| \<ID aplikace, nativní aplikace > | **Podnikové aplikace** > *vaší nativní aplikací* > **vlastnosti** > **ID aplikace** |
| \<Identifikátor URI nativní aplikace pro přesměrování > | **Azure Active Directory** > **registrace aplikací** > *vaší nativní aplikací* > **identifikátory URI přesměrování** |
| \<Adresa Url rozhraní API App proxy > | **Azure Active Directory** > **registrace aplikací** > *vaší nativní aplikací* > **oprávnění k rozhraní API**  >  **Rozhraní API / NAME oprávnění** |

Po úpravě ADAL s použitím těchto parametrů, ověřování uživatelů pro nativní klientské aplikace i v případě, že se mimo podnikovou síť.

## <a name="next-steps"></a>Další postup

Další informace o toku nativní aplikace, najdete v části [nativní aplikace v Azure Active Directory](../develop/native-app.md).

Další informace o nastavení [jednotné přihlašování k aplikacím v Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
