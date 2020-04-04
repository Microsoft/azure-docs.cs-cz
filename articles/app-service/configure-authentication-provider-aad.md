---
title: Konfigurace ověřování Azure AD
description: Přečtěte si, jak nakonfigurovat ověřování Azure Active Directory jako poskytovatele identity pro vaši aplikaci App Service nebo Azure Functions.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 09/03/2019
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: dbbe58df4f1cfe93555b494e525fad18f5b02664
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632575"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Konfigurace aplikace App Service nebo aplikace Azure Functions tak, aby používaly přihlášení Azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Tento článek ukazuje, jak nakonfigurovat Azure App Service nebo Azure Functions používat Azure Active Directory (Azure AD) jako zprostředkovatele ověřování.

> [!NOTE]
> V současné době [azure active directory v2.0](../active-directory/develop/v2-overview.md) (včetně [MSAL)](../active-directory/develop/msal-overview.md)není podporována pro Azure App Service a Azure functions. Zkontrolujte, zda neobsahuje aktualizace.
>

Při nastavování aplikace a ověřování postupujte podle těchto doporučených postupů:

- Udělte každé aplikaci App Service vlastní oprávnění a souhlas.
- Nakonfigurujte každou aplikaci služby App Service s vlastní registrací.
- Vyhněte se sdílení oprávnění mezi prostředími pomocí samostatných registrací aplikací pro samostatné sloty nasazení. Při testování nového kódu, tento postup může pomoci zabránit problémům ovlivnění produkční aplikace.

## <a name="configure-with-express-settings"></a><a name="express"> </a>Konfigurace s expresním nastavením

> [!NOTE]
> Možnost **Express** není k dispozici pro vládní cloudy. 

1. Na [webu Azure Portal]vyhledejte a vyberte **Služby aplikací**a vyberte aplikaci.
2. V levém navigačním panelu vyberte **možnost Ověřování / Autorizace** > **zapnuto**.
3. Vyberte **možnost Azure Active Directory** > **Express**.

   Pokud chcete místo toho zvolit existující registraci aplikace:

   1. Zvolte **Vybrat existující aplikaci AD**a klikněte na **Azure AD App**.
   2. Zvolte existující registraci aplikace a klepněte na **tlačítko OK**.

3. Výběrem **možnosti OK** zaregistrujete aplikaci App Service ve službě Azure Active Directory. Vytvoří se nová registrace aplikace.
   
    ![Expresní nastavení ve službě Azure Active Directory](./media/configure-authentication-provider-aad/express-settings.png)
   
4. (Nepovinné) Ve výchozím nastavení služba App Service poskytuje ověřování, ale neomezuje oprávněný přístup k obsahu webu a souborům API. V kódu aplikace musíte autorizovat uživatele. Chcete-li omezit přístup k aplikacím jenom na uživatele ověřené službou Azure Active Directory, nastavte **akci, která se má provést, když se požadavek neověří,** aby se **přihlásila pomocí služby Azure Active Directory**. Když nastavíte tuto funkci, vaše aplikace vyžaduje, aby byly ověřeny všechny požadavky. Také přesměruje všechny neověřené do služby Azure Active Directory pro ověřování.

    > [!CAUTION]
    > Omezení přístupu tímto způsobem platí pro všechna volání do vaší aplikace, což nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, jako v mnoha jednostránkových aplikacích. Pro takové aplikace **povolit anonymní požadavky (žádná akce)** může být upřednostňována, s aplikací ručně spuštění přihlášení sám. Další informace naleznete v [tématu Ověřování toku](overview-authentication-authorization.md#authentication-flow).
5. Vyberte **Uložit**.

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>Konfigurace s upřesňujícími nastaveními

Nastavení aplikace můžete nakonfigurovat ručně, pokud chcete použít registraci aplikace z jiného klienta Azure AD. Dokončení této vlastní konfigurace:

1. Vytvořte registraci ve službě Azure AD.
2. Zadejte některé registrační údaje služby App Service.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Vytvoření registrace aplikace ve službě Azure AD pro aplikaci App Service

Při konfiguraci aplikace App Service budete potřebovat následující informace:

- ID klienta
- ID tenanta
- Tajný klíč klienta (nepovinné)
- Identifikátor URI ID aplikace

Proveďte následující kroky:

1. Přihlaste se na [portál Azure]Portal , vyhledejte a vyberte **App Services**a vyberte aplikaci. Poznamenejte si **adresu URL**aplikace . Budete ji používat ke konfiguraci registrace aplikace Azure Active Directory.
1. Vyberte**Registrace** > aplikací **Azure Active Directory** > **Nová registrace**.
1. Na stránce **Registrace aplikace** zadejte **název** registrace aplikace.
1. V **identifikátoru URI přesměrování**vyberte možnost **Web** a typ `<app-url>/.auth/login/aad/callback`. Například, `https://contoso.azurewebsites.net/.auth/login/aad/callback`. 
1. Vyberte **Vytvořit**.
1. Po vytvoření registrace aplikace zkopírujte **ID aplikace (klienta)** a **ID adresáře (klienta)** na později.
1. Vyberte **Ověřování**. V **části Implicitní udělení**povolte **tokeny ID,** aby bylo možné povolit přihlášení uživatelů OpenID Connect ze služby App Service.
1. (Nepovinné) Vyberte **branding**. V **adrese URL domovské stránky**zadejte adresu URL aplikace App Service a vyberte **Uložit**.
1. Vyberte vystavit**sadu** **rozhraní API** > . U aplikace s jedním klientem vložte do adresy URL aplikace App Service a vyberte **Uložit** a pro aplikaci pro více klientů vložte do adresy URL, která je založená na jedné z domén ověřených klientem, a pak vyberte **Uložit**.

   > [!NOTE]
   > Tato hodnota je **identifikátor URI ID aplikace** registrace aplikace. Pokud vaše webová aplikace vyžaduje přístup k rozhraní API v cloudu, budete při konfiguraci cloudové služby App Service potřebovat **identifikátor URI ID aplikace** webové aplikace. Můžete použít například pokud chcete, aby cloudová služba explicitně udělit přístup k webové aplikaci.

1. Vyberte **Přidat obor**.
   1. Do **pole Název oboru**zadejte *user_impersonation*.
   1. Do textových polí zadejte název a popis oboru souhlasu, který mají uživatelé zobrazit na stránce souhlasu. Zadejte například *aplikaci Access my*. 
   1. Vyberte **přidat obor**.
1. (Nepovinné) Chcete-li vytvořit tajný klíč klienta, vyberte **možnost Certifikáty & tajných kódů** > **Nový tajný klíč klienta** > **Přidat**. Zkopírujte hodnotu tajného klíče klienta zobrazenou na stránce. Už se to znovu neukáže.
1. (Nepovinné) Chcete-li přidat více **adres URL pro odpověď**, vyberte možnost **Ověřování**.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Povolení služby Azure Active Directory v aplikaci App Service

1. Na [webu Azure Portal]vyhledejte a vyberte **Služby aplikací**a vyberte aplikaci. 
1. V levém podokně vyberte v části **Nastavení**možnost **Ověřování / Autorizace** > **zapnuto**.
1. (Nepovinné) Ve výchozím nastavení ověřování služby App Service umožňuje neověřený přístup k vaší aplikaci. Chcete-li vynutit ověřování uživatelů, nastavte **akci, která má být v případě, že požadavek není ověřen, přihlaste** se **pomocí služby Azure Active Directory**.
1. V části **Zprostředkovatelé ověřování**vyberte **službu Azure Active Directory**.
1. V **režimu správy**vyberte **Upřesnit** a nakonfigurujte ověřování služby App Service podle následující tabulky:

    |Pole|Popis|
    |-|-|
    |ID klienta| Použijte **ID aplikace (klienta)** registrace aplikace. |
    |Adresa URL vystavittele| Použijte `https://login.microsoftonline.com/<tenant-id>`a * \<nahraďte>id klienta* **ID adresáře (tenanta)** registrace aplikace. Tato hodnota se používá k přesměrování uživatelů na správného klienta Azure AD, stejně jako ke stažení příslušných metadat k určení příslušné klíče podpisu tokenu a hodnotu deklarace vystavitele tokenu například. |
    |Tajný klíč klienta (volitelné)| Použijte tajný klíč klienta, který jste vygenerovali při registraci aplikace.|
    |Povolené cílové skupiny tokenů| Pokud se jedná o cloudovou nebo serverovou aplikaci a chcete povolit ověřovací tokeny z webové aplikace, přidejte identifikátor **URI ID aplikace** webové aplikace sem. Nakonfigurované **ID klienta** je *vždy* implicitně považováno za povolenou cílovou skupinu. |

2. Vyberte **OK**a pak vyberte **Uložit**.

Teď jste připraveni používat Azure Active Directory pro ověřování v aplikaci App Service.

## <a name="configure-a-native-client-application"></a>Konfigurace nativní klientské aplikace

Můžete zaregistrovat nativní klienty a povolit tak ověřování na webová rozhraní API hostovaná ve vaší aplikaci pomocí klientské knihovny, jako je **například Knihovna ověřování služby Active Directory**.

1. Na [webu Azure Portal]vyberte**Registrace aplikací Služby** >  **Active Directory** > **Nová registrace**.
1. Na stránce **Registrace aplikace** zadejte **název** registrace aplikace.
1. V **identifikátoru URI přesměrování**vyberte možnost Veřejný klient **(mobilní & plochy)** a zadejte adresu URL `<app-url>/.auth/login/aad/callback`. Například, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > Pro aplikaci Microsoft Store použijte [balíček SID](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) jako identifikátor URI.
1. Vyberte **Vytvořit**.
1. Po vytvoření registrace aplikace zkopírujte hodnotu **ID aplikace (klienta).**
1. Vyberte **oprávnění** > rozhraní**API: Přidání oprávnění** > **Moje rozhraní API**.
1. Vyberte registraci aplikace, kterou jste pro aplikaci App Service vytvořili dříve. Pokud registraci aplikace nevidíte, ujistěte se, že jste přidali **user_impersonation** oboru v [tématu Vytvoření registrace aplikace ve službě Azure AD pro aplikaci App Service](#register).
1. Vyberte **user_impersonation**a pak **vyberte Přidat oprávnění**.

Nyní jste nakonfigurovali nativní klientskou aplikaci, která může přistupovat k vaší aplikaci App Service jménem uživatele.

## <a name="next-steps"></a><a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[portál Azure]: https://portal.azure.com/
