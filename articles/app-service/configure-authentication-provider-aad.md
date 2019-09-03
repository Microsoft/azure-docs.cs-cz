---
title: Konfigurace ověřování Azure Active Directory – Azure App Service
description: Přečtěte si, jak nakonfigurovat ověřování Azure Active Directory pro aplikaci App Service.
author: cephalin
services: app-service
documentationcenter: ''
manager: gwallace
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service
ms.workload: web,mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/03/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 8b4b6549f9553773cc44c311f49befbb3eec9dc9
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233099"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Konfigurace aplikace pro App Service, aby používala Azure Active Directory přihlášení

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

> [!NOTE]
> V tuto chvíli není AAD v2 (včetně MSAL) podporovaná pro Azure App Service a Azure Functions.
>

V tomto článku se dozvíte, jak nakonfigurovat Azure App Service pro použití Azure Active Directory jako poskytovatele ověřování.

Doporučuje se nakonfigurovat každou aplikaci App Service s vlastní registrací, takže má vlastní oprávnění a souhlas. Zvažte také použití samostatných registrací aplikací pro samostatné sloty nasazení. Tím se vyhnete sdílení oprávnění mezi prostředími, aby problém v novém kódu, který testujete, nemá vliv na produkci.

## <a name="express"> </a>Konfigurace pomocí expresního nastavení

1. V [Azure Portal]přejděte do aplikace App Service. V levém navigačním panelu vyberte **ověřování/autorizace**.
2. Pokud není povolená možnost **ověřování/autorizace** ,vyberte zapnuto.
3. Vyberte **Azure Active Directory**a potom v **režimu správy**vyberte **Express** .
4. Výběrem **OK** zaregistrujete aplikaci App Service do Azure Active Directory. Tím se vytvoří nová registrace aplikace. Pokud chcete místo toho zvolit existující registraci aplikace, klikněte na **Vybrat existující aplikaci** a vyhledejte název dříve vytvořené registrace aplikace ve vašem tenantovi. Kliknutím vyberte registraci aplikace a klikněte na **OK**. Pak na stránce nastavení Azure Active Directory klikněte na **OK** .
Ve výchozím nastavení App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace.
5. Volitelné Pokud chcete omezit přístup k aplikaci jenom na uživatele ověřené pomocí Azure Active Directory, nastavte **akci, která se má provést, když se žádost neověřuje** , aby se přihlásila **pomocí Azure Active Directory**. To vyžaduje, aby všechny požadavky byly ověřené a všechny neověřené požadavky byly přesměrovány na Azure Active Directory pro ověřování.

    > [!NOTE]
    > Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. U takových aplikací může být upřednostňována možnost **povolení anonymních požadavků (bez akce)** , pokud se aplikace ručně spouští samotné přihlášení, jak je popsáno [zde](overview-authentication-authorization.md#authentication-flow).
6. Klikněte na **Uložit**.

## <a name="advanced"> </a>Konfigurace s pokročilým nastavením

Nastavení konfigurace můžete zadat také ručně, pokud se tenant Azure Active Directory, který chcete použít, liší od klienta, se kterým se přihlašujete k Azure. Chcete-li dokončit konfiguraci, musíte nejprve vytvořit registraci v Azure Active Directory a pak musíte zadat některé podrobnosti o registraci, které chcete App Service.

### <a name="register"> </a>Vytvoření registrace aplikace v Azure AD pro vaši aplikaci App Service

Při ručním vytváření registrace aplikace si poznamenejte tři informace, které budete potřebovat později při konfiguraci App Service aplikace: ID klienta, ID tenanta a volitelně tajný klíč klienta a identifikátor URI ID aplikace.

1. V [Azure Portal]přejděte do App Service aplikace a poznamenejte si **adresu URL**vaší aplikace. Použijete ho ke konfiguraci registrace aplikace Azure Active Directory.
1. V [Azure Portal]v nabídce vlevo vyberte možnost **Active Directory** > **Registrace aplikací** > **Nová registrace**. 
1. Na stránce **zaregistrovat aplikaci** zadejte **název** registrace vaší aplikace.
1. V části **identifikátor URI přesměrování**vyberte **Web** a zadejte adresu URL vaší App Service aplikace a přidejte cestu `/.auth/login/aad/callback`. Například, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Potom vyberte **Vytvořit**.
1. Po vytvoření registrace aplikace zkopírujte **ID aplikace (klienta)** a **ID adresáře (tenant)** pro pozdější použití.
1. Vyberte **branding**. Do pole **Adresa URL domovské stránky**zadejte adresu url vaší App Service aplikace a vyberte **Uložit**.
1. Vyberte **zveřejnit sadu rozhraní API** > . Vložte adresu URL vaší aplikace App Service a vyberte **Uložit**.

    > [!NOTE]
    > Tato hodnota je **identifikátor URI ID aplikace** pro registraci aplikace. Pokud chcete mít front-end webovou aplikaci pro přístup k back-endovému rozhraní API, například a chcete, aby back-end byl explicitně udělen přístup k front-endu, budete potřebovat **identifikátor URI ID aplikace** *front-endu* při konfiguraci prostředku App Service aplikace v < C2 back-end >
1. Vyberte **Přidat obor**. Do **název oboru**zadejte *user_impersonation*. Do textových polí zadejte název oboru souhlasu a popis, který mají uživatelé vidět na stránce s souhlasem, jako je například *přístup k aplikaci*. Po dokončení klikněte na **Přidat obor**.
1. Volitelné Pokud chcete vytvořit tajný klíč klienta, vyberte **certifikáty & tajné klíče** > **nový tajný klíč** > klienta**Přidat**. Zkopírujte hodnotu tajného klíče klienta zobrazenou na stránce. Po opuštění se znovu nezobrazí.
1. Volitelné Pokud chcete přidat víc **adres URL odpovědí**, v nabídce vyberte **ověřování** .

### <a name="secrets"> </a>Přidání informací o Azure Active Directory do aplikace App Service

1. V [Azure Portal]přejděte do aplikace App Service. V nabídce vlevo vyberte **ověřování/autorizace**. Pokud není povolená funkce ověřování/autorizace, vyberte **zapnuto**. 
1. Volitelné Ve výchozím nastavení App Service ověřování umožňuje neověřený přístup k vaší aplikaci. Pokud chcete vyhovět ověřování uživatele, nastavte **akci, která se má provést, když se žádost neověřuje** , aby se přihlásila **pomocí Azure Active Directory**.
1. V části zprostředkovatelé ověřování vyberte **Azure Active Directory**.
1. V **režimu správy**vyberte **upřesnit** a nakonfigurujte App Service ověřování podle následující tabulky:

    |Pole|Popis|
    |-|-|
    |ID klienta| Použijte **ID aplikace (klienta)** registrace aplikace. |
    |ID vystavitele| Pomocí `https://login.microsoftonline.com/<tenant-id>`a nahraďte  *\<> ID tenanta* ID **adresáře (tenanta)** registrace aplikace. |
    |Tajný kód klienta (volitelné)| Použijte tajný klíč klienta, který jste vygenerovali v registraci aplikace.|
    |Povolené cílové skupiny tokenu| Pokud se jedná o *back-endové* aplikace a chcete u front-endové aplikace zakázat ověřovací tokeny, přidejte sem **identifikátor URI ID aplikace** pro *front* -end. |
1. Vyberte **OK**a pak vyberte **Uložit**.

Nyní jste připraveni použít Azure Active Directory pro ověřování ve vaší aplikaci App Service.

## <a name="configure-a-native-client-application"></a>Konfigurace nativní klientské aplikace
Nativní klienty můžete zaregistrovat, pokud chcete provádět přihlášení pomocí klientské knihovny, jako je například **Active Directory Authentication Library**.

1. V [Azure Portal]v nabídce vlevo vyberte možnost **Active Directory** > **Registrace aplikací** > **Nová registrace**. 
1. Na stránce **zaregistrovat aplikaci** zadejte **název** registrace vaší aplikace.
1. V části **identifikátor URI pro přesměrování**vyberte **veřejný klient (mobilní & Desktop)** a zadejte adresu URL vaší App Service aplikace a přidejte cestu `/.auth/login/aad/callback`. Například, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Potom vyberte **Vytvořit**.

    > [!NOTE]
    > V případě aplikace pro Windows použijte místo toho identifikátor [SID balíčku](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) .
1. Po vytvoření registrace aplikace Zkopírujte hodnotu **ID aplikace (klienta)** .
1. V nabídce vlevo vyberte >  **oprávnění rozhraní API** **Přidat oprávnění** > **Moje rozhraní API**.
1. Vyberte registraci aplikace, kterou jste vytvořili dříve pro App Service aplikaci. Pokud se registrace aplikace nezobrazuje, zkontrolujte, že jste přidali obor **user_impersonation** v tématu [Vytvoření registrace aplikace ve službě Azure AD pro vaši aplikaci App Service](#register).
1. Vyberte **user_impersonation** a klikněte na **Přidat oprávnění**.

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
