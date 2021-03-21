---
title: Přístup k místním rozhraním API pomocí Azure Proxy aplikací služby AD
description: Proxy aplikace Azure Active Directory umožňuje nativním aplikacím bezpečně přistupovat k rozhraním API a obchodní logikě, které hostuje místní nebo cloudové virtuální počítače.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 9341646f32f6a2e05397b072d3f63186964fbd88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258978"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Zabezpečený přístup k místním rozhraním API pomocí Azure Proxy aplikací služby AD

Můžete mít rozhraní API pro obchodní logiku spuštěná místně nebo hostovaná na virtuálních počítačích v cloudu. Vaše nativní aplikace pro Android, iOS, Mac nebo Windows potřebují komunikovat s koncovými body rozhraní API, aby mohli používat data nebo poskytovat interakci s uživatelem. Azure Proxy aplikací služby AD a [Knihovna Microsoft Authentication Library (MSAL)](../azuread-dev/active-directory-authentication-libraries.md) umožňují vašim nativním aplikacím zabezpečený přístup k vašim místním rozhraním API. Proxy aplikací služby Azure Active Directory je rychlejší a bezpečnější řešení než otevření portů brány firewall a řízení ověřování a autorizace ve vrstvě aplikace.

Tento článek vás provede nastavením řešení Azure Proxy aplikací služby AD pro hostování služby webového rozhraní API, ke které mají přístup nativní aplikace.

## <a name="overview"></a>Přehled

Následující diagram znázorňuje tradiční způsob publikování místních rozhraní API. Tento přístup vyžaduje otevření příchozích portů 80 a 443.

![Tradiční přístup přes rozhraní API](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Následující diagram ukazuje, jak můžete pomocí Azure Proxy aplikací služby AD bezpečně publikovat rozhraní API bez nutnosti otevírat příchozí porty:

![Přístup k rozhraní API pro Azure Proxy aplikací služby AD](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure Proxy aplikací služby AD tvoří páteřní síť řešení, pracuje jako veřejný koncový bod pro přístup k rozhraní API a poskytuje ověřování a autorizaci. K rozhraním API můžete přistupovat z rozsáhlého pole platforem pomocí knihoven [Microsoft Authentication Library (MSAL)](../azuread-dev/active-directory-authentication-libraries.md) .

Vzhledem k tomu, že služba Azure Proxy aplikací služby AD ověřování a autorizace jsou postaveny na službě Azure AD, můžete použít podmíněný přístup Azure AD a zajistit tak, aby přístup k rozhraním API publikovaným pomocí proxy aplikací mohli jenom důvěryhodná zařízení. Použijte službu Azure AD JOIN nebo službu Azure AD Hybrid připojenou pro stolní počítače a Intune spravovanou pro zařízení. Můžete také využít výhod Azure Active Directory Premium funkcí jako Azure AD Multi-Factor Authentication a na základě strojového učení – zabezpečení [Azure Identity Protection](../identity-protection/overview-identity-protection.md).

## <a name="prerequisites"></a>Předpoklady

Pokud chcete postupovat podle tohoto návodu, budete potřebovat:

- Přístup správce k adresáři Azure pomocí účtu, který může vytvářet a registrovat aplikace
- Ukázkové webové rozhraní API a nativní klientské aplikace z [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp)

## <a name="publish-the-api-through-application-proxy"></a>Publikování rozhraní API prostřednictvím proxy aplikace

Pokud chcete publikovat rozhraní API mimo intranet prostřednictvím proxy aplikací, použijte stejný vzor jako při publikování webových aplikací. Další informace najdete v tématu [kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](application-proxy-add-on-premises-application.md).

Publikování webového rozhraní API SecretAPI prostřednictvím aplikačního proxy serveru:

1. Sestavte a publikujte vzorový projekt SecretAPI jako webovou aplikaci ASP.NET na místním počítači nebo intranetu. Ujistěte se, že máte přístup k webové aplikaci místně.

1. Na portálu [Azure Portal](https://portal.azure.com) vyberte **Azure Active Directory**. Pak vyberte **podnikové aplikace**.

1. V horní části stránky **podnikové aplikace – všechny aplikace** vyberte možnost **Nová aplikace**.

1. Na stránce **Přidat aplikaci** vyberte možnost **místní aplikace**. Zobrazí se stránka **Přidat vlastní místní aplikace** .

1. Pokud nemáte nainstalovaný konektor proxy aplikací, zobrazí se výzva k jeho instalaci. Vyberte **Stáhnout konektor proxy aplikací** a stáhněte a nainstalujte konektor.

1. Po instalaci konektoru proxy aplikací na stránce **Přidat vlastní místní aplikaci** :

   1. Vedle **pole název** zadejte *SecretAPI*.

   1. Do pole **interní adresa URL** zadejte adresu URL, kterou používáte pro přístup k rozhraní API v rámci intranetu.

   1. Ujistěte se, že **předběžné ověřování** je nastaveno na **Azure Active Directory**.

   1. V horní části stránky vyberte **Přidat** a počkejte, než se aplikace vytvoří.

   ![Přidat aplikaci API](./media/application-proxy-secure-api-access/3-add-api-app.png)

1. Na stránce **podnikové aplikace – všechny aplikace** vyberte aplikaci **SecretAPI** .

1. Na stránce **SecretAPI-přehled** vyberte v levém navigačním panelu možnost **vlastnosti** .

1. Nechcete, aby rozhraní API byla k dispozici koncovým **uživatelům na panelu aplikace** , proto nastavte možnost **viditelná pro uživatele** na hodnotu **ne** v dolní části stránky **vlastností** a potom vyberte **Uložit**.

   ![Neviditelná pro uživatele](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)

Webové rozhraní API jste publikovali prostřednictvím Azure Proxy aplikací služby AD. Nyní přidejte uživatele, kteří mají přístup k aplikaci.

1. Na stránce **SecretAPI-přehled** vyberte **uživatele a skupiny** v levém navigačním panelu.

1. Na stránce **Uživatelé a skupiny** vyberte **Přidat uživatele**.

1. Na stránce **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

1. Na stránce **Uživatelé a skupiny** vyhledejte a vyberte uživatele, kteří mají k aplikaci přístup, včetně aspoň sebe. Po výběru možnosti všichni uživatelé vyberte **Vybrat**.

   ![Vybrat a přiřadit uživatele](./media/application-proxy-secure-api-access/7-select-admin-user.png)

1. Zpátky na stránce **Přidat přiřazení** vyberte **přiřadit**.

> [!NOTE]
> Rozhraní API, která používají integrované ověřování systému Windows, mohou vyžadovat [Další kroky](./application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registrace nativní aplikace a udělení přístupu k rozhraní API

Nativní aplikace jsou programy vyvinuté k použití na konkrétní platformě nebo zařízení. Předtím, než se může vaše nativní aplikace připojit a získat přístup k rozhraní API, je nutné ho zaregistrovat ve službě Azure AD. Následující kroky ukazují, jak zaregistrovat nativní aplikaci a dát jí přístup k webovému rozhraní API, které jste publikovali prostřednictvím proxy aplikace.

Registrace nativní aplikace AppProxyNativeAppSample:

1. Na stránce **přehled** Azure Active Directory vyberte možnost **Registrace aplikací** a v horní části podokna **Registrace aplikací** vyberte možnost **Nová registrace**.

1. Na stránce **zaregistrovat aplikaci** :

   1. Do **pole název** zadejte *AppProxyNativeAppSample*.

   1. V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.

   1. V části **Adresa URL pro přesměrování** vyberte možnost **veřejný klient (mobilní & plocha)** a pak zadejte *https://login.microsoftonline.com/common/oauth2/nativeclient* .

   1. Vyberte **Registrovat** a počkejte, než se aplikace úspěšně zaregistruje.

      ![Registrace nové aplikace](./media/application-proxy-secure-api-access/8-create-reg-ga.png)

Aplikaci AppProxyNativeAppSample jste teď zaregistrovali v Azure Active Directory. Přístup k webovému rozhraní API SecretAPI vaší nativní aplikace:

1. Na stránce Azure Active Directory **Přehled**  >  **Registrace aplikací** vyberte aplikaci **AppProxyNativeAppSample** .

1. Na stránce **AppProxyNativeAppSample** v levém navigačním panelu vyberte **oprávnění rozhraní API** .

1. Na stránce **oprávnění rozhraní API** vyberte **Přidat oprávnění**.

1. Na stránce první **žádost o přístup k rozhraní API** vyberte kartu **rozhraní API moje organizace používá** a potom vyhledejte a vyberte **SecretAPI**.

1. Na stránce další **žádosti o oprávnění API** zaškrtněte políčko vedle **user_impersonation** a pak vyberte **Přidat oprávnění**.

    ![Výběr rozhraní API](./media/application-proxy-secure-api-access/10-secretapi-added.png)

1. Zpátky na stránce **oprávnění rozhraní API** můžete vybrat **udělit souhlas správce pro společnost Contoso** , aby ostatní uživatelé nemohli udělit individuálně souhlas s aplikací.

## <a name="configure-the-native-app-code"></a>Konfigurace nativního kódu aplikace

Posledním krokem je konfigurace nativní aplikace. Následující fragment kódu ze souboru *Form1. cs* v ukázkové aplikaci NativeClient způsobí, že knihovna MSAL získá token pro vyžádání volání rozhraní API a připojí ho jako nosič k hlavičce aplikace.

   ```
   // Acquire Access Token from AAD for Proxy Application
 IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }
 
if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application
  
  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

Pokud chcete nakonfigurovat nativní aplikaci pro připojení k Azure Active Directory a volání proxy aplikace API, aktualizujte zástupné hodnoty v souboru *App.config* ukázkové aplikace NativeClient hodnotami z Azure AD:

- Do pole vložte **ID adresáře (tenant)** `<add key="ida:Tenant" value="" />` . Tuto hodnotu (identifikátor GUID) můžete najít a zkopírovat ze stránky **Přehled** některé z vašich aplikací.

- Do pole vložte **ID aplikace AppProxyNativeAppSample (ID klienta)** `<add key="ida:ClientId" value="" />` . Tuto hodnotu (identifikátor GUID) můžete na stránce **přehledu** AppProxyNativeAppSample najít a zkopírovat.

- Do pole vložte **identifikátor URI pro přesměrování** AppProxyNativeAppSample `<add key="ida:RedirectUri" value="" />` . Tuto hodnotu (identifikátor URI) můžete najít a zkopírovat na stránce **ověřování** AppProxyNativeAppSample.

- Vložte **identifikátor URI ID aplikace** SecretAPI do `<add key="todo:TodoListResourceId" value="" />` pole. Tuto hodnotu (identifikátor URI) můžete najít a zkopírovat ze stránky **rozhraní API SecretAPI vystavit** .

- Vložte **adresu URL domovské stránky** SecretAPI do `<add key="todo:TodoListBaseAddress" value="" />` pole. Tuto hodnotu (adresu URL) můžete najít a zkopírovat ze stránky **brandingu** SecretAPI.

Po nakonfigurování parametrů Sestavte a spusťte nativní aplikaci. Když vyberete tlačítko **Přihlásit** , aplikace vám umožní přihlásit se a pak zobrazí obrazovku po úspěchu, která potvrdí, že se úspěšně připojil k SecretAPI.

![Snímek obrazovky se zobrazí jako tajný klíč zprávy P I úspěch a tlačítko OK.](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Další kroky

- [Kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Rychlý Start: Konfigurace klientské aplikace pro přístup k webovým rozhraním API](../develop/quickstart-configure-app-access-web-apis.md)
- [Jak povolit nativní klientské aplikace pro interakci s proxy aplikacemi](application-proxy-configure-native-client-application.md)