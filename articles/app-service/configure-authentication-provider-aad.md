---
title: Konfigurace ověřování Azure Active Directory – Azure App Service
description: Naučte se nakonfigurovat Azure Active Directory ověřování pro vaši aplikaci App Services.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/20/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 41084c3532e29b3a52c121d48226c5a45857d5dc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088238"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Konfigurace aplikace pro App Service, aby používala Azure Active Directory přihlášení

> [!NOTE]
> V tuto chvíli se pro Azure App Services a Azure Functions nepodporuje AAD v2 (včetně MSAL). Vraťte se prosím na aktualizace.
>

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto článku se dozvíte, jak nakonfigurovat službu Azure App Services tak, aby používala Azure Active Directory jako poskytovatele ověřování.

## <a name="express"> </a>Konfigurace pomocí expresního nastavení

1. V [Azure Portal]přejděte do aplikace App Service. V levém navigačním panelu vyberte **ověřování/autorizace**.
2. Pokud není povolená možnost **ověřování/autorizace** ,vyberte zapnuto.
3. Vyberte **Azure Active Directory**a potom v **režimu správy**vyberte **Express** .
4. Výběrem **OK** zaregistrujete aplikaci App Service do Azure Active Directory. Tím se vytvoří nová registrace aplikace. Pokud chcete místo toho zvolit existující registraci aplikace, klikněte na **Vybrat existující aplikaci** a vyhledejte název dříve vytvořené registrace aplikace ve vašem tenantovi.
   Kliknutím vyberte registraci aplikace a klikněte na **OK**. Pak na stránce nastavení Azure Active Directory klikněte na **OK** .
   Ve výchozím nastavení App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace.
5. Volitelné Pokud chcete omezit přístup k vašemu webu jenom na uživatele ověřené pomocí Azure Active Directory, nastavte **akci, která se má provést, když se žádost neověřuje** , aby se přihlásila **pomocí Azure Active Directory**. To vyžaduje, aby všechny požadavky byly ověřené a všechny neověřené požadavky byly přesměrovány na Azure Active Directory pro ověřování.

> [!CAUTION]
> Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. U takových aplikací může být upřednostňována možnost **povolení anonymních požadavků (bez akce)** , pokud se aplikace ručně spouští samotné přihlášení, jak je popsáno [zde](overview-authentication-authorization.md#authentication-flow).

6. Klikněte na **Uložit**.

## <a name="advanced"> </a>Konfigurace s pokročilým nastavením

Nastavení konfigurace můžete zadat také ručně. Toto je preferované řešení, pokud Azure Active Directory tenanta, kterého chcete použít, se liší od klienta, se kterým se přihlašujete k Azure. Chcete-li dokončit konfiguraci, musíte nejprve vytvořit registraci v Azure Active Directory a pak musíte zadat některé podrobnosti o registraci, které chcete App Service.

### <a name="register"> </a>Registrace aplikace App Service s využitím Azure Active Directory

1. Přihlaste se k [Azure Portal]a přejděte do aplikace App Service. Zkopírujte **adresu URL**aplikace. Pomocí tohoto nastavení nakonfigurujete registraci aplikace Azure Active Directory.
2. Přejděte do **služby Active Directory**, vyberte **Registrace aplikací**a potom kliknutím na **Nová registrace aplikace** v horní části spusťte novou registraci aplikace. 
3. Na stránce **vytvořit** zadejte **název** registrace vaší aplikace, vyberte typ **Webová aplikace/rozhraní API** , v poli **Adresa URL pro přihlášení** vložte adresu URL aplikace (z kroku 1). Pak klikněte na **vytvořit**.
4. Během několika sekund by se měla zobrazit nová registrace aplikace, kterou jste právě vytvořili.
5. Po přidání registrace aplikace klikněte na název registrace aplikace, klikněte na **Nastavení** v horní části a potom klikněte na **vlastnosti** . 
6. Do pole **identifikátor URI ID aplikace** vložte adresu URL aplikace (z kroku 1), taky do adresy **URL domovské stránky** vložte adresu URL aplikace (z kroku 1) a pak klikněte na **Uložit** .
7. Nyní klikněte na **adresy URL odpovědí**, upravte **adresu URL odpovědi**, vložte adresu URL aplikace (z kroku 1) a pak ji přidejte na konec adresy URL */.auth/Login/AAD/callback* (například `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Klikněte na **Uložit**.

   > [!NOTE]
   > Můžete použít stejnou registraci aplikace pro více domén přidáním dalších **adres URL pro odpovědi**. Nezapomeňte modelovat každou instanci App Service s vlastní registrací, takže má vlastní oprávnění a souhlas. Zvažte také použití samostatných registrací aplikací pro samostatné sloty webu. K tomu je potřeba zabránit sdílení oprávnění mezi prostředími, aby chyba v novém testovaném kódu neovlivnila produkční verzi.
    
8. V tomto okamžiku zkopírujte **ID aplikace** pro aplikaci. Ponechte ho pro pozdější použití. Budete ho potřebovat ke konfiguraci aplikace App Service.
9. Zavřete stránku **registrovaná aplikace** . Na stránce **Registrace aplikací** klikněte v horní části na tlačítko **koncové body** a pak zkopírujte adresu URL **koncového bodu pro přihlášení ke službě WS-Federation** , ale `/wsfed` odeberte koncovou adresu URL. Konečný výsledek by měl vypadat takto `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000`:. Název domény se může u svrchovaného cloudu lišit. Tato akce bude sloužit jako adresa URL vystavitele později.

### <a name="secrets"> </a>Přidání informací o Azure Active Directory do aplikace App Service

1. Zpátky v [Azure Portal]přejděte do aplikace App Service. Klikněte na **ověřování/autorizace**. Pokud není povolená funkce ověřování/autorizace, zapněte přepínač na **zapnuto**. Pokud chcete nakonfigurovat aplikaci, klikněte v části poskytovatelé ověřování na **Azure Active Directory**.

    Volitelné Ve výchozím nastavení App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace. Nastavte **akci, která se má provést, když se žádost neověřuje** pro **přihlášení pomocí Azure Active Directory**. Tato možnost vyžaduje, aby všechny požadavky byly ověřené a všechny neověřené požadavky byly přesměrovány na Azure Active Directory pro ověřování.
2. V konfiguraci ověřování služby Active Directory klikněte na **Upřesnit** v části **režim správy**. Vložte ID aplikace do pole ID klienta (z kroku 8) a vložte adresu URL (z kroku 9) do hodnoty adresy URL vystavitele. Pak klikněte na **OK**.
3. Na stránce Konfigurace ověřování služby Active Directory klikněte na **Uložit**.

Nyní jste připraveni použít Azure Active Directory pro ověřování ve vaší aplikaci App Service.

## <a name="configure-a-native-client-application"></a>Konfigurace nativní klientské aplikace
Můžete registrovat nativní klienty, které poskytují větší kontrolu nad mapováním oprávnění. To budete potřebovat, pokud chcete provádět přihlášení pomocí klientské knihovny, jako je **Active Directory Authentication Library**.

1. V [Azure Portal]přejděte na **Azure Active Directory** .
2. V levém navigačním panelu vyberte **Registrace aplikací**. V horní části klikněte na **Registrace nové aplikace** .
4. Na stránce **vytvořit** zadejte **název** registrace vaší aplikace. Vyberte možnost **nativní** v **typu aplikace**.
5. Do pole **identifikátor URI pro přesměrování** Zadejte koncový bod */.auth/Login/Done* vašeho webu pomocí schématu https. Tato hodnota by měla být podobná *https://contoso.azurewebsites.net/.auth/login/done* . Pokud vytváříte aplikaci pro Windows, místo toho použijte identifikátor [SID balíčku](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) jako identifikátor URI.
5. Klikněte na možnost **Vytvořit**.
6. Po přidání registrace aplikace ji můžete otevřít tak, že ji vyberete. Vyhledejte **ID aplikace** a poznamenejte si tuto hodnotu.
7. Klikněte na **všechna nastavení** > **požadovaná oprávnění** > **Přidat** > **Vybrat rozhraní API**.
8. Zadejte název aplikace App Service, kterou jste předtím zaregistrovali, a vyberte ji a klikněte na **Vybrat**.
9. Vyberte **Access \<APP_NAME >** . Pak klikněte na **Vybrat**. Potom klikněte na **Done** (Hotovo).

Nyní jste nakonfigurovali nativní klientskou aplikaci, která má přístup k vaší aplikaci App Service.

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
