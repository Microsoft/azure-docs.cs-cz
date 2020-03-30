---
title: Přístup k místním virtuálním i virtuálním i proxy objektům služby Azure AD
description: Proxy aplikace Služby Azure Active Directory umožňuje nativním aplikacím bezpečně přistupovat k rozhraním API a obchodní logice, kterou hostujete místně nebo na cloudových virtuálních počítačích.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ecd5d8bae22d67f8d9f5b99d5c94eecf54a4a1f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77166019"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Zabezpečený přístup k místním virtuálním i virtuálním i virtuálním i proxy objektům služby Azure AD

Můžete mít obchodní logiky API spuštěna místně nebo hostované na virtuálních počítačích v cloudu. Nativní aplikace pro Android, iOS, Mac nebo Windows musí pracovat s koncovými body rozhraní API, aby mohly používat data nebo poskytovat interakci s uživatelem. Proxy aplikace Azure AD a [knihovny ověřování Azure Active Directory (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) umožňují vašim nativním aplikacím bezpečně přistupovat k místním rozhraním API. Azure Active Directory Application Proxy je rychlejší a bezpečnější řešení než otevření portů brány firewall a řízení ověřování a autorizace na vrstvě aplikace. 

Tento článek vás provede nastavením řešení Proxy aplikací Azure AD pro hostování služby webového rozhraní API, ke které mají nativní aplikace přístup. 

## <a name="overview"></a>Přehled

Následující diagram znázorňuje tradiční způsob publikování místních rozhraní API. Tento přístup vyžaduje otevření příchozích portů 80 a 443.

![Tradiční přístup k rozhraní API](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Následující diagram znázorňuje, jak můžete pomocí proxy aplikace Azure AD bezpečně publikovat api bez otevření příchozích portů:

![Přístup k rozhraní PROXY aplikace Azure AD](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Proxy aplikace Azure AD tvoří páteř řešení, pracuje jako veřejný koncový bod pro přístup k rozhraní API a poskytuje ověřování a autorizaci. K vašim apim můžete přistupovat z široké škály platforem pomocí knihoven [ADAL.](/azure/active-directory/develop/active-directory-authentication-libraries) 

Vzhledem k tomu, že ověřování a autorizace proxy aplikací Azure AD je postavená na Azure AD, můžete použít podmíněný přístup Azure AD k zajištění, že k pouze důvěryhodným zařízením mají přístup k virtuálním imalem publikovaným prostřednictvím proxy aplikace. Použijte azure ad připojit nebo Azure AD hybridní připojil pro stolní počítače a Intune spravované pro zařízení. Můžete také využít funkce Azure Active Directory Premium, jako je Azure Multi-Factor Authentication a zabezpečení [Azure Identity Protection](/azure/active-directory/active-directory-identityprotection)podporované strojovým učením .

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle tohoto návodu, potřebujete:

- Přístup správce k adresáři Azure s účtem, který umí vytvářet a registrovat aplikace
- Ukázkové webové rozhraní API a nativní klientské aplikace[https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>Publikování rozhraní API prostřednictvím proxy aplikace

Chcete-li publikovat rozhraní API mimo intranet prostřednictvím proxy aplikace, postupujte podle stejného vzoru jako při publikování webových aplikací. Další informace najdete [v tématu Kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace ve službě Azure Active Directory](application-proxy-add-on-premises-application.md).

Publikování webového rozhraní API SecretAPI prostřednictvím proxy aplikace:

1. Vytvořte a publikujte ukázkový projekt SecretAPI jako ASP.NET webové aplikace v místním počítači nebo intranetu. Ujistěte se, že máte přístup k webové aplikaci místně. 
   
1. Na [webu Azure Portal](https://portal.azure.com)vyberte **Azure Active Directory**. Pak vyberte **podnikové aplikace**.
   
1. V horní části **stránky Podnikové aplikace – všechny aplikace** vyberte Nová **aplikace**.
   
1. Na stránce **Přidat aplikaci** vyberte **Místní aplikace**. Zobrazí se stránka **Přidat vlastní místní aplikaci.**
   
1. Pokud nemáte nainstalovanou aplikaci Proxy Connector, budete vyzváni k jeho instalaci. Chcete-li stáhnout a nainstalovat konektor, vyberte **možnost Stáhnout konektor proxy aplikace.** 
   
1. Po instalaci konektoru proxy aplikace na stránce **Přidat vlastní místní aplikaci:**
   
   1. Vedle **názvu**zadejte *Rozhraní SecretAPI*.
      
   1. Vedle **možnosti Interní adresa URL**zadejte adresu URL, kterou používáte pro přístup k rozhraní API v rámci sítě intranet.
      
   1. Ujistěte se, že je **předověření** nastaveno na **Azure Active Directory**. 
      
   1. V horní části stránky vyberte **Přidat** a počkejte, až se aplikace vytvoří.
   
   ![Přidání aplikace API](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. Na stránce **Podnikové aplikace – všechny aplikace** vyberte aplikaci **SecretAPI.** 
   
1. Na stránce **SecretAPI - Přehled** vyberte **vlastnosti** v levém navigačním panelu.
   
1. Nechcete, aby rozhraní API byla dostupná koncovým uživatelům v panelu **MyApps,** proto nastavte **možnost Viditelné pro uživatele** na **Ne** v dolní části stránky **Vlastnosti** a pak vyberte **Uložit**.
   
   ![Není viditelné pro uživatele](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Vaše webové rozhraní API jste publikovali prostřednictvím proxy aplikací Azure AD. Nyní přidejte uživatele, kteří mají přístup k aplikaci. 

1. Na stránce **SecretAPI – přehled** vyberte v levém navigačním panelu **položku Uživatelé a skupiny.**
   
1. Na stránce **Uživatelé a skupiny** vyberte **Přidat uživatele**.  
   
1. Na stránce **Přidat přiřazení** vyberte **Možnost Uživatelé a skupiny**. 
   
1. Na stránce **Uživatelé a skupiny** vyhledejte a vyberte uživatele, kteří mají přístup k aplikaci, včetně alespoň vás. Po výběru všech uživatelů vyberte **vybrat**. 
   
   ![Výběr a přiřazení uživatele](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Zpět na stránce **Přidat přiřazení** vyberte **Přiřadit**. 

> [!NOTE]
> Použití api, která používají integrované ověřování systému Windows, mohou vyžadovat [další kroky](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd).

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Registrace nativní aplikace a udělení přístupu k rozhraní API

Nativní aplikace jsou programy vyvinuté pro použití na konkrétní platformě nebo zařízení. Než se vaše nativní aplikace bude moci připojit a získat přístup k rozhraní API, musíte ho zaregistrovat ve službě Azure AD. Následující kroky ukazují, jak zaregistrovat nativní aplikaci a udělit jí přístup k webovému rozhraní API, které jste publikovali prostřednictvím proxy aplikace.

Registrace nativní aplikace AppProxyNativeAppSample:

1. Na stránce **Přehled** služby Azure Active Directory vyberte **Registrace aplikací**a v horní části podokna **Registrace aplikací** vyberte Nová **registrace**.
   
1. Na stránce **Registrovat přihlášku:**
   
   1. V části **Název**zadejte *AppProxyNativeAppSample*. 
      
   1. V části **Podporované typy účtů**vyberte Účty ve všech **organizačních adresářích a osobních účtech Microsoft**. 
      
   1. V části **Adresa URL přesměrování**rozbalte a vyberte Veřejný klient **(mobilní & stolní mů e však)** a zadejte *https:\//appproxynativeapp*. 
      
   1. Vyberte **Registrovat**a počkejte, až bude aplikace úspěšně zaregistrována. 
      
      ![Registrace nové aplikace](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
Teď jste zaregistrovali aplikaci AppProxyNativeAppSample ve službě Azure Active Directory. Jak nativní aplikaci udělit přístup k webovému rozhraní API Rozhraní Pro tajné rozhraní:

1. Na stránce Registrace aplikací **Přehled** > **služby** Azure AD vyberte aplikaci **AppProxyNativeAppSample.** 
   
1. Na stránce **AppProxyNativeAppSample** vyberte oprávnění **rozhraní API** v levém navigačním panelu. 
   
1. Na stránce **oprávnění rozhraní API** vyberte Přidat **oprávnění**.
   
1. Na stránce první ho **oprávnění rozhraní API požadavku** vyberte kartu Rozhraní **API, která moje organizace používá,** a pak vyhledejte a vyberte **položku SecretAPI**. 
   
1. Na další stránce **Oprávnění rozhraní API požadavku** zaškrtněte políčko vedle **user_impersonation**a pak vyberte **Přidat oprávnění**. 
   
    ![Výběr rozhraní API](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Zpět na stránce **oprávnění rozhraní API** můžete vybrat **Udělit souhlas správce pro společnost Contoso,** abyste zabránili ostatním uživatelům v nutnosti individuálního souhlasu s aplikací. 

## <a name="configure-the-native-app-code"></a>Konfigurace kódu nativní aplikace

Posledním krokem je konfigurace nativní aplikace. Následující úryvek ze souboru *Form1.cs* v ukázkové aplikaci NativeClient způsobí, že knihovna ADAL získá token pro vyžádání volání rozhraní API a připojí ho jako nositele k záhlaví aplikace. 
   
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
   
Pokud chcete nativní aplikaci nakonfigurovat tak, aby se připojovala ke službě Azure Active Directory a volala proxy aplikace API, aktualizujte zástupné hodnoty v souboru *App.config* ukázkové aplikace NativeClient pomocí hodnot z Azure AD: 

- Do pole vložte **ID adresáře (tenanta).** `<add key="ida:Tenant" value="" />` Tuto hodnotu (identifikátor GUID) můžete najít a zkopírovat na stránce **Přehled** jedné z aplikací. 
  
- Do `<add key="ida:ClientId" value="" />` pole vložte ID aplikace AppProxyNativeAppSample **(klienta).** Tuto hodnotu (identifikátor GUID) můžete najít a zkopírovat na stránce **Přehled** appproxynativeappsample.
  
- Do `<add key="ida:RedirectUri" value="" />` pole vložte **identifikátor URI přesměrování** AppProxyNativeAppSample. Tuto hodnotu (identifikátor URI) můžete najít a zkopírovat ze stránky AppProxyNativeAppSample **Authentication.** 
  
- Do `<add key="todo:TodoListResourceId" value="" />` pole vložte **identifikátor URI ID aplikace** SecretAPI. Tuto hodnotu (identifikátor URI) můžete najít a zkopírovat ze stránky Rozhraní **API** rozhraní SecretAPI.
  
- Do `<add key="todo:TodoListBaseAddress" value="" />` pole vložte **adresu URL domovské stránky** rozhraní SecretAPI. Tuto hodnotu (adresu URL) můžete najít a zkopírovat ze stránky **Značky** SecretAPI.

Po konfiguraci parametrů vytvořte a spusťte nativní aplikaci. Když vyberete tlačítko **Přihlásit** se, aplikace vám umožní přihlásit se a pak se zobrazí obrazovka úspěchu, která potvrzuje, že se úspěšně připojila k rozhraní SecretAPI.

![Úspěch](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Další kroky

- [Kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace ve službě Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Rychlý start: Konfigurace klientské aplikace pro přístup k webovým rozhraním API](../develop/quickstart-configure-app-access-web-apis.md)
- [Jak povolit nativní klientské aplikace pro interakci s proxy aplikacemi](application-proxy-configure-native-client-application.md)
