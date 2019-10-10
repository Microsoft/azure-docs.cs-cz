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
ms.custom: fasttrack-edit
ms.openlocfilehash: ac73b549546c353dce4c40005b7742577e03d26c
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176981"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>Konfigurace aplikace App Service pro použití přihlášení Azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto článku se dozvíte, jak nakonfigurovat Azure App Service pro použití Azure Active Directory (Azure AD) jako poskytovatele ověřování.

> [!NOTE]
> V současné době se Azure App Service a Azure Functions podporují jenom Azure AD v 1.0. Nepodporují [Microsoft Identity Platform v 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-overview), což zahrnuje knihovny Microsoft Authentication Library (MSAL).

Při nastavování aplikace a ověřování použijte tyto osvědčené postupy:

- Poskytněte každé aplikaci App Service vlastní oprávnění a souhlas.
- Nakonfigurujte každou aplikaci App Service s vlastní registrací.
- Nepoužívejte sdílení oprávnění mezi prostředími pomocí samostatných registrací aplikací pro samostatné sloty nasazení. Při testování nového kódu Tento postup může přispět k tomu, aby se zabránilo problémům v ovlivnění produkční aplikace.

## <a name="express"> </a>Konfigurace pomocí expresního nastavení

1. V [Azure Portal]přejdete do aplikace App Service.
1. V levém podokně vyberte **nastavení** > **ověřování/autorizace** a ujistěte se, že je **zapnuté** **ověřování App Service** .
1. Vyberte **Azure Active Directory**a potom v **režimu správy**vyberte **Express** .
1. Výběrem **OK** zaregistrujete aplikaci App Service do Azure Active Directory. Vytvoří se nová registrace aplikace.

   Pokud chcete místo toho zvolit existující registraci aplikace:

   1. Zvolte **Vybrat existující aplikaci** a potom vyhledejte název dříve vytvořené registrace aplikace ve vašem tenantovi.
   1. Vyberte registraci aplikace a pak vyberte **OK**.
   1. Pak na stránce nastavení Azure Active Directory vyberte **OK** .

   Ve výchozím nastavení App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace.
1. Volitelné Pokud chcete omezit přístup k aplikacím jenom na uživatele ověřené nástrojem Azure Active Directory, nastavte **akci, která se má provést, když se žádost neověřuje** , aby se **přihlásila pomocí Azure Active Directory**. Když nastavíte tuto funkci, aplikace vyžaduje ověření všech požadavků. Také přesměruje všechna neověřená na Azure Active Directory pro ověřování.

    > [!CAUTION]
    > Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. U takových aplikací může být upřednostňována možnost **povolení anonymních požadavků (žádná akce)** , přičemž aplikace se ručně spouští samotné přihlášení. Další informace najdete v tématu [tok ověřování](overview-authentication-authorization.md#authentication-flow).
1. Vyberte **Save** (Uložit).

## <a name="advanced"> </a>Konfigurace s pokročilým nastavením

Pokud chcete použít tenanta Azure AD, který je jiný než ten, který používáte k přihlášení do Azure, můžete nastavení aplikace nakonfigurovat ručně. K dokončení této vlastní konfigurace budete potřebovat:

1. Vytvořte registraci ve službě Azure AD.
1. Zadejte informace o registraci pro App Service.

### <a name="register"> </a>Vytvoření registrace aplikace v Azure AD pro vaši aplikaci App Service

Při konfiguraci aplikace App Service budete potřebovat následující informace:

- ID klienta
- ID tenanta
- Tajný kód klienta (volitelné)
- Identifikátor URI ID aplikace

Proveďte následující kroky:

1. Přihlaste se k [Azure Portal] a pokračujte do aplikace App Service. Poznamenejte si **adresu URL**vaší aplikace. Použijete ho ke konfiguraci registrace aplikace Azure Active Directory.
1. Vyberte **Azure Active Directory** > **Registrace aplikací** > **Nová registrace**.
1. Na stránce **zaregistrovat aplikaci** zadejte **název** registrace vaší aplikace.
1. V části **identifikátor URI pro přesměrování**vyberte **Web** a zadejte adresu URL vaší App Service aplikace a přidejte cestu `/.auth/login/aad/callback`. Například, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Vyberte **Create** (Vytvořit).
1. Po vytvoření registrace aplikace zkopírujte **ID aplikace (klienta)** a **ID adresáře (tenant)** pro pozdější použití.
1. Vyberte **branding**. Do pole **Adresa URL domovské stránky**zadejte adresu URL vaší aplikace App Service a vyberte **Uložit**.
1. Vyberte možnost **zveřejnit rozhraní API** > **sady**. Vložte adresu URL vaší aplikace App Service a vyberte **Uložit**.

   > [!NOTE]
   > Tato hodnota je **identifikátor URI ID aplikace** pro registraci aplikace. Pokud vaše webová aplikace vyžaduje přístup k rozhraní API v cloudu, budete potřebovat **identifikátor URI ID aplikace** webové aplikace, když nakonfigurujete cloudový App Service prostředek. Tuto možnost můžete použít například v případě, že chcete, aby cloudová služba explicitně udělila přístup k webové aplikaci.

1. Vyberte **Přidat obor**.
   1. Do **název oboru**zadejte *user_impersonation*.
   1. Do textových polí zadejte název oboru souhlasu a popis, který mají uživatelé vidět na stránce s souhlasem. Zadejte například *přístup k aplikaci*. 
   1. Vyberte **Přidat obor**.
1. Volitelné Pokud chcete vytvořit tajný klíč klienta, vyberte **certifikáty & tajných**kódů  > **nový tajný klíč klienta** > **Přidat**. Zkopírujte hodnotu tajného klíče klienta zobrazenou na stránce. Znovu se nezobrazí.
1. Volitelné Pokud chcete přidat víc **adres URL odpovědi**, vyberte **ověřování**.

### <a name="secrets"> </a>Přidání informací o Azure Active Directory do aplikace App Service

1. V [Azure Portal]přejdete do aplikace App Service. 
1. V levém podokně vyberte **nastavení > ověřování/autorizace** a ujistěte se, že je **zapnuté** **ověřování App Service** .
1. Volitelné Ve výchozím nastavení App Service ověřování umožňuje neověřený přístup k vaší aplikaci. Pokud chcete vyhovět ověřování uživatele, nastavte **akci, která se má provést, když se žádost neověřuje** , aby se **přihlásila pomocí Azure Active Directory**.
1. V části zprostředkovatelé ověřování vyberte **Azure Active Directory**.
1. V **režimu správy**vyberte **upřesnit** a nakonfigurujte App Service ověřování podle následující tabulky:

    |Pole|Popis|
    |-|-|
    |ID klienta| Použijte **ID aplikace (klienta)** registrace aplikace. |
    |ID vystavitele| Použijte `https://login.microsoftonline.com/<tenant-id>` a nahraďte *> \<tenant-ID* s **ID adresáře (tenant)** registrace aplikace. |
    |Tajný kód klienta (volitelné)| Použijte tajný klíč klienta, který jste vygenerovali v registraci aplikace.|
    |Povolené cílové skupiny tokenů| Pokud se jedná o cloudovou nebo serverovou aplikaci a chcete z webové aplikace dovolit ověřovací tokeny, přidejte sem **identifikátor URI ID aplikace** webové aplikace. |

    > [!NOTE]
    > Nakonfigurované **ID klienta** se *vždycky* implicitně považuje za povolenou cílovou skupinu, a to bez ohledu na to, jak jste nakonfigurovali **povolené cílové skupiny tokenů**.
1. Vyberte **OK**a pak vyberte **Uložit**.

Nyní jste připraveni použít Azure Active Directory pro ověřování ve vaší aplikaci App Service.

## <a name="configure-a-native-client-application"></a>Konfigurace nativní klientské aplikace

Nativní klienty můžete registrovat, aby bylo možné ověřování pomocí klientské knihovny, jako je například **Active Directory Authentication Library**.

1. V [Azure Portal]vyberte možnost **Active Directory** > **Registrace aplikací** > **Nová registrace**.
1. Na stránce **zaregistrovat aplikaci** zadejte **název** registrace vaší aplikace.
1. V části **identifikátor URI pro přesměrování**vyberte **veřejný klient (mobilní & Desktop)** a zadejte adresu URL vaší aplikace App Service a přidejte cestu `/.auth/login/aad/callback`. Například, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Vyberte **Create** (Vytvořit).

    > [!NOTE]
    > V případě aplikace pro Windows použijte místo toho identifikátor [SID balíčku](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) .
1. Po vytvoření registrace aplikace Zkopírujte hodnotu **ID aplikace (klienta)** .
1. Vyberte **oprávnění API** > **přidejte oprávnění** > **Moje rozhraní API**.
1. Vyberte registraci aplikace, kterou jste vytvořili dříve pro App Service aplikaci. Pokud se registrace aplikace nezobrazuje, ujistěte se, že jste přidali obor **user_impersonation** v tématu [Vytvoření registrace aplikace ve službě Azure AD pro vaši aplikaci App Service](#register).
1. Vyberte **user_impersonation**a pak vyberte **Přidat oprávnění**.

Nyní jste nakonfigurovali nativní klientskou aplikaci, která má přístup k vaší aplikaci App Service.

## <a name="related-content"></a>Další kroky

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
