---
title: Publikování nativních klientských aplikací – Azure AD | Microsoft Docs
description: Obsahuje informace o tom, jak povolit nativním klientským aplikacím komunikovat se službou Azure Proxy aplikací služby AD Connector, aby bylo možné zajistit zabezpečený vzdálený přístup k místním aplikacím.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e830b52accefa0bcb76cc3434cae2fe94eed97db
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258658"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Jak povolit nativní klientské aplikace pro interakci s proxy aplikacemi

K publikování webových aplikací můžete použít proxy aplikace Azure Active Directory (Azure AD), ale také je lze použít k publikování nativních klientských aplikací, které jsou nakonfigurovány pomocí knihovny Microsoft Authentication Library (MSAL). Nativní klientské aplikace se liší od webových aplikací, protože jsou nainstalované v zařízení, zatímco webové aplikace jsou dostupné přes prohlížeč.

Pro podporu nativních klientských aplikací akceptuje proxy aplikací tokeny vydané službou Azure AD, které jsou odeslány v hlavičce. Služba proxy aplikací provádí ověřování pro uživatele. Toto řešení nepoužívá pro ověřování tokeny aplikace.

![Vztah mezi koncovými uživateli, službou Azure AD a publikovanými aplikacemi](./media/application-proxy-configure-native-client-application/richclientflow.png)

K publikování nativních aplikací použijte knihovnu Microsoft Authentication Library, která se stará o ověřování a podporuje mnoho klientských prostředí. Proxy aplikace se zahodí do [aplikace klasické pracovní plochy, která volá webové rozhraní API jménem přihlášeného uživatelského](../develop/authentication-flows-app-scenarios.md#desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user) scénáře.

Tento článek vás provede čtyřmi kroky k publikování nativní aplikace s proxy aplikací a knihovnou ověřování Azure AD.

## <a name="step-1-publish-your-proxy-application"></a>Krok 1: publikování proxy aplikace

Publikujte proxy aplikaci stejně jako jakoukoli jinou aplikaci a přiřaďte uživatelům přístup k vaší aplikaci. Další informace najdete v tématu [publikování aplikací pomocí proxy aplikací](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>Krok 2: registrace vaší nativní aplikace

Teď musíte aplikaci zaregistrovat ve službě Azure AD, a to takto:

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/). Zobrazí se **řídicí panel** **centra pro správu Azure Active Directory** .
1. Na bočním panelu vyberte **Azure Active Directory**. Zobrazí se stránka přehled **Azure Active Directory** .
1. Na bočním panelu přehled služby Azure AD vyberte **Registrace aplikací**. Zobrazí se seznam všech registrací aplikací.
1. Vyberte **Nová registrace**. Zobrazí se stránka **zaregistrovat aplikaci** .

   ![Vytvořte novou registraci aplikace v Azure Portal](./media/application-proxy-configure-native-client-application/create.png)

1. V záhlaví **název** zadejte zobrazovaný název pro uživatele pro vaši aplikaci.
1. V záhlaví **podporované typy účtů** vyberte úroveň přístupu pomocí těchto pokynů:

   - Pokud chcete cílit jenom na účty, které jsou pro vaši organizaci interní, vyberte **účty jenom v tomto organizačním adresáři**.
   - Pokud chcete cílit jenom na obchodní nebo vzdělávací zákazníky, vyberte **účty v jakémkoli adresáři organizace**.
   - Pokud chcete cílit na nejširší sadu identit Microsoftu, vyberte **účty v libovolném organizačním adresáři a v osobních účtech Microsoft**.
1. V části **identifikátor URI pro přesměrování** vyberte **veřejný klient (mobilní & Desktop)** a pak zadejte identifikátor URI `https://login.microsoftonline.com/common/oauth2/nativeclient` pro přesměrování vaší aplikace.
1. Vyberte a přečtěte si **Zásady platformy Microsoftu** a pak vyberte **zaregistrovat**. Vytvoří a zobrazí se stránka s přehledem pro novou registraci aplikace.

Podrobnější informace o vytvoření nové registrace aplikace najdete v tématu [integrování aplikací pomocí Azure Active Directory](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Krok 3: udělení přístupu k proxy aplikaci

Teď, když jste zaregistrovali nativní aplikaci, můžete v tomto případě přístup k aplikaci proxy udělit přístup k dalším aplikacím v adresáři. Povolení zpřístupnění nativní aplikace proxy aplikaci:

1. V bočním panelu na stránce Nová registrace aplikace vyberte **oprávnění rozhraní API**. Zobrazí se stránka **oprávnění rozhraní API** pro novou registraci aplikace.
1. Vyberte **Přidat oprávnění**. Zobrazí se stránka **oprávnění API pro vyžádání** .
1. V části **Vybrat nastavení rozhraní API** vyberte **rozhraní API moje organizace používá**. Zobrazí se seznam obsahující aplikace ve vašem adresáři, které zveřejňují rozhraní API.
1. Do vyhledávacího pole zadejte nebo přejděte na proxy aplikaci, kterou jste publikovali v [kroku 1: publikování aplikace proxy](#step-1-publish-your-proxy-application)a pak vyberte aplikaci proxy.
1. V poli **jaký typ oprávnění vyžaduje vaše aplikace?** vyberte typ oprávnění. Pokud vaše nativní aplikace potřebuje přístup k rozhraní API aplikace proxy jako přihlášený uživatel, vyberte **delegovaná oprávnění**.
1. V záhlaví **vybrat oprávnění** vyberte požadované oprávnění a pak vyberte **Přidat oprávnění**. Stránka **oprávnění API** pro vaši nativní aplikaci teď zobrazuje proxy aplikace a rozhraní API pro přístup, které jste přidali.

## <a name="step-4-add-the-microsoft-authentication-library-to-your-code-net-c-sample"></a>Krok 4: přidejte do kódu knihovnu Microsoft Authentication Library (ukázka .NET C#)

Upravte kód nativní aplikace v kontextu ověřování knihovny Microsoft Authentication Library (MSAL) tak, aby obsahovala následující text: 

```         
// Acquire Access Token from AAD for Proxy Application
IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }

if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application

  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

Požadované informace najdete v ukázkovém kódu na portálu Azure AD, a to následujícím způsobem:

| Požadovány informace | Jak ji najít na portálu Azure AD |
| --- | --- |
| \<Tenant ID> | **Azure Active Directory**  >  **Vlastnosti**  >  **ID adresáře** |
| \<App ID of the Native app> | **Registrace aplikace**  >  *vaše nativní aplikace*  >  **Přehled**  >  **ID aplikace** |
| \<Scope> | **Registrace aplikace**  >  *vaše nativní aplikace*  >  **Oprávnění k rozhraní api** > klikněte na rozhraní API pro oprávnění (user_impersonation) > panel s titulkem **user_impersonation** se objeví na pravé straně. > oboru je adresa URL v poli pro úpravy.
| \<Proxy App Url> | Externí adresa URL a cesta k rozhraní API

Po úpravě kódu MSAL pomocí těchto parametrů se uživatelé můžou ověřit pro nativní klientské aplikace i v případě, že jsou mimo podnikovou síť.

## <a name="next-steps"></a>Další kroky

Další informace o nativním toku aplikace najdete v tématu [nativní aplikace v Azure Active Directory](../azuread-dev/native-app.md).

Přečtěte si o nastavení [jednotného přihlašování k aplikacím v Azure Active Directory](sso-options.md#choosing-a-single-sign-on-method).