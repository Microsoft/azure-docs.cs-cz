---
title: Konfigurovat ověřování Azure Active Directory - službě Azure App Service
description: Zjistěte, jak konfigurovat ověřování Azure Active Directory pro vaši aplikaci App Services.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/20/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: d687e770fae6c32ee351a597e12d1aca6094e5cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60851371"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Konfigurace aplikace App Service pro použití přihlášení Azure Active Directory

> [!NOTE]
> V tuto chvíli AAD V2 (včetně MSAL) není podporována pro Azure App Services a Azure Functions. Zkontrolujte prosím novinky.
>

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Tento článek ukazuje postup při konfiguraci Azure App Service pro použití služby Azure Active Directory jako zprostředkovatele ověřování.

## <a name="express"> </a>Konfigurace pomocí expresního nastavení

1. V [Azure Portal], přejděte do aplikace služby App Service. V levém navigačním panelu vyberte **ověřování / autorizace**.
2. Pokud **ověřování / autorizace** není povoleno, vyberte **na**.
3. Vyberte **Azure Active Directory**a pak vyberte **Express** pod **režim správy**.
4. Vyberte **OK** registrovat aplikace služby App Service v Azure Active Directory. Tím se vytvoří nová registrace aplikací. Pokud chcete místo toho zvolit existující registraci aplikace, klikněte na tlačítko **vybrat existující aplikaci** a vyhledejte název registrace dříve vytvořené aplikace v rámci vašeho tenanta.
   Klikněte na možnost registrace aplikace, vyberte ho a klikněte na **OK**. Pak klikněte na tlačítko **OK** na stránce nastavení služby Azure Active Directory.
   Ve výchozím nastavení služby App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu webu a rozhraní API. V kódu vaší aplikace musí uživateli uživatele autorizovat.
5. (Volitelné) Chcete-li omezit přístup k webu pouze uživatelům ověření službou Azure Active Directory, nastavte **akce má být provedena, když požadavek nebude ověřený** k **přihlášení pomocí Azure Active Directory**. K tomu je potřeba ověřit, že všechny požadavky, a všechny neověřené požadavky jsou přesměrovány do služby Azure Active Directory pro ověřování.
6. Klikněte na **Uložit**.

## <a name="advanced"> </a>Konfigurace s pokročilým nastavením

Můžete také zadat nastavení konfigurace ručně. To je preferovaným řešením, pokud se liší od tenanta, pomocí kterého můžete přihlásit k Azure tenanta Azure Active Directory, kterou chcete použít. Chcete-li dokončit konfiguraci, musíte nejdřív vytvořit registrace ve službě Azure Active Directory a pak je nutné zadat některé podrobnosti registrace do služby App Service.

### <a name="register"> </a>Zaregistrovat aplikaci služby App Service pomocí Azure Active Directory

1. Přihlaste se k [Azure Portal]a přejděte do aplikace služby App Service. Kopírovat aplikaci **URL**. Bude to použít ke konfiguraci vaší registrace aplikace Azure Active Directory.
2. Přejděte do **služby Active Directory**, vyberte **registrace aplikací**, pak klikněte na tlačítko **registrace nové aplikace** v horní části stránky na spuštění registrace nové aplikace. 
3. V **vytvořit** stránky, zadejte **název** registrace vaší aplikace, vyberte **webová aplikace / rozhraní API** zadejte v **přihlašovací adresa URL** pole vložit Adresa URL aplikace (z kroku 1). Klikněte na **vytvořit**.
4. Během několika sekund byste měli vidět registrace nové aplikace, kterou jste právě vytvořili.
5. Po registraci aplikace se přidala, klikněte na název registrace aplikace, klikněte na **nastavení** nahoře, potom klikněte na **vlastnosti** 
6. V **identifikátor ID URI aplikace** pole, vložte adresu URL aplikace (z kroku 1), také v **adresa URL domovské stránky** vložte adresu URL aplikace (z kroku 1), potom klikněte na tlačítko **uložit**
7. Nyní klikněte na **adresy URL odpovědí**, upravit **adresy URL odpovědi**, vložte adresu URL aplikace (z kroku 1) a pak připojte na konec adresy URL, */.auth/login/aad/callback* (pro například `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Klikněte na **Uložit**.

   > [!NOTE]
   > Můžete použít stejné registrace aplikace pro víc domén tak, že přidáte další **adresy URL odpovědí**. Ujistěte se, že k modelování každá instance služby App Service s vlastním registrací, tak má svůj vlastní oprávnění a vyjádření souhlasu. Také zvážit použití registrace samostatných aplikací pro samostatnou lokalitu sloty. To je vyhnout se sdílí mezi prostředími, oprávnění tak, aby chybu v novém kódu, který testujete nemá vliv na produkční.
    
8. V tomto okamžiku zkopírovat **ID aplikace** pro aplikaci. Zachovat ho pro pozdější použití. Budete potřebovat a nakonfigurovat aplikaci služby App Service.
9. Zavřít **registrovaná aplikace** stránky. Na **registrace aplikací** stránky, klikněte na **koncové body** tlačítko v horní části stránky, zkopírujte **koncového bodu WS-FEDERATION přihlašování** adresu URL, ale odebrat `/wsfed` končí z adresy URL. Konečný výsledek by měl vypadat jako `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000`. Název domény se může lišit pro suverénních cloudů. To bude sloužit jako adresu URL vystavitele pro pozdější.

### <a name="secrets"> </a>Přidejte informace o Azure Active Directory do aplikace služby App Service

1. Zpátky [Azure Portal], přejděte do aplikace služby App Service. Klikněte na tlačítko **ověřování/autorizace**. Pokud není povolená funkce ověřování/autorizace, přepněte přepínač **na**. Klikněte na **Azure Active Directory**, v části zprostředkovatelů ověřování, ke konfiguraci vaší aplikace.

    (Volitelné) Ve výchozím nastavení služby App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu webu a rozhraní API. V kódu vaší aplikace musí uživateli uživatele autorizovat. Nastavte **akce má být provedena, když požadavek nebude ověřený** k **přihlášení pomocí Azure Active Directory**. Tato možnost vyžaduje, že všechny žádosti o ověření a všechny neověřené požadavky jsou přesměrovány do služby Azure Active Directory pro ověřování.
2. V konfiguraci ověřování služby Active Directory, klikněte na tlačítko **Upřesnit** pod **režim správy**. Vložte ID aplikace do pole ID klienta (z kroku 8) a vložte adresu URL (z kroku 9) na hodnotu adresy URL vystavitele. Pak klikněte na **OK**.
3. Na stránce konfigurace ověřování služby Active Directory, klikněte na tlačítko **Uložit**.

Nyní jste připraveni pro ověřování ve vaší aplikaci služby App Service pomocí Azure Active Directory.

## <a name="configure-a-native-client-application"></a>Konfigurace nativní klientské aplikace
Můžete zaregistrovat nativní klienty, které poskytuje větší kontrolu nad oprávněními mapování. Budete ho potřebovat Pokud budete chtít provést přihlášení pomocí klientské knihovny, jako **Active Directory Authentication Library**.

1. Přejděte do **Azure Active Directory** v [Azure Portal].
2. V levém navigačním panelu vyberte **registrace aplikací**. Klikněte na tlačítko **registrace nové aplikace** v horní části.
4. V **vytvořit** stránky, zadejte **název** pro registraci vaší aplikace. Vyberte **nativní** v **typ aplikace**.
5. V **identifikátor URI pro přesměrování** zadejte váš web */.auth/login/done* koncový bod, používat schéma HTTPS. Tato hodnota by měl být podobný *https://contoso.azurewebsites.net/.auth/login/done*. Vytvoření aplikace pro Windows, místo toho použití-li [balíček SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) jako identifikátor URI.
5. Klikněte na možnost **Vytvořit**.
6. Po registraci aplikace se přidala, vyberte ji a otevřete ho. Najít **ID aplikace** a poznamenejte si tuto hodnotu.
7. Klikněte na tlačítko **všechna nastavení** > **požadovaná oprávnění** > **přidat** > **vyberte rozhraní API**.
8. Zadejte název aplikace služby App Service, které jste zaregistrovali dříve vyhledejte, pak ho vyberte a klikněte na **vyberte**.
9. Vyberte **přístup \<app_name >**. Pak klikněte na **Vybrat**. Potom klikněte na **Done** (Hotovo).

Nyní jste nakonfigurovali nativní klientské aplikace s přístupem k aplikaci služby App Service.

## <a name="related-content"> </a>Související obsah

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[alternative method]:#advanced
