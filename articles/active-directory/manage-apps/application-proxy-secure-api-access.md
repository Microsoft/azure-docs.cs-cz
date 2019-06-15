---
title: Přístup k místním rozhraním API pomocí Azure AD Application Proxy
description: Proxy aplikací Azure Active Directory umožňuje nativní aplikace pro zabezpečený přístup k rozhraní API a obchodní logiky, která hostujete na místním nebo na cloudových virtuálních počítačích.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: c2b99525e3d0a61c02dc502fcd0927ea65993e5b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108493"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Zabezpečený přístup k místním rozhraním API pomocí Azure AD Application Proxy

Může mít obchodní logiky rozhraní API spuštěné v místním nebo hostovaný na virtuálních počítačích v cloudu. Vaše nativní aplikace pro Android, iOS, Mac nebo Windows potřebují komunikovat s koncových bodů rozhraní API dat nebo interakci uživatele. Proxy aplikací Azure AD a [Azure Active Directory Authentication knihovny (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) umožňují nativní aplikace bezpečný přístup k rozhraní API místní. Azure Active Directory Application Proxy je rychlejší a bezpečnější řešení než otevřít porty brány firewall a řízení ověřování a autorizace ve vrstvě aplikací. 

Tento článek vás provede nastavení Azure AD Application Proxy řešení pro hostování webového rozhraní API služby s přístupem k nativní aplikace. 

## <a name="overview"></a>Přehled

Následující diagram znázorňuje tradiční způsob, jak publikovat místních rozhraní API. Tento přístup vyžaduje otevřít příchozí porty 80 a 443.

![Tradiční přístup přes rozhraní API](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Následující diagram ukazuje, jak lze pomocí Proxy aplikací Azure AD bezpečně publikovat rozhraní API, aniž byste museli otevírat žádné příchozí porty:

![Přístup k Azure AD Application Proxy API](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Proxy aplikací služby Azure AD pomocí formuláře páteřní řešení, funguje jako veřejný koncový bod pro přístup k rozhraní API a poskytuje ověřování a autorizace. Dostanete vaše rozhraní API z velkého množství platforem s využitím [ADAL](/azure/active-directory/develop/active-directory-authentication-libraries) knihovny. 

Od Azure AD Application Proxy ověřování a autorizace jsou postavené na Azure AD, můžete použít podmíněný přístup Azure AD k zajištění, že pouze důvěryhodné zařízení mají přístup k rozhraní API publikované prostřednictvím Proxy aplikací. Použití Azure AD Join nebo připojeno k hybridní Azure AD pro stolní počítače a pro zařízení spravovaná pomocí Intune. Můžete taky využít výhod funkce Azure Active Directory Premium, jako je Azure Multi-Factor Authentication a machine learning zajišťuje zabezpečení [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection).

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle tohoto návodu, potřebujete:

- Přístup správce s adresářem Azure, pomocí účtu, který můžete vytvářet a registrovat aplikace
- Ukázkové webové rozhraní API a nativní klientské aplikace [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Publikujte rozhraní API prostřednictvím Proxy aplikací

Chcete-li publikovat rozhraní API mimo intranet prostřednictvím Proxy aplikací, postupují stejným způsobem jako u publikování webových aplikací. Další informace najdete v tématu [kurzu: Přidat místní aplikace pro vzdálený přístup prostřednictvím Proxy aplikací v Azure Active Directory](application-proxy-add-on-premises-application.md).

Chcete-li publikovat SecretAPI webového rozhraní API prostřednictvím Proxy aplikací:

1. Vytvářejte a publikujte SecretAPI ukázkový projekt jako webová aplikace ASP.NET do místního počítače nebo intranetu. Ujistěte se, že umožňuje přístup k webové aplikaci místně. 
   
1. V [webu Azure portal](https://portal.azure.com)vyberte **Azure Active Directory** v levém navigačním panelu. Potom na **přehled** stránce **podnikové aplikace**.
   
1. V horní části **podnikové aplikace – všechny aplikace** stránce **novou aplikaci**.
   
1. Na **přidat aplikaci** stránce v části **přidat vlastní aplikaci**vyberte **On-premises application**. 
   
1. Pokud nemáte nainstalované konektoru Proxy aplikace, budete vyzváni k jeho instalaci. Vyberte **stažení konektoru Proxy aplikace** ke stažení a instalaci konektoru. 
   
1. Po instalaci konektoru Proxy aplikace na **přidat vlastní místní aplikaci** stránky:
   
   1. Zadejte *SecretAPI* vedle **název**.
      
   1. Zadejte adresu URL, které používáte pro přístup k rozhraní API z v rámci sítě intranet vedle **interní adresa Url**. 
      
   1. Ujistěte se, že **předběžné ověření** je nastavena na **Azure Active Directory**. 
      
   1. Vyberte **přidat** v horní části stránky a vyčkat, než aplikace, který se má vytvořit.
   
   ![Přidat aplikace API](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. Na **podnikové aplikace – všechny aplikace** stránky, vyberte **SecretAPI** aplikace. 
   
1. Na **SecretAPI – přehled** stránce **vlastnosti** v levém navigačním panelu.
   
1. Nechcete, aby rozhraní API bude k dispozici koncovým uživatelům v **MyApps** panelu, takže nastavte **viditelné pro uživatele** k **ne** v dolní části **vlastnosti**stránce a pak vyberte **Uložit**.
   
   ![Není viditelné pro uživatele](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Jste publikovali prostřednictvím Proxy aplikací Azure AD vašeho webového rozhraní API. Teď přidejte uživatele, kteří mohou přistupovat k aplikaci. 

1. Na **SecretAPI – přehled** stránce **uživatelů a skupin** v levém navigačním panelu.
   
1. Na **uživatelů a skupin** stránce **přidat uživatele**.  
   
1. Na **přidat přiřazení** stránce **uživatelů a skupin**. 
   
1. Na **uživatelů a skupin** stránky, vyhledejte a vyberte uživatele, kteří můžou přístup k aplikaci, včetně alespoň sami. Až vyberete všechny uživatele, vyberte **vyberte**. 
   
   ![Vybrat a přiřadit uživatele](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Zpět na **přidat přiřazení** stránce **přiřadit**. 

> [!NOTE]
> Rozhraní API, které používají integrované ověřování Windows může vyžadovat [další kroky](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registrovat nativní aplikace a udělit přístup k rozhraní API

Nativní aplikace jsou programy vytvořené pro použití na konkrétní platformu nebo zařízení. Před vaší nativní aplikace můžete připojit a přístup k rozhraní API, musíte ji zaregistrovat ve službě Azure AD. Následující kroky ukazují, jak registrovat nativní aplikace a získání přístupu k webovému rozhraní API publikované prostřednictvím Proxy aplikací.

Postup pro registraci AppProxyNativeAppSample nativní aplikace:

1. V Azure Active Directory **přehled** stránce **registrace aplikací**a v horní části **registrace aplikací** vyberte **nové registrace** .
   
1. Na **zaregistrovat aplikaci** stránky:
   
   1. V části **název**, zadejte *AppProxyNativeAppSample*. 
      
   1. V části **podporovaných typů účtu**vyberte **účty v jakékoli organizaci adresáři a osobní účty Microsoft**. 
      
   1. V části **adresy URL pro přesměrování**, rozevírací seznam a vyberte **veřejným klientem (mobilu)** a pak zadejte *https:\//appproxynativeapp*. 
      
   1. Vyberte **zaregistrovat**a počkejte, aplikace se úspěšně zaregistrovala. 
      
      ![Registrace nové aplikace](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
Jste nyní zaregistrováni AppProxyNativeAppSample aplikace v Azure Active Directory. Do vaší nativní aplikace přístup k SecretAPI webového rozhraní API:

1. V Azure Active Directory **přehled** > **registrace aplikací** stránky, vyberte **AppProxyNativeAppSample** aplikace. 
   
1. Na **AppProxyNativeAppSample** stránce **oprávnění k rozhraní API** v levém navigačním panelu. 
   
1. Na **oprávnění k rozhraní API** stránce **přidat oprávnění**.
   
1. V prvním **žádosti rozhraní API oprávnění** stránky, vyberte **naše organizace bude využívat rozhraní API** kartu a poté vyhledejte a vyberte **SecretAPI**. 
   
1. Při dalším **žádosti rozhraní API oprávnění** stránce, zaškrtněte políčko vedle položky **user_impersonation**a pak vyberte **přidat oprávnění**. 
   
    ![Výběr rozhraní API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Zpět na **oprávnění k rozhraní API** stránky, můžete vybrat **udělit souhlas správce pro doménu Contoso** k ostatním uživatelům zabránit, aby museli samostatně vyjádřit souhlas aplikace. 

## <a name="configure-the-native-app-code"></a>Konfigurovat kód nativní aplikace

Posledním krokem je konfigurace nativní aplikace. Následující fragment kódu ze *Form1.cs* soubor v ukázkové aplikaci NativeClient způsobí, že se knihovna ADAL pro získání tokenu žádosti o volání rozhraní API a připojit ho jako nosiče k hlavičce aplikace. 
   
   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
       
       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
       
       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");
   
       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```
   
Pokud chcete nakonfigurovat nativní aplikace pro připojení k Azure Active Directory a volat rozhraní API App Proxy, aktualizujte hodnoty zástupných symbolů v *App.config* souboru NativeClient ukázkovou aplikaci s hodnotami ze služby Azure AD: 

- Vložit **ID adresáře (tenant)** v `<add key="ida:Tenant" value="" />` pole. Můžete najít a zkopírujte tuto hodnotu (GUID) z **přehled** stránky buď z vašich aplikací. 
  
- Vložte AppProxyNativeAppSample **ID aplikace (klient)** v `<add key="ida:ClientId" value="" />` pole. Můžete najít a zkopírujte tuto hodnotu (GUID) z AppProxyNativeAppSample **přehled** stránky.
  
- Vložte AppProxyNativeAppSample **identifikátor URI pro přesměrování** v `<add key="ida:RedirectUri" value="" />` pole. Můžete najít a zkopírujte tuto hodnotu (URI) z AppProxyNativeAppSample **ověřování** stránky. 
  
- Vložte SecretAPI **identifikátor URI ID aplikace** v `<add key="todo:TodoListResourceId" value="" />` pole. Můžete najít a zkopírujte tuto hodnotu (URI) z SecretAPI **vystavit rozhraní API** stránky.
  
- Vložte SecretAPI **adresa URL domovské stránky** v `<add key="todo:TodoListBaseAddress" value="" />` pole. Můžete najít a zkopírujte tuto hodnotu (URL) z SecretAPI **značky** stránky.

Po dokončení konfigurace parametrů sestavíte a spustíte nativní aplikaci. Když vyberete **Sign In** tlačítko, aplikace vám umožní přihlášení, a pak zobrazí obrazovce úspěch ke potvrďte, že úspěšně připojen k SecretAPI.

![Úspěch](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Další postup

- [Kurz: Přidat místní aplikace pro vzdálený přístup prostřednictvím Proxy aplikací v Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Rychlé zprovoznění: Konfigurovat klientskou aplikaci pro přístup k webovým rozhraním API](../develop/quickstart-configure-app-access-web-apis.md)
- [Jak povolit nativní klientské aplikace pro interakci s proxy aplikací](application-proxy-configure-native-client-application.md)
