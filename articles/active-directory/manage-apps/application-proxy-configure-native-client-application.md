---
title: Publikování aplikací pro nativní klient systému – Azure AD | Dokumentace Microsoftu
description: Popisuje, jak povolit nativní klientské aplikace pro komunikaci s konektoru Proxy aplikace Azure AD poskytovat zabezpečený vzdálený přístup k místním aplikacím.
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
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159280"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Jak povolit nativní klientské aplikace pro interakci s proxy aplikacemi

Pomocí služby Azure Active Directory (Azure AD) proxy aplikace můžete publikovat webové aplikace, ale také je lze použít k publikování nativních klientských aplikací, které jsou nakonfigurovány pomocí knihovny ADAL (Azure AD Authentication Library). Nativní klientské aplikace se liší od webových aplikací, protože jsou nainstalované v zařízení, zatímco webové aplikace jsou dostupné přes prohlížeč.

Pro podporu nativních klientských aplikací akceptuje proxy aplikací tokeny vydané službou Azure AD, které jsou odeslány v hlavičce. Služba proxy aplikací provádí ověřování pro uživatele. Toto řešení nepoužívá pro ověřování tokeny aplikace.

![Vztah mezi koncovými uživateli, službou Azure AD a publikovanými aplikacemi](./media/application-proxy-configure-native-client-application/richclientflow.png)

K publikování nativních aplikací použijte knihovnu ověřování Azure AD, která má za starosti ověřování a podporuje mnoho klientských prostředí. Proxy aplikace se zahodí do [scénáře nativní aplikace do webového rozhraní API](../azuread-dev/native-app.md).

Tento článek vás provede čtyři kroky k publikování nativní aplikace s Proxy aplikace a knihovny pro ověřování Azure AD.

## <a name="step-1-publish-your-proxy-application"></a>Krok 1: publikování proxy aplikace

Publikování aplikace proxy serveru, stejně jako jakékoli jiné aplikace a přiřadit uživatele pro přístup k aplikaci. Další informace najdete v tématu [publikování aplikací pomocí proxy aplikací](application-proxy-add-on-premises-application.md).

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

1. V záhlaví **identifikátor URI pro přesměrování** vyberte **veřejný klient (mobilní & Desktop)** a pak zadejte identifikátor URI pro přesměrování vaší aplikace.
1. Vyberte a přečtěte si **Zásady platformy Microsoftu**a pak vyberte **zaregistrovat**. Vytvoří a zobrazí se stránka s přehledem pro novou registraci aplikace.

Podrobnější informace o vytvoření nové registrace aplikace najdete v tématu [integrování aplikací pomocí Azure Active Directory](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>Krok 3: udělení přístupu k proxy aplikaci

Teď, když jste zaregistrovali nativní aplikaci, můžete v tomto případě přístup k aplikaci proxy udělit přístup k dalším aplikacím v adresáři. Povolení zpřístupnění nativní aplikace proxy aplikaci:

1. V bočním panelu na stránce Nová registrace aplikace vyberte **oprávnění rozhraní API**. Zobrazí se stránka **oprávnění rozhraní API** pro novou registraci aplikace.
1. Vyberte **Přidat oprávnění**. Zobrazí se stránka **oprávnění API pro vyžádání** .
1. V části **Vybrat nastavení rozhraní API** vyberte **rozhraní API moje organizace používá**. Zobrazí se seznam obsahující aplikace ve vašem adresáři, které zveřejňují rozhraní API.
1. Do vyhledávacího pole zadejte nebo přejděte na proxy aplikaci, kterou jste publikovali v [kroku 1: publikování aplikace proxy](#step-1-publish-your-proxy-application)a pak vyberte aplikaci proxy.
1. V poli **jaký typ oprávnění vyžaduje vaše aplikace?** vyberte typ oprávnění. Pokud vaše nativní aplikace potřebuje přístup k rozhraní API aplikace proxy jako přihlášený uživatel, vyberte **delegovaná oprávnění**.
1. V záhlaví **vybrat oprávnění** vyberte požadované oprávnění a pak vyberte **Přidat oprávnění**. Stránka **oprávnění API** pro vaši nativní aplikaci teď zobrazuje proxy aplikace a rozhraní API pro přístup, které jste přidali.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Krok 4: Úprava Active Directory Authentication Library

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

Požadované informace najdete v ukázkovém kódu na portálu Azure AD, a to následujícím způsobem:

| Požadovány informace | Jak ji najít na portálu Azure AD |
| --- | --- |
| ID tenanta \<> | **Azure Active Directory** > **vlastnosti** > **ID adresáře** |
| \<externí adresu URL proxy aplikace > | **Podnikové aplikace** > *proxy aplikace > * **proxy aplikace** > **externí adrese URL** |
| ID aplikace \<nativní aplikace > | **Podnikové aplikace** >  > **vlastností** *nativní aplikace* > **ID aplikace** |
| \<identifikátor URI pro přesměrování nativní aplikace > | **Azure Active Directory** > **Registrace aplikací** > *nativní aplikace* > **identifikátory URI pro přesměrování** |
| Adresa URL rozhraní API proxy aplikace \<> | **Azure Active Directory** > **Registrace aplikací** > *vaše nativní aplikace* > **oprávnění rozhraní API** > **název rozhraní API/oprávnění** |

Po úpravě ADAL pomocí těchto parametrů se uživatelé můžou ověřit pro nativní klientské aplikace i v případě, že jsou mimo podnikovou síť.

## <a name="next-steps"></a>Další kroky

Další informace o nativním toku aplikace najdete v tématu [nativní aplikace v Azure Active Directory](../azuread-dev/native-app.md).

Přečtěte si o nastavení [jednotného přihlašování k aplikacím v Azure Active Directory](what-is-single-sign-on.md#choosing-a-single-sign-on-method).
