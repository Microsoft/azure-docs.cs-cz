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
ms.date: 11/08/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: 0609d7e2da482fa7791dff34e4c6099f2ddbb0bd
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55965303"
---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Jak povolit nativní klientské aplikace pro interakci s proxy aplikací

Kromě webových aplikací Azure Active Directory Application Proxy je také možné publikovat nativní klientské aplikace, které jsou nakonfigurovány s Azure AD Authentication Library (ADAL). Nativní klientské aplikace se liší od webové aplikace, protože se instalují na zařízení, zatímco webové aplikace jsou přístupné prostřednictvím prohlížeče. 

Proxy aplikací podporuje nativní klientské aplikace přijímá Azure AD vydaných tokenech odesílaných v záhlaví. Proxy aplikací služby provádí ověření jménem uživatele. Toto řešení pro ověřování tokenů aplikace nepoužívá. 

![Vztah mezi koncovým uživatelům, Azure Active Directory a publikované aplikace](./media/application-proxy-configure-native-client-application/richclientflow.png)

Pomocí knihovny ověřování AD Azure, která se postará o ověřování a podporuje mnoho prostředí klienta, publikovat nativních aplikací. Proxy aplikací zapadá do [nativní aplikace pro webové rozhraní API scénář](../develop/native-app.md). 

Tento článek vás provede čtyři kroky k publikování nativní aplikace s Proxy aplikace a knihovny pro ověřování Azure AD. 

## <a name="step-1-publish-your-application"></a>Krok 1: Publikování aplikace
Publikování aplikace proxy serveru, stejně jako jakékoli jiné aplikace a přiřadit uživatele pro přístup k aplikaci. Další informace najdete v tématu [publikování aplikací pomocí Proxy aplikace](application-proxy-add-on-premises-application.md).

## <a name="step-2-configure-your-application"></a>Krok 2: Konfigurace aplikace
Nakonfigurujte vaše nativní aplikace následujícím způsobem:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do **Azure Active Directory** > **registrace aplikací**.
3. Vyberte **Registrace nové aplikace**.
4. Zadejte název pro vaši aplikaci, vyberte **nativní** jako typ aplikace a zadejte identifikátor URI přesměrování pro aplikaci. 

   ![Vytvoření registrace nové aplikace](./media/application-proxy-configure-native-client-application/create.png)
5. Vyberte **Vytvořit**.

Podrobné informace o vytváření registrace nové aplikace, najdete v článku [integrace aplikací s Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md).


## <a name="step-3-grant-access-to-other-applications"></a>Krok 3: Udělit přístup k ostatním aplikacím
Povolte nativní aplikace zpřístupní k ostatním aplikacím ve vašem adresáři:

1. Pořád ještě v **registrace aplikací**, vyberte nové nativní aplikaci, kterou jste právě vytvořili.
2. Vyberte **oprávnění k rozhraní API**.
3. Vyberte **přidat oprávnění**.
4. Prvním krokem otevřete **vyberte rozhraní API**.
5. Pomocí panelu hledání vyhledejte aplikaci Proxy aplikací, kterou jste publikovali v první části. Zvolte tuto aplikaci a potom klikněte na tlačítko **vyberte**. 

   ![Najděte aplikaci, proxy server](./media/application-proxy-configure-native-client-application/select_api.png)
6. Otevřete druhý krok **vyberte oprávnění**.
7. Pomocí zaškrtávacího políčka udělte vaše nativní aplikace přístup k proxy aplikace a pak klikněte na **vyberte**.

   ![Udělení přístupu k proxy aplikace](./media/application-proxy-configure-native-client-application/select_perms.png)
8. Vyberte **Done** (Hotovo).


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

Proměnné ve vzorovém kódu by měla být nahrazená následujícím způsobem:

* **ID tenanta** najdete na webu Azure Portal. Přejděte do **Azure Active Directory** > **vlastnosti** a zkopírujte ID adresáře. 
* **Externí adresa URL** je front-end adresa URL jste zadali v Proxy aplikací. Chcete-li tuto hodnotu najít, přejděte na **proxy aplikací** části proxy aplikace.
* **ID aplikace** nativní aplikace můžete najít na **vlastnosti** stránky nativní aplikace.
* **Identifikátor URI nativní aplikace pro přesměrování** můžete najít na **identifikátory URI přesměrování** stránky nativní aplikace.

Po ADAL je upravit s použitím těchto parametrů, uživatelé by mělo být schopni ověřit nativní klientské aplikace i v případě, že se mimo podnikovou síť. 

## <a name="next-steps"></a>Další postup

Další informace o toku nativní aplikace, najdete v části [nativní aplikace pro webové rozhraní API](../develop/native-app.md)

Další informace o nastavení [jednotného přihlašování pro Proxy aplikací](what-is-single-sign-on.md#single- sign-on-methods)
