---
title: Konfigurace ověřování Azure AD
description: Naučte se nakonfigurovat Azure Active Directory ověřování jako zprostředkovatele identity pro App Service nebo Azure Functions aplikaci.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 0f028f264d02d7300bb888e2053708ef6b06ea51
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721558"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Konfigurace App Service nebo Azure Functions aplikace pro použití přihlášení Azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto článku se dozvíte, jak nakonfigurovat ověřování pro Azure App Service nebo Azure Functions, aby se vaše aplikace přihlášena uživatelům pomocí Azure Active Directory (Azure AD) jako poskytovatele ověřování.

## <a name="configure-with-express-settings"></a><a name="express"> </a>Konfigurace pomocí expresního nastavení

Možnost **expresní** je navržená tak, aby bylo snadné povolit ověřování a vyžaduje jenom několik kliknutí.

Pomocí expresního nastavení se automaticky vytvoří registrace aplikace, která používá koncový bod Azure Active Directory v1. Pokud chcete použít [Azure Active Directory v 2.0](../active-directory/develop/v2-overview.md) (včetně [MSAL](../active-directory/develop/msal-overview.md)), postupujte podle [pokynů pro pokročilou konfiguraci](#advanced).

> [!NOTE]
> Možnost **Express** není pro cloudy státní správy k dispozici.

Pokud chcete povolit ověřování pomocí možnosti **Express** , postupujte takto:

1. V [Azure Portal]vyhledejte a vyberte **App Services** a pak vyberte svou aplikaci.
2. V levém navigačním panelu vyberte **ověřování/autorizace**  >  **na**.
3. Vyberte **Azure Active Directory**  >  **Express**.

   Pokud chcete místo toho zvolit existující registraci aplikace:

   1. Zvolte **Vybrat existující aplikaci AD** a pak klikněte na **aplikace Azure AD**.
   2. Vyberte existující registraci aplikace a klikněte na **OK**.

4. Výběrem **OK** zaregistrujete aplikaci App Service do Azure Active Directory. Vytvoří se nová registrace aplikace.

    ![Expresní nastavení v Azure Active Directory](./media/configure-authentication-provider-aad/express-settings.png)

5. Volitelné Ve výchozím nastavení App Service poskytuje ověřování, ale neomezuje autorizovaný přístup k obsahu a rozhraním API vašeho webu. Musíte autorizovat uživatele v kódu vaší aplikace. Pokud chcete omezit přístup k aplikacím jenom na uživatele ověřené nástrojem Azure Active Directory, nastavte **akci, která se má provést, když se žádost neověřuje** , aby se **přihlásila pomocí Azure Active Directory**. Když nastavíte tuto funkci, aplikace vyžaduje ověření všech požadavků. Také přesměruje všechna neověřená na Azure Active Directory pro ověřování.

    > [!CAUTION]
    > Omezení přístupu tímto způsobem se vztahuje na všechna volání aplikace, která nemusí být žádoucí pro aplikace, které mají veřejně dostupnou domovskou stránku, stejně jako v mnoha aplikacích s jednou stránkou. U takových aplikací může být upřednostňována možnost **povolení anonymních požadavků (žádná akce)** , přičemž aplikace se ručně spouští samotné přihlášení. Další informace najdete v tématu [tok ověřování](overview-authentication-authorization.md#authentication-flow).
6. Vyberte **Uložit**.

Příklad konfigurace přihlášení Azure AD pro webovou aplikaci, která přistupuje k Azure Storage a Microsoft Graph, najdete v [tomto kurzu](scenario-secure-app-authentication-app-service.md).

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>Konfigurace s pokročilým nastavením

Aby mohla služba Azure AD fungovat jako poskytovatel ověřování pro vaši aplikaci, musíte aplikaci zaregistrovat. Možnost Express to provede automaticky. Možnost Upřesnit umožňuje manuálně zaregistrovat aplikaci, přizpůsobit registraci a ruční vkládání podrobností o registraci zpět do App Service. To je užitečné například v případě, že chcete použít registraci aplikace z jiného tenanta Azure AD, než v jaké je vaše App Service.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Vytvoření registrace aplikace v Azure AD pro vaši aplikaci App Service

Nejprve vytvoříte registraci aplikace. V takovém případě shromážděte následující informace, které budete později potřebovat při konfiguraci ověřování v aplikaci App Service:

- ID klienta
- ID tenanta
- Tajný kód klienta (volitelné)
- Identifikátor URI ID aplikace

K registraci aplikace proveďte následující kroky:

1. Přihlaste se k [Azure Portal], vyhledejte a vyberte **App Services** a pak vyberte svou aplikaci. Poznamenejte si **adresu URL** vaší aplikace. Použijete ho ke konfiguraci registrace aplikace Azure Active Directory.
1. V nabídce portálu vyberte **Azure Active Directory**, přejděte na kartu **Registrace aplikací** a vyberte **Nová registrace**.
1. Na stránce **zaregistrovat aplikaci** zadejte **název** registrace vaší aplikace.
1. V **identifikátoru URI přesměrování** vyberte **Web** a zadejte `<app-url>/.auth/login/aad/callback` . Například, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Vyberte **Zaregistrovat**.
1. Po vytvoření registrace aplikace zkopírujte **ID aplikace (klienta)** a **ID adresáře (tenant)** pro pozdější použití.
1. Vyberte **Ověřování**. V části **implicitní udělení** povolte **tokeny ID** , aby bylo možné přihlašovat OpenID připojit uživatele z App Service.
1. Volitelné Vyberte **branding**. Do pole **Adresa URL domovské stránky** zadejte adresu URL vaší aplikace App Service a vyberte **Uložit**.
1. Vyberte **zveřejnit sadu rozhraní API**  >  . V případě aplikace pro jednoho tenanta vložte adresu URL vaší aplikace App Service a vyberte **Uložit** a pro víceklientské aplikace, vložte adresu URL, která je založená na jedné z ověřených domén klienta, a pak vyberte **Uložit**.

   > [!NOTE]
   > Tato hodnota je **identifikátor URI ID aplikace** pro registraci aplikace. Pokud vaše webová aplikace vyžaduje přístup k rozhraní API v cloudu, budete potřebovat **identifikátor URI ID aplikace** webové aplikace, když nakonfigurujete cloudový App Service prostředek. Tuto možnost můžete použít například v případě, že chcete, aby cloudová služba explicitně udělila přístup k webové aplikaci.

1. Vyberte **Přidat obor**.
   1. Do **název oboru** zadejte *user_impersonation*.
   1. Do textových polí zadejte název oboru souhlasu a popis, který mají uživatelé vidět na stránce s souhlasem. Zadejte například *přístup k aplikaci*.
   1. Vyberte **Přidat obor**.
1. Volitelné Pokud chcete vytvořit tajný klíč klienta, vyberte **certifikáty & tajné klíče**  >  **nový tajný klíč klienta**  >  **Přidat**. Zkopírujte hodnotu tajného klíče klienta zobrazenou na stránce. Znovu se nezobrazí.
1. Volitelné Pokud chcete přidat víc **adres URL odpovědi**, vyberte **ověřování**.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Povolení Azure Active Directory v aplikaci App Service

1. V [Azure Portal]vyhledejte a vyberte **App Services** a pak vyberte svou aplikaci.
1. V levém podokně v části **Nastavení** vyberte **ověřování/autorizace**  >  **pro**.
1. Volitelné Ve výchozím nastavení App Service ověřování umožňuje neověřený přístup k vaší aplikaci. Pokud chcete vyhovět ověřování uživatele, nastavte **akci, která se má provést, když se žádost neověřuje** , aby se **přihlásila pomocí Azure Active Directory**.
1. V části **Zprostředkovatelé ověřování** vyberte **Azure Active Directory**.
1. V **režimu správy** vyberte **upřesnit** a nakonfigurujte App Service ověřování podle následující tabulky:

    |Pole|Description|
    |-|-|
    |ID klienta| Použijte **ID aplikace (klienta)** registrace aplikace. |
    |Adresa URL vydavatele| Pomocí `<authentication-endpoint>/<tenant-id>/v2.0` a nahraďte *\<authentication-endpoint>* [koncovým bodem ověřování pro vaše cloudové prostředí](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (např. " https://login.microsoftonline.com " pro globální Azure ") nahraďte *\<tenant-id>* **ID adresáře (tenanta)** , ve kterém se vytvořila registrace aplikace. Tato hodnota se používá k přesměrování uživatelů do správného tenanta Azure AD a také ke stažení odpovídajících metadat k určení vhodného podpisového klíče tokenu a hodnoty deklarace vystavitele tokenu. U aplikací, které používají Azure AD V1 a pro aplikace Azure Functions, `/v2.0` v adrese URL vynechejte.|
    |Tajný kód klienta (volitelné)| Použijte tajný klíč klienta, který jste vygenerovali v registraci aplikace. V případě tajného klíče klienta se používá hybridní tok a App Service vrátí tokeny pro přístup a aktualizaci. Pokud není nastaven tajný klíč klienta, je použit implicitní tok a je vrácen pouze token ID. Tyto tokeny posílá poskytovatel a ukládají se do úložiště tokenů EasyAuth.|
    |Povolené cílové skupiny tokenů| Pokud se jedná o cloudovou nebo serverovou aplikaci a chcete z webové aplikace dovolit ověřovací tokeny, přidejte sem **identifikátor URI ID aplikace** webové aplikace. Nakonfigurované **ID klienta** se *vždycky* implicitně považuje za povolenou cílovou skupinu.|

2. Vyberte **OK** a pak vyberte **Uložit**.

Nyní jste připraveni použít Azure Active Directory pro ověřování ve vaší aplikaci App Service.

## <a name="configure-client-apps-to-access-your-app-service"></a>Konfigurace klientských aplikací pro přístup k App Service

V předchozí části jste zaregistrovali své App Service nebo funkci Azure pro ověřování uživatelů. V této části se dozvíte, jak registrovat nativní aplikace klienta nebo démona tak, aby mohly vyžadovat přístup k rozhraním API vystaveným vaším App Service jménem uživatelů nebo sami sebe. Provedení kroků v této části není vyžadováno, pokud chcete pouze ověřovat uživatele.

### <a name="native-client-application"></a>Nativní klientská aplikace

Nativní klienty můžete zaregistrovat pro vyžádání přístupu k rozhraním API vaší App Service aplikace jménem přihlášeného uživatele.

1. V [Azure Portal]vyberte možnost **Active Directory**  >  **Registrace aplikací**  >  **Nová registrace**.
1. Na stránce **zaregistrovat aplikaci** zadejte **název** registrace vaší aplikace.
1. V části **identifikátor URI pro přesměrování** vyberte **veřejný klient (mobilní & Desktop)** a zadejte adresu URL `<app-url>/.auth/login/aad/callback` . Například, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > V případě aplikace Microsoft Store použijte místo toho identifikátor [SID balíčku](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#package-sid) .
1. Vyberte **Vytvořit**.
1. Po vytvoření registrace aplikace Zkopírujte hodnotu **ID aplikace (klienta)**.
1. Vyberte **oprávnění API**  >  **Přidat oprávnění**  >  **Moje rozhraní API**.
1. Vyberte registraci aplikace, kterou jste vytvořili dříve pro App Service aplikaci. Pokud se registrace aplikace nezobrazuje, ujistěte se, že jste přidali obor **user_impersonation** v části [Vytvoření registrace aplikace ve službě Azure AD pro vaši aplikaci App Service](#register).
1. V části **delegovaná oprávnění** vyberte **user_impersonation** a pak vyberte **Přidat oprávnění**.

Nyní jste nakonfigurovali nativní klientskou aplikaci, která může požádat o přístup k aplikaci App Service jménem uživatele.

### <a name="daemon-client-application-service-to-service-calls"></a>Klientská aplikace démona (volání služby pro službu)

Vaše aplikace může získat token pro volání webového rozhraní API hostovaného ve vaší App Service nebo aplikaci Function App jménem sebe sama (ne jménem uživatele). Tento scénář je vhodný pro neinteraktivní aplikace démona, které provádějí úlohy bez přihlášeného uživatele. Používá standardní udělení [přihlašovacích údajů klienta](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) OAuth 2,0.

1. V [Azure Portal]vyberte možnost **Active Directory**  >  **Registrace aplikací**  >  **Nová registrace**.
1. Na stránce **zaregistrovat aplikaci** zadejte **název** pro vaši registraci aplikace démona.
1. V případě aplikace démona není nutné identifikátor URI přesměrování, takže ho můžete ponechat prázdné.
1. Vyberte **Vytvořit**.
1. Po vytvoření registrace aplikace Zkopírujte hodnotu **ID aplikace (klienta)**.
1. Vyberte **certifikáty & tajných klíčů**  >  **nový tajný klíč klienta**  >  **Přidat**. Zkopírujte hodnotu tajného klíče klienta zobrazenou na stránce. Znovu se nezobrazí.

[Pomocí ID klienta a tajného klíče klienta teď můžete požádat o přístupový token](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) tím, že nastavíte `resource` parametr na **identifikátor URI ID aplikace** cílové aplikace. Výsledný přístupový token se pak dá předvést cílové aplikaci pomocí standardní [hlavičky autorizace OAuth 2,0](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource)a App Service ověřování/autorizace ověří a použije token jako obvykle, což znamená, že volající (aplikace v tomto případě není uživatel) ověřený.

V současné době to umožňuje _všem_ klientským aplikacím v Tenantovi služby Azure AD požádat o přístupový token a ověřit ho v cílové aplikaci. Pokud chcete vymáhat _autorizaci_ , aby povolovala jenom některé klientské aplikace, musíte provést nějakou další konfiguraci.

1. V manifestu registrace aplikace [Definujte roli aplikace](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) , která představuje App Service nebo aplikace Function App, kterou chcete chránit.
1. V případě registrace aplikace představující klienta, který musí být autorizovaný, vyberte **oprávnění rozhraní API**  >  **Přidat oprávnění**  >  **Moje rozhraní API**.
1. Vyberte registraci aplikace, kterou jste vytvořili dříve. Pokud se registrace aplikace nezobrazuje, ujistěte se, že jste [přidali roli aplikace](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md).
1. V části **oprávnění aplikace** vyberte roli aplikace, kterou jste vytvořili dříve, a pak vyberte **Přidat oprávnění**.
1. Nezapomeňte kliknout na **udělit souhlas správce** , aby se autorizace klientské aplikace mohla požádat o oprávnění.
1. Podobně jako v předchozím scénáři (před přidáním rolí) teď můžete [požádat o přístupový token](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) pro stejný cíl `resource` a přístupový token bude obsahovat `roles` deklaraci identity obsahující role aplikace, které byly autorizovány pro klientskou aplikaci.
1. V rámci cílového App Service nebo kódu aplikace Function app teď můžete ověřit, že se v tokenu nachází očekávané role (neprovádí se App Service ověřováním/autorizací). Další informace najdete v tématu [přístup k deklaracím uživatelů](app-service-authentication-how-to.md#access-user-claims).

Nyní jste nakonfigurovali klientskou aplikaci démona, která má přístup k vaší aplikaci App Service pomocí vlastní identity.

## <a name="best-practices"></a>Osvědčené postupy

Bez ohledu na konfiguraci, kterou použijete k nastavení ověřování, se následující osvědčené postupy zachovají, že váš tenant a aplikace budou bezpečnější:

- Poskytněte každé aplikaci App Service vlastní oprávnění a souhlas.
- Nakonfigurujte každou aplikaci App Service s vlastní registrací.
- Nepoužívejte sdílení oprávnění mezi prostředími pomocí samostatných registrací aplikací pro samostatné sloty nasazení. Při testování nového kódu Tento postup může přispět k tomu, aby se zabránilo problémům v ovlivnění produkční aplikace.

## <a name="next-steps"></a><a name="related-content"> </a>Další kroky

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [Kurz: ověřování a autorizace uživatelů ve webové aplikaci, která přistupuje k Azure Storage a Microsoft Graph](scenario-secure-app-authentication-app-service.md)
* [Kurz: Komplexní ověřování a autorizace uživatelů v Azure App Service](tutorial-auth-aad.md)
<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
