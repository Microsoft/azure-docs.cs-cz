---
title: Konfigurace ověřování Azure Active Directory pro vaši aplikaci aplikační služby
description: Informace o konfiguraci ověřování služby Azure Active Directory pro vaši aplikaci aplikační služby.
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
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: 2530cb55cb054c02df5d55ccb86e959a061e2499
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-login"></a>Konfigurace aplikace služby App Service použít přihlášení Azure Active Directory
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Tento článek ukazuje, jak nakonfigurovat Azure App Services používat Azure Active Directory k ověřování.

## <a name="express"> </a>Konfigurace Azure Active Directory s použitím expresního nastavení
1. V [portál Azure], přejděte do aplikace služby App Service. V levém navigačním panelu, vyberte **ověřování / autorizace**.
2. Pokud **ověřování / autorizace** není povoleno, vyberte **na**.
3. Vyberte **Azure Active Directory**a potom vyberte **Express** pod **režim správy**.
4. Vyberte **OK** pro registraci aplikace služby App Service v Azure Active Directory. Tím se vytvoří nové registrace aplikace. Pokud chcete místo toho vyberte existující registraci aplikace, klikněte na tlačítko **vybrat existující aplikaci** a poté vyhledejte název registrace dříve vytvořenou aplikací v rámci vašeho klienta.
   Klikněte na možnost registrace aplikace, vyberte ho a klikněte na tlačítko **OK**. Pak klikněte na tlačítko **OK** na stránce nastavení služby Azure Active Directory.
   Ve výchozím nastavení služby App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu webu a rozhraní API. Je nutné autorizovat uživatele v kódu aplikace.
5. (Volitelné) Chcete-li omezit přístup k webu jenom na uživatele ověřeného službou Azure Active Directory, nastavte **akci provést, když požadavek nebude ověřený** k **přihlásit se přes Azure Active Directory**. To vyžaduje, že všechny žádosti o ověření a všechny neověřené požadavky se přesměrují do Azure Active Directory pro ověřování.
6. Klikněte na **Uložit**.

Nyní jste připraveni používat pro ověřování v aplikaci aplikační služby Azure Active Directory.

## <a name="advanced"> </a>(Alternativní metoda) Ručně nakonfigurovat upřesňující nastavení Azure Active Directory
Můžete také zadat nastavení konfigurace ručně. Toto je upřednostňovaný řešení Pokud klienta AAD, který si přejete použít se liší od klienta, se kterým jste přihlášení do Azure. Chcete-li dokončit konfiguraci, musíte nejdřív vytvořit registrace v Azure Active Directory a potom je nutné zadat některé podrobnosti registrace do služby App Service.

### <a name="register"> </a>Zaregistrovat aplikaci aplikační služby Azure Active Directory
1. Přihlaste se na [portál Azure]a přejděte do aplikace služby App Service. Kopírovat aplikace **URL**. Bude to použít ke konfiguraci registrace aplikace Azure Active Directory.
2. Přejděte na **služby Active Directory**, vyberte **registrace aplikace**, pak klikněte na tlačítko **nové registrace aplikace** v horní části spuštění nové aplikace registrace. 
3. V **vytvořit** stránky, zadejte **název** pro registraci vaší aplikace, vyberte **webovou aplikaci nebo rozhraní API** zadejte v **přihlašovací adresa URL** pole vložení Adresa URL aplikace (z kroku 1). Klikněte na **vytvořit**.
4. Během pár sekund měli byste vidět nové registrace aplikace, kterou jste právě vytvořili.
5. Po přidání registrace aplikace, klikněte na název registrace aplikace, klikněte na **nastavení** nahoře, potom klikněte na **vlastnosti** 
6. V **identifikátor ID URI aplikace** pole, vložte adresu URL aplikace (z kroku 1), také v **adresa URL domovské stránky** vložte adresu URL aplikace (z kroku 1), a pak klikněte na tlačítko **uložit**
7. Nyní klikněte na **adresy URL odpovědí**, upravit **adresa URL odpovědi**, vložte adresu URL aplikace (z kroku 1), je třeba změnit protokol, který se zajistěte, aby byla **https://** protocol (ne http://) potom připojen na konec adresy URL, */.auth/login/aad/callback* (například `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Klikněte na **Uložit**.   
8.  V tomto okamžiku zkopírovat **ID aplikace** pro aplikaci. Dodržte pro pozdější použití. Je nutné ho nakonfigurovat aplikaci aplikační služby.
9. Zavřít **registrovaná aplikace** stránky. Na **registrace aplikace** klikněte na **koncové body** tlačítko nahoře, zkopírujte **dokument federačních metadat** adresy URL. 
10. Otevřete nové okno prohlížeče a přejděte na adresu URL kliknutím vkládání a přejděte na stránku XML. V horní části dokumentu se **EntityDescriptor** elementu, by měl být **entityID** atribut formuláře `https://sts.windows.net/` následuje identifikátor GUID, které jsou specifické pro vašeho klienta (nazývané "ID klienta"). Tato hodnota pro kopírování – slouží jako vaše **URL vystavitele**. Můžete nakonfigurovat aplikace pro pozdější použití.

### <a name="secrets"> </a>Přidat informace o službě Azure Active Directory do aplikace služby App Service
1. Zpět v [portál Azure], přejděte do aplikace služby App Service. Klikněte na tlačítko **ověřování/autorizace**. Pokud není povolená funkce ověřování/autorizace, zapněte přepínač k **na**. Klikněte na **Azure Active Directory**, v rámci zprostředkovatele ověřování, ke konfiguraci vaší aplikace. (Volitelné) Ve výchozím nastavení služby App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu webu a rozhraní API. Je nutné autorizovat uživatele v kódu aplikace. Nastavit **akci provést, když požadavek nebude ověřený** k **přihlásit se přes Azure Active Directory**. Tato možnost vyžaduje, že všechny žádosti o ověření a všechny neověřené požadavky se přesměrují do Azure Active Directory pro ověřování.
2. V konfiguraci ověřování služby Active Directory, klikněte na tlačítko **Upřesnit** pod **režim správy**. Vložte ID aplikace do pole ID klienta (z kroku 8) a vložte do hodnoty URL vystavitele entityId (z kroku 10). Pak klikněte na **OK**.
3. Na stránce konfigurace ověřování služby Active Directory klikněte na tlačítko **Uložit**.

Nyní jste připraveni používat pro ověřování v aplikaci aplikační služby Azure Active Directory.

## <a name="optional-configure-a-native-client-application"></a>(Volitelné) Konfigurovat nativní klientskou aplikaci
Azure Active Directory taky umožňuje registraci nativních klientů, které poskytuje větší kontrolu nad oprávněními mapování. Tuto funkci potřebujete Pokud chcete provést přihlášení pomocí knihovny, jako **Active Directory Authentication Library**.

1. Přejděte na **Azure Active Directory** v [portál Azure].
2. V levém navigačním panelu, vyberte **registrace aplikace**. Klikněte na tlačítko **nové registrace aplikace** v horní části.
4. V **vytvořit** stránky, zadejte **název** pro registraci vaší aplikace. Vyberte **nativní** v **typ aplikace**.
5. V **identifikátor URI pro přesměrování** zadejte váš web */.auth/login/done* koncový bod, pomocí schéma HTTPS. Tato hodnota by měla být podobná *https://contoso.azurewebsites.net/.auth/login/done*. Pokud se vytváří aplikace pro systém Windows, místo toho použijte [balíček SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) jako identifikátor URI.
5. Klikněte na možnost **Vytvořit**.
6. Po přidání registrace aplikace, vyberte ho otevřete. Najít **ID aplikace** a poznamenejte si tuto hodnotu.
7. Klikněte na tlačítko **všechna nastavení** > **požadovaná oprávnění** > **přidat** > **vybrat rozhraní API**.
8. Zadejte název aplikace služby App Service, který jste dříve registrován na, vyhledejte a vyberte ho a klikněte na **vyberte**. 
9. Vyberte **přístup \<app_name >**. Pak klikněte na tlačítko **vyberte**. Potom klikněte na **Done** (Hotovo).

Nyní máte nakonfigurovaný nativní klientskou aplikaci, která můžete přístup k aplikaci aplikační služby.

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
[8]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth-config.png



<!-- URLs. -->

[portál Azure]: https://portal.azure.com/
[alternative method]:#advanced
