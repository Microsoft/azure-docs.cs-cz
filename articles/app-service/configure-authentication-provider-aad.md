---
title: Konfigurace ověřování Azure AD
description: Naučte se nakonfigurovat Azure Active Directory ověřování jako zprostředkovatele identity pro App Service nebo Azure Functions aplikaci.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: b1254e7db0e62d08ea2a3d6d30f2abd379675c55
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078311"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Konfigurace App Service nebo Azure Functions aplikace pro použití přihlášení Azure AD

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

V tomto článku se dozvíte, jak nakonfigurovat ověřování pro Azure App Service nebo Azure Functions, aby se vaše aplikace přihlášena uživatelům pomocí [platformy Microsoft Identity](../active-directory/develop/v2-overview.md) (Azure AD) jako poskytovatele ověřování.

Funkce ověřování App Service může automaticky vytvořit registraci aplikace s platformou Microsoft identity. Můžete také použít registraci, kterou vy nebo Správce adresáře vytváříte samostatně.

- [Automaticky vytvořit novou registraci aplikace](#express)
- [Použít existující registraci vytvořenou samostatně](#advanced)

> [!NOTE]
> Možnost vytvoření nové registrace není pro cloudy státní správy k dispozici. Místo toho [Definujte registraci samostatně](#advanced).

## <a name="create-a-new-app-registration-automatically"></a><a name="express"> </a>Automaticky vytvořit novou registraci aplikace

Tato možnost je navržena tak, aby povolila jednoduché ověřování a vyžadovala jen několik kliknutí.

1. Přihlaste se k [Azure Portal] a přejděte do aplikace.
1. V nabídce na levé straně vyberte **ověřování** . Klikněte na **Přidat zprostředkovatele identity**.
1. V rozevíracím seznamu Zprostředkovatel identity vyberte **Microsoft** . Ve výchozím nastavení je vybraná možnost pro vytvoření nové registrace. Můžete změnit název registrace nebo podporované typy účtů.

    Vytvoří se tajný klíč klienta, který se uloží jako [nastavení aplikace](./configure-common.md#configure-app-settings) typu rychlé pozice s názvem `MICROSOFT_PROVIDER_AUTHENTICATION_SECRET` . Toto nastavení můžete později aktualizovat, aby se používaly [Key Vault odkazy](./app-service-key-vault-references.md) , pokud chcete tajný klíč spravovat v Azure Key Vault.

1. Pokud se jedná o prvního zprostředkovatele identity nakonfigurovaného pro aplikaci, zobrazí se také výzva s oddílem **nastavení ověřování App Service** . V opačném případě se můžete přesunout k dalšímu kroku.
    
    Tyto možnosti určují, jak vaše aplikace reaguje na neověřené požadavky, a výchozí výběry přesměrují všechny požadavky na přihlášení pomocí tohoto nového poskytovatele. Přizpůsobením tohoto chování teď můžete změnit nebo upravit toto nastavení později z hlavní obrazovky pro **ověřování** výběrem možnosti **Upravit** vedle **nastavení ověřování**. Další informace o těchto možnostech najdete v tématu [tok ověřování](overview-authentication-authorization.md#authentication-flow).

1. Volitelné Klikněte na **Další: oprávnění** a přidejte libovolné obory, které aplikace potřebuje. Ty se přidají k registraci aplikace, ale můžete je také později změnit.
1. Klikněte na **Přidat**.

Nyní jste připraveni použít platformu Microsoft identity pro ověřování ve vaší aplikaci. Poskytovatel bude uveden na obrazovce **ověřování** . Odtud můžete tuto konfiguraci poskytovatele upravit nebo odstranit.

Příklad konfigurace přihlášení Azure AD pro webovou aplikaci, která přistupuje k Azure Storage a Microsoft Graph, najdete v [tomto kurzu](scenario-secure-app-authentication-app-service.md).

## <a name="use-an-existing-registration-created-separately"></a><a name="advanced"> </a>Použít existující registraci vytvořenou samostatně

Můžete také manuálně zaregistrovat aplikaci pro platformu Microsoft identity, přizpůsobit registraci a nakonfigurovat App Service ověřování s použitím registračních údajů. To je užitečné, například pokud chcete použít registraci aplikace z jiného tenanta Azure AD, než jakou vaše aplikace používá.

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

1. Přihlaste se k [Azure Portal] a přejděte do aplikace.
1. V nabídce na levé straně vyberte **ověřování** . Klikněte na **Přidat zprostředkovatele identity**.
1. V rozevíracím seznamu Zprostředkovatel identity vyberte **Microsoft** .
1. Pro **typ registrace aplikace** se můžete rozhodnout vybrat **existující registraci aplikace v tomto adresáři** , která bude automaticky shromažďovat potřebné informace o aplikaci. Pokud vaše registrace pochází z jiného tenanta nebo nemáte oprávnění k zobrazení objektu pro registraci, vyberte **zadat podrobnosti existující registrace aplikace**. Pro tuto možnost budete muset vyplnit následující konfigurační údaje:

    |Pole|Description|
    |-|-|
    |ID aplikace (klienta)| Použijte **ID aplikace (klienta)** registrace aplikace. |
    |Tajný kód klienta (volitelné)| Použijte tajný klíč klienta, který jste vygenerovali v registraci aplikace. V případě tajného klíče klienta se používá hybridní tok a App Service vrátí tokeny pro přístup a aktualizaci. Pokud není nastaven tajný klíč klienta, je použit implicitní tok a je vrácen pouze token ID. Tyto tokeny posílá poskytovatel a ukládají se do úložiště tokenů EasyAuth.|
    |Adresa URL vydavatele| Pomocí `<authentication-endpoint>/<tenant-id>/v2.0` a nahraďte *\<authentication-endpoint>* [koncovým bodem ověřování pro vaše cloudové prostředí](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (např. " https://login.microsoftonline.com " pro globální Azure ") nahraďte *\<tenant-id>* **ID adresáře (tenanta)** , ve kterém se vytvořila registrace aplikace. Tato hodnota se používá k přesměrování uživatelů do správného tenanta Azure AD a také ke stažení odpovídajících metadat k určení vhodného podpisového klíče tokenu a hodnoty deklarace vystavitele tokenu. U aplikací, které používají Azure AD V1 a pro aplikace Azure Functions, `/v2.0` v adrese URL vynechejte.|
    |Povolené cílové skupiny tokenů| Pokud se jedná o cloudovou nebo serverovou aplikaci a chcete z webové aplikace dovolit ověřovací tokeny, přidejte sem **identifikátor URI ID aplikace** webové aplikace. Nakonfigurované **ID klienta** se *vždycky* implicitně považuje za povolenou cílovou skupinu.|

    Tajný kód klienta bude uložen jako [nastavení aplikace](./configure-common.md#configure-app-settings) typu rychlé rozhraní s názvem `MICROSOFT_PROVIDER_AUTHENTICATION_SECRET` . Toto nastavení můžete později aktualizovat, aby se používaly [Key Vault odkazy](./app-service-key-vault-references.md) , pokud chcete tajný klíč spravovat v Azure Key Vault.

1. Pokud se jedná o prvního zprostředkovatele identity nakonfigurovaného pro aplikaci, zobrazí se také výzva s oddílem **nastavení ověřování App Service** . V opačném případě se můžete přesunout k dalšímu kroku.
    
    Tyto možnosti určují, jak vaše aplikace reaguje na neověřené požadavky, a výchozí výběry přesměrují všechny požadavky na přihlášení pomocí tohoto nového poskytovatele. Přizpůsobením tohoto chování teď můžete změnit nebo upravit toto nastavení později z hlavní obrazovky pro **ověřování** výběrem možnosti **Upravit** vedle **nastavení ověřování**. Další informace o těchto možnostech najdete v tématu [tok ověřování](overview-authentication-authorization.md#authentication-flow).

1. Klikněte na **Přidat**.

Nyní jste připraveni použít platformu Microsoft identity pro ověřování ve vaší aplikaci. Poskytovatel bude uveden na obrazovce **ověřování** . Odtud můžete tuto konfiguraci poskytovatele upravit nebo odstranit.

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
