---
title: Konfigurace ověřování Azure AD
description: Naučte se, jak nakonfigurovat ověřování Azure Active Directory jako zprostředkovatele identity pro vaši aplikaci App Service.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 69959418c52eb7324efe19ca41481e426b822ab4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842349"
---
# <a name="configure-your-app-service-app-to-use-azure-ad-login"></a>Konfigurace aplikace App Service pro použití přihlášení Azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto článku se dozvíte, jak nakonfigurovat Azure App Service pro použití Azure Active Directory (Azure AD) jako poskytovatele ověřování.

Při nastavování aplikace a ověřování použijte tyto osvědčené postupy:

- Poskytněte každé aplikaci App Service vlastní oprávnění a souhlas.
- Nakonfigurujte každou aplikaci App Service s vlastní registrací.
- Nepoužívejte sdílení oprávnění mezi prostředími pomocí samostatných registrací aplikací pro samostatné sloty nasazení. Při testování nového kódu Tento postup může přispět k tomu, aby se zabránilo problémům v ovlivnění produkční aplikace.

## <a name="express"> </a>Konfigurace pomocí expresního nastavení

1. V [Azure Portal]vyhledejte a vyberte **App Services**a pak vyberte svou aplikaci.
2. V levém navigačním panelu vyberte **ověřování/autorizace** > **zapnuto**.
3. Vyberte **Azure Active Directory** > **Express**.

   Pokud chcete místo toho zvolit existující registraci aplikace:

   1. Zvolte **Vybrat existující aplikaci AD**a pak klikněte na **aplikace Azure AD**.
   2. Vyberte existující registraci aplikace a klikněte na **OK**.

3. Výběrem **OK** zaregistrujete aplikaci App Service do Azure Active Directory. Vytvoří se nová registrace aplikace.
   
    ![Expresní nastavení v Azure Active Directory](./media/configure-authentication-provider-aad/express-settings.png)
   
4. Volitelné Ve výchozím nastavení App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace. Pokud chcete omezit přístup k aplikacím jenom na uživatele ověřené nástrojem Azure Active Directory, nastavte **akci, která se má provést, když se žádost neověřuje** , aby se **přihlásila pomocí Azure Active Directory**. Když nastavíte tuto funkci, aplikace vyžaduje ověření všech požadavků. Také přesměruje všechna neověřená na Azure Active Directory pro ověřování.

    > [!CAUTION]
    > Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. U takových aplikací může být upřednostňována možnost **povolení anonymních požadavků (žádná akce)** , přičemž aplikace se ručně spouští samotné přihlášení. Další informace najdete v tématu [tok ověřování](overview-authentication-authorization.md#authentication-flow).
5. Vyberte **Uložit**.

## <a name="advanced"> </a>Konfigurace s pokročilým nastavením

Nastavení aplikace můžete nakonfigurovat ručně, pokud chcete použít registraci aplikace z jiného tenanta Azure AD. Dokončení této vlastní konfigurace:

1. Vytvořte registraci ve službě Azure AD.
2. Zadejte informace o registraci pro App Service.

### <a name="register"> </a>Vytvoření registrace aplikace v Azure AD pro vaši aplikaci App Service

Při konfiguraci aplikace App Service budete potřebovat následující informace:

- ID klienta
- ID tenanta
- Tajný kód klienta (volitelné)
- Identifikátor URI ID aplikace

Proveďte následující kroky:

1. Přihlaste se k [Azure Portal], vyhledejte a vyberte **App Services**a pak vyberte svou aplikaci. Poznamenejte si **adresu URL**vaší aplikace. Použijete ho ke konfiguraci registrace aplikace Azure Active Directory.
1. Vyberte **Azure Active Directory** > **Registrace aplikací** > **nové registrace**.
1. Na stránce **zaregistrovat aplikaci** zadejte **název** registrace vaší aplikace.
1. V **identifikátoru URI přesměrování**vyberte **Web** a zadejte `<app-url>/.auth/login/aad/callback`. Například, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Vyberte **Vytvořit**.
1. Po vytvoření registrace aplikace zkopírujte **ID aplikace (klienta)** a **ID adresáře (tenant)** pro pozdější použití.
1. Vyberte **branding**. Do pole **Adresa URL domovské stránky**zadejte adresu URL vaší aplikace App Service a vyberte **Uložit**.
1. Vyberte možnost zveřejnit > **sadu** **rozhraní API** . Vložte adresu URL vaší aplikace App Service a vyberte **Uložit**.

   > [!NOTE]
   > Tato hodnota je **identifikátor URI ID aplikace** pro registraci aplikace. Pokud vaše webová aplikace vyžaduje přístup k rozhraní API v cloudu, budete potřebovat **identifikátor URI ID aplikace** webové aplikace, když nakonfigurujete cloudový App Service prostředek. Tuto možnost můžete použít například v případě, že chcete, aby cloudová služba explicitně udělila přístup k webové aplikaci.

1. Vyberte **Přidat obor**.
   1. Do **název oboru**zadejte *user_impersonation*.
   1. Do textových polí zadejte název oboru souhlasu a popis, který mají uživatelé vidět na stránce s souhlasem. Zadejte například *přístup k aplikaci*. 
   1. Vyberte **Přidat obor**.
1. Volitelné Pokud chcete vytvořit tajný klíč klienta, vyberte **certifikáty & tajné klíče** > **nový tajný klíč klienta** > **Přidat**. Zkopírujte hodnotu tajného klíče klienta zobrazenou na stránce. Znovu se nezobrazí.
1. Volitelné Pokud chcete přidat víc **adres URL odpovědi**, vyberte **ověřování**.

### <a name="secrets"> </a>Povolení Azure Active Directory v aplikaci App Service

1. V [Azure Portal]vyhledejte a vyberte **App Services**a pak vyberte svou aplikaci. 
1. V levém podokně v části **Nastavení**vyberte **ověřování/autorizace** > **zapnuto**.
1. Volitelné Ve výchozím nastavení App Service ověřování umožňuje neověřený přístup k vaší aplikaci. Pokud chcete vyhovět ověřování uživatele, nastavte **akci, která se má provést, když se žádost neověřuje** , aby se **přihlásila pomocí Azure Active Directory**.
1. V části **Zprostředkovatelé ověřování**vyberte **Azure Active Directory**.
1. V **režimu správy**vyberte **upřesnit** a nakonfigurujte App Service ověřování podle následující tabulky:

    |Pole|Popis|
    |-|-|
    |ID klienta| Použijte **ID aplikace (klienta)** registrace aplikace. |
    |ID vystavitele| Použijte `https://login.microsoftonline.com/<tenant-id>`a nahraďte *\<ID tenanta >* s **ID adresáře (tenant)** registrace aplikace. |
    |Tajný kód klienta (volitelné)| Použijte tajný klíč klienta, který jste vygenerovali v registraci aplikace.|
    |Povolené cílové skupiny tokenů| Pokud se jedná o cloudovou nebo serverovou aplikaci a chcete z webové aplikace dovolit ověřovací tokeny, přidejte sem **identifikátor URI ID aplikace** webové aplikace. Nakonfigurované **ID klienta** se *vždycky* implicitně považuje za povolenou cílovou skupinu. |

2. Vyberte **OK**a pak vyberte **Uložit**.

Nyní jste připraveni použít Azure Active Directory pro ověřování ve vaší aplikaci App Service.

## <a name="configure-a-native-client-application"></a>Konfigurace nativní klientské aplikace

Nativní klienty můžete registrovat, aby bylo možné ověřování pomocí klientské knihovny, jako je například **Active Directory Authentication Library**.

1. V [Azure Portal]vyberte možnost **Active Directory** > **Registrace aplikací** > **nové registrace**.
1. Na stránce **zaregistrovat aplikaci** zadejte **název** registrace vaší aplikace.
1. V části **identifikátor URI přesměrování**vyberte **veřejný klient (mobilní & Desktop)** a zadejte adresu URL `<app-url>/.auth/login/aad/callback`. Například, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > V případě aplikace pro Windows použijte místo toho identifikátor [SID balíčku](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) .
1. Vyberte **Vytvořit**.
1. Po vytvoření registrace aplikace Zkopírujte hodnotu **ID aplikace (klienta)** .
1. Vyberte **oprávnění rozhraní API** > **přidejte oprávnění** > **Moje rozhraní API**.
1. Vyberte registraci aplikace, kterou jste vytvořili dříve pro App Service aplikaci. Pokud se registrace aplikace nezobrazuje, ujistěte se, že jste přidali obor **user_impersonation** v části [Vytvoření registrace aplikace ve službě Azure AD pro vaši aplikaci App Service](#register).
1. Vyberte **user_impersonation**a pak vyberte **Přidat oprávnění**.

Nyní jste nakonfigurovali nativní klientskou aplikaci, která má přístup k vaší aplikaci App Service.

## <a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
