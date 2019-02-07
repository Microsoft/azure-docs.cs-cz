---
title: Integrace aplikací se službou Azure Active Directory
description: Zjistěte, jak v Azure Active Directory (Azure AD) aktualizovat aplikaci.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 34d0c4054273babcc56516d290857c4ddb554bf7
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819089"
---
# <a name="quickstart-update-an-application-in-azure-active-directory"></a>Rychlý start: Aktualizace aplikace v Azure Active Directory

Podnikoví vývojáři a poskytovatelé softwaru jako služby (SaaS), kteří mají v Azure Active Directory (Azure AD) zaregistrované aplikace, mohou potřebovat své aplikace nakonfigurovat tak, aby měly přístup k ostatním prostředkům, jako jsou webová rozhraní API, nebo byly k dispozici v jiných organizacích a tak dále.

V tomto rychlém startu se dozvíte různé způsoby, jak můžete aplikaci nakonfigurovat nebo aktualizovat, aby splňovala nejen vaše požadavky nebo potřeby, ale i požadavky nebo potřeby jiných organizací.

## <a name="prerequisites"></a>Požadavky

Pokud chcete začít, ujistěte se, že jste dokončili následující kroky:

* Přečetli jste si přehled o [architektuře Azure AD pro udělování souhlasu](consent-framework.md), kterému je důležité rozumět při vytváření aplikací, které budou používat jiní uživatelé či jiné aplikace.
* Máte tenanta služby Azure AD, ke kterému jsou zaregistrované aplikace.
  * Pokud ještě tenanta nemáte, [zjistěte, jak ho získat](quickstart-create-new-tenant.md).
  * Pokud nemáte v tenantovi zaregistrované aplikace, [zjistěte, jak aplikaci do Azure AD přidat](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="configure-a-client-application-to-access-web-apis"></a>Konfigurace klientské aplikace pro přístup k webovým rozhraním API

Aby se webová nebo důvěrná klientská aplikace mohla účastnit toku udělení autorizace, který vyžaduje ověření (a získání přístupového tokenu), musí mít zabezpečené přihlašovací údaje. Výchozí metodou ověřování podporovanou webem Azure Portal je ID klienta a tajný klíč. Tato část popisuje kroky konfigurace požadované k poskytnutí tajného klíče pomocí přihlašovacích údajů klienta.

Než klient získá přístup k webovému rozhraní API zveřejněnému aplikací prostředků (jako je rozhraní API pro Microsoft Graph), rozhraní pro udělování souhlasu zajišťuje, že klient musí nejprve na základě vyžádání oprávnění získat udělení oprávnění. Ve výchozím nastavení si mohou všechny aplikace zvolit oprávnění z **Azure Active Directory** (rozhraní Graph API) a modelu nasazení Azure Classic. Ve výchozím nastavení je také vybrané [oprávnění rozhraní Graph API pro „přihlášení a čtení uživatelského profilu“](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails). Pokud je váš klient zaregistrovaný v tenantovi, který má účty s předplatným Office 365, budou k dispozici k výběru webová rozhraní API a oprávnění pro SharePoint a Exchange Online. U každého požadovaného webového rozhraní API si můžete vybrat ze dvou typů oprávnění:

- Oprávnění aplikace: Klientská aplikace potřebuje přístup k webovému rozhraní API jako samotný (žádný kontext uživatele). Tento typ oprávnění vyžaduje souhlas správce a navíc není k dispozici pro nativní klientské aplikace.
- Delegovaná oprávnění: Klientská aplikace potřebuje přístup k webovému rozhraní API jako přihlášený uživatel, ale přístup omezen vybrané oprávnění. Pokud tento typ oprávnění nevyžaduje souhlas správce, může ho udělit uživatel.

  > [!NOTE]
  > Přidání delegovaného oprávnění do aplikace neuděluje uživatelům v tenantovi souhlas automaticky. Pokud správce neudělí souhlas jménem všech uživatelů, musí uživatelé stále souhlas pro delegovaná oprávnění přidaná za běhu udělit ručně.

### <a name="add-application-credentials-or-permissions-to-access-web-apis"></a>Přidání přihlašovacích údajů aplikace nebo oprávnění pro přístup k webovému rozhraní API

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet umožňuje přístup k více účtům, vyberte požadovaný účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
3. V levém navigačním podokně vyberte službu **Azure Active Directory**, vyberte **Registrace aplikací** a potom najděte a vyberte aplikaci, kterou chcete nakonfigurovat.

   ![Aktualizace registrace aplikace](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Budete přesměrováni na hlavní stránku registrace aplikace, která obsahuje stránku aplikace **Nastavení**. Postup přidání přihlašovacích údajů u webové aplikace:
  1. Na stránce **Nastavení** vyberte část **Klíče**.
  2. Přidání certifikátu:
    - Vyberte **Odeslat veřejný klíč**.
    - Vyberte soubor, který chcete nahrát. Musí jít o jeden z následujících typů souboru: .cer, .pem nebo .crt.
  - Přidání hesla:
    - Přidejte svému klíči popis.
    - Vyberte dobu trvání.
    - Vyberte **Uložit**. Po uložení změn konfigurace bude krajní pravý sloupec obsahovat hodnotu klíče. **Nezapomeňte si klíč zkopírovat**, abyste ho mohli použít v kódu klientské aplikace, protože jinak se k němu už nedostanete, když tuto stránku opustíte.

5. Přidání oprávnění pro přístup k rozhraní API prostředků z klienta
  1. Na stránce **Nastavení** vyberte část **Požadovaná oprávnění** a potom vyberte **Přidat**.
  1. Zvolte **Vyberte rozhraní API**, abyste zvolili typ prostředků, ze kterých chcete vybírat.
  1. Projděte si seznam dostupných rozhraní API nebo použijte vyhledávací pole a vyberte si v adresáři z dostupných aplikací prostředků, které zveřejňují webové rozhraní API. Vyberte prostředek, o který máte zájem, a potom klikněte na **Vybrat**.
  1. Na stránce **Povolit přístup** vyberte oprávnění aplikace a/nebo delegovaná oprávnění, které aplikace potřebuje při přístupu k rozhraní API.
   
  ![Aktualizace registrace aplikace – rozhraní API pro oprávnění](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-api.png)

  ![Aktualizace registrace aplikace – povolení oprávnění](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms.png)

6. Až budete hotoví, vyberte na stránce **Povolit přístup** tlačítko **Vybrat** a pak na stránce **Přidat přístup přes rozhraní API** vyberte tlačítko **Hotovo**. Budete přesměrováni na stránku **Požadovaná oprávnění**, kde na seznamu rozhraní API uvidíte nový prostředek.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurace aplikace prostředků k zveřejnění webových rozhraní API

Můžete vyvinou webové rozhraní API a zpřístupnit ho klientským aplikacím zveřejněním [oborů](developer-glossary.md#scopes) a [rolí](developer-glossary.md#roles) přístupu. Správně nakonfigurované webové rozhraní API bude k dispozici stejně jako ostatní webová rozhraní API Microsoftu, včetně rozhraní Graph API a rozhraní API pro Office 365. Obory a role přístupu se zveřejňují prostřednictvím [manifestu aplikace](developer-glossary.md#application-manifest), což je soubor JSON, který představuje konfiguraci identity vaší aplikace.

V následující části se dozvíte, jak zveřejnit obory přístupu úpravou manifestu aplikace prostředků.

### <a name="add-access-scopes-to-your-resource-application"></a>Přidání oborů přístupu do aplikace prostředků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet umožňuje přístup k více účtům, vyberte požadovaný účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
3. V levém navigačním podokně vyberte **Azure Active Directory > Registrace aplikací** a potom najděte a vyberte aplikaci, kterou chcete nakonfigurovat.

   ![Aktualizace registrace aplikace](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Budete přesměrováni na hlavní stránku registrace aplikace, která otevře stránku **Nastavení**. Kliknutím na **Manifest** se ze stránky registrace aplikace přepněte na stránku **Upravit manifest**. Otevře se editor manifestu na webovém základu, který vám umožní na webu Azure Portal manifest **Upravit**. Volitelně můžete kliknout na **Stáhnout**, upravit si ho místně a potom ho **Nahrát** zpět do aplikace.
5. V tomto příkladu zveřejníme v našem prostředku či rozhraní API nový obor s názvem `Employees.Read.All` tak, že přidáme do kolekce `oauth2Permissions` následující prvek JSON. Existující obor `user_impersonation` získáte ve výchozím nastavení během registrace. Obor `user_impersonation` umožňuje klientské aplikaci pod identitou přihlášeného uživatele požádat o oprávnění pro přístup k prostředku. Nezapomeňte za existující obor `user_impersonation` přidat čárku a změnit hodnoty vlastností tak, aby vyhovovaly potřebám prostředku. 

  ```json
  {
    "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
    "adminConsentDisplayName": "Read-only access to Employee records",
    "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
    "isEnabled": true,
    "type": "User",
    "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
    "userConsentDisplayName": "Read-only access to your Employee records",
    "value": "Employees.Read.All"
  }
  ```

  > [!NOTE]
  > `id` Hodnota musí být generována prostřednictvím kódu programu nebo pomocí identifikátoru GUID generování nástroje, jako [Guidgen –](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). Hodnota `id` představuje jedinečný identifikátor pro obor zveřejněný webovým rozhraním API. Jakmile se klient správně nakonfiguruje pomocí oprávnění pro přístup k webovému rozhraní API, Azure AD vydá přístupový token OAuth 2.0. Když klient volá webové rozhraní API, představí přístupový token, jehož deklarace identity oboru je nastavená na oprávnění vyžádaná v registraci aplikace.
  >
  > Další obory můžete podle potřeby zveřejnit později. Vezměte v úvahu, že webové rozhraní API může zveřejnit více oborů přidružených k celé řadě různých funkcí. Váš prostředek může za běhu řídit přístup k webovému rozhraní API tak, že bude vyhodnocovat deklaraci/deklarace identity oboru (`scp`) v přijatém přístupovém tokenu OAuth 2.0.

6. Jakmile budete hotoví, klikněte na **Uložit**. Webové rozhraní API je teď nakonfigurované k použití jinými aplikacemi v adresáři.

  ![Aktualizace registrace aplikace](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-manifest.png)

### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Ověření, že je webové rozhraní API zveřejněné ostatním aplikacím v tenantovi

1. Vraťte se do tenanta Azure AD, vyberte znovu **Registrace aplikací** a pak najděte a vyberte klientskou aplikaci, kterou chcete nakonfigurovat.

   ![Aktualizace registrace aplikace](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

2. Zopakujte pátý krok jako v části [Konfigurace klientské aplikace pro přístup k webovým rozhraním API](#configure-a-client-application-to-access-web-apis). Když se dostanete ke kroku **Vyberte rozhraní API**, zadejte název aplikace do vyhledávacího pole, vyhledejte svůj prostředek a potom klikněte na **Vybrat**.

3. Na stránce **Povolit přístup** byste měli vidět nový obor, který je k dispozici pro žádosti o oprávnění klientů.

  ![Zobrazená nová oprávnění](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms-newscopes.png)

### <a name="more-on-the-application-manifest"></a>Další informace o manifestu aplikace

Manifest aplikace slouží jako mechanismus pro aktualizaci aplikační entity, která definuje všechny atributy konfigurace identity aplikace Azure AD, včetně probíraných oborů přístupu rozhraní API. Další informace o aplikační entitě a jejím schéma najdete v [dokumentaci aplikační entity rozhraní Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Tento článek obsahuje úplné referenční informace o členech aplikační entity používaných k určení oprávnění u rozhraní API, včetně:  

- Člena appRoles, který je kolekcí entit [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) používaných k definování [oprávnění aplikace](developer-glossary.md#permissions) u webového rozhraní API. 
- Člena oauth2Permissions, který je kolekcí entit [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) používaných k definování [delegovaných oprávnění](developer-glossary.md#permissions) u webového rozhraní API.

Další informace o obecných konceptech manifestu aplikace získáte v [manifestu aplikace Azure AD](reference-app-manifest.md).

## <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Přístup k Azure AD Graph a Office 365 prostřednictvím rozhraní API pro Microsoft Graph  

Jak už jsme zmiňovali dříve, kromě zveřejnění a zpřístupnění rozhraní API pro vlastní aplikace můžete klientské aplikace zaregistrovat i pro přístup k rozhraním API zveřejněným prostředky Microsoftu. Rozhraní API pro Microsoft Graph, které se v seznamu prostředků či rozhraní API na webu Azure Portal označuje jako „Microsoft Graph“, je k dispozici všem aplikacím, které jsou zaregistrované pomocí Azure AD. Pokud registrujete klientskou aplikaci v tenantovi, který obsahuje účty s předplatným Office 365, můžete získat přístup i k oborům zveřejněným různými prostředky Office 365.

Úplné informace o oborech zveřejněných rozhraním API pro Microsoft Graph najdete v článku s [referenčními informacemi k oprávněním Microsoft Graphu](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference).

> [!NOTE]
> Pokud nativní klientské aplikace používají oprávnění pro „přístup k adresáři organizace“, mohou vzhledem k aktuálnímu omezení volat pouze rozhraní Azure AD Graph API. Toto omezení neplatí pro webové aplikace.

## <a name="configuring-multi-tenant-applications"></a>Konfigurace aplikací s více tenanty

Při registraci aplikace v Azure AD možná budete chtít svou aplikaci zpřístupnit pouze uživatelům v organizaci. Možná taky budete chtít aplikaci zpřístupnit uživatelům v externích organizacích. Těmto dvou typům aplikací se říká aplikace s jedním nebo více tenanty. Tato část popisuje, jak změnit konfiguraci aplikace s jedním tenantem, aby se z ní stala aplikace s více tenanty.

Je důležité uvědomit si rozdíly mezi aplikací s jedním a více tenanty:  

- Aplikace s jedním tenantem je určená k použití v jedné organizaci. Obvykle se jedná o podnikovou aplikaci napsanou podnikovým vývojářem. K aplikaci s jedním tenantem mají přístup pouze uživatelé s účty ve stejném tenantovi, ve kterém je i registrace aplikace. Proto je potřeba ji zřídit pouze v jednom adresáři.
- Aplikace s více tenanty je určená k použití ve více organizacích. Obvykle jde o aplikaci napsanou nezávislým výrobcem softwaru (ISV) a označuje se jako webová aplikace softwaru jako služby (SaaS). Aplikace s více klienty se musí zřizovat v každém tenantovi, do kterého potřebují uživatelé získat přístup. U všech tenantů (kromě tenanta, ve kterém je aplikace zaregistrovaná) je potřeba souhlas uživatele nebo správce, aby se aplikace mohla zaregistrovat. Nativní klientské aplikace mají ve výchozím nastavení více tenantů, protože jsou nainstalované na zařízení vlastníka prostředku. Zobrazit předchozí přehled oddíl framework souhlasu podrobnosti na rozhraní pro udělování souhlasu.

Převedení aplikace na aplikaci s více tenanty vyžaduje jak změny v registraci aplikace, tak i změny v samotné webové aplikaci. Následující část se věnuje oběma změnám.

### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Změna registrace aplikace kvůli podpoře více tenantů

Pokud píšete aplikaci, kterou chcete zpřístupnit zákazníkům nebo partnerům mimo organizaci, musíte na webu Azure Portal aktualizovat definici aplikace.

> [!IMPORTANT]
> Azure AD vyžaduje, aby identifikátor URI ID aplikací s více tenanty byl globálně jedinečný. Identifikátor URI ID aplikace je jedním ze způsobů, kterými se může aplikace ve zprávách protokolu identifikovat. U aplikace s jedním tenantem stačí, když bude identifikátor URI ID aplikace jedinečný v rámci daného tenanta. U aplikace s více tenanty musí být globálně jedinečný, aby služba Azure AD aplikaci našla mezi všemi tenanty.
> Globální jedinečnost se vynucuje požadavkem, aby Identifikátor URI ID aplikace obsahoval název hostitele, který odpovídá ověřené doméně tenanta Azure AD. Když je například název tenanta contoso.onmicrosoft.com, pak by platným identifikátorem URI ID aplikace byl https://contoso.onmicrosoft.com/myapp. Pokud má tenant ověřenou doménu contoso.com, pak by platným identifikátorem URI ID aplikace byl i https://contoso.com/myapp. Pokud identifikátor URI ID aplikace nepoužívá tento vzor, nastavení aplikace jako aplikace s více tenanty se nezdaří.

Pokud chcete externím uživatelům umožnit přístup k aplikaci, postupujte následovně:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet umožňuje přístup k více účtům, klikněte na požadovaný účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
3. V levém navigačním podokně klikněte na službu **Azure Active Directory**, klikněte na **Registrace aplikací** a potom najděte a klikněte na aplikaci, kterou chcete nakonfigurovat. Budete přesměrováni na hlavní stránku registrace aplikace, která otevře stránku **Nastavení**.
4. Na stránce **Nastavení** klikněte na **Vlastnosti** a posuňte přepínač **Více tenantů** na **Ano**.

Po provedení změn budou moct uživatelé a správci v jiných organizacích udělit svým uživatelům možnost přihlásit se k vaší aplikaci a tato aplikace bude mít přístup k prostředkům zabezpečeným jejich tenantem.

### <a name="changing-the-application-to-support-multi-tenant"></a>Změna aplikace kvůli podpoře více tenantů

Podpora aplikací s více tenanty do značné míry závisí na rozhraní pro udělování souhlasu Azure AD. Vyjádření souhlasu je mechanismus, který umožňuje uživateli z jiného tenanta udělit aplikaci přístup k prostředkům zabezpečeným tenantem tohoto uživatele. Tomuto se říká „souhlas uživatele“.

Webová aplikace může také nabízet:

- Možnost pro správce „přihlásit svou společnost“. Tomu se zase říká „souhlas správce“, který dává správci možnost udělit souhlas jménem *všech uživatelů* v organizaci. Souhlas správce může poskytnout pouze uživatel, který provede ověření pomocí účtu patřícímu roli globálního správce. Ostatním se zobrazí chyba.
- Registrační prostředí pro uživatele. Očekává se, že se uživatelům zobrazí tlačítko k zaregistrování, které je přesměruje na koncový bod OAuth 2.0 `/authorize` nebo OpenID Connect `/userinfo`. Tyto koncové body umožňují aplikaci získat informace o novém uživateli zkontrolováním tokenu id_token. Následující fáze registrace uživateli zobrazí s výzvou souhlasu, podobný tomu vidíte v přehledu vyjádření souhlasu oddíl framework.

Pro další informace o změnách aplikace, které se vyžadují pro podporu přístupu s více tenanty a prostředí pro přihlášení a registraci, si pročtěte:

- [Postup přihlášení libovolného uživatele služby Azure Active Directory (AD) pomocí vzoru aplikace s více tenanty](howto-convert-app-to-be-multi-tenant.md)
- Seznam [ukázek kódu pro více tenantů](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant)
- [Rychlé zprovoznění: Přidání firemního brandingu na přihlašovací stránku ve službě Azure AD](../fundamentals/customize-branding.md)

## <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Povolení implicitního udělení OAuth 2.0 u jednostránkových aplikací

Jednostránkové aplikace (SPA) mají obvykle strukturu front-endu se spoustou JavaScriptu běžícího v prohlížeči, který volá back-end aplikace webového rozhraní API, aby provedl obchodní logiku. U jednostránkových aplikací hostovaných v Azure AD se používá implicitní udělení OAuth 2.0 k ověření uživatele pomocí Azure AD a získání tokenu, který můžete použít k zabezpečení volání z javascriptového klienta aplikace do back-endu webového rozhraní API.

Jakmile uživatel udělí souhlas, může se stejný protokol ověřování použít k získání tokenů, aby se zabezpečila volání mezi klientem a jinými prostředky webového rozhraní API nakonfigurovanými pro danou aplikaci. Další informace o implicitním udělení autorizace a pomoc s rozhodnutím, zda je implicitní udělení autorizace pro váš scénář vhodné, najdete v článku o [principech toku implicitního udělení OAuth2 v Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

Ve výchozím nastavení je implicitní udělení OAuth 2.0 u aplikací zakázané. Implicitní udělení OAuth 2.0 můžete u své aplikace povolit nastavením hodnoty `oauth2AllowImplicitFlow` v [manifestu dané aplikace](reference-app-manifest.md).

### <a name="to-enable-oauth-20-implicit-grant"></a>Povolení implicitního udělení OAuth 2.0

> [!NOTE]
> Pro získání podrobností o tom, jak upravit manifest aplikace, si nejprve projděte předchozí část [Konfigurace aplikace prostředků k zveřejnění webových rozhraní API](#configuring-a-resource-application-to-expose-web-apis).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet umožňuje přístup k více účtům, klikněte na požadovaný účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
3. V levém navigačním podokně klikněte na službu **Azure Active Directory**, klikněte na **Registrace aplikací** a potom najděte a klikněte na aplikaci, kterou chcete nakonfigurovat. Budete přesměrováni na hlavní stránku registrace aplikace, která otevře stránku **Nastavení**.
4. Kliknutím na **Manifest** se ze stránky registrace aplikace přepněte na stránku **Upravit manifest**. Otevře se editor manifestu na webovém základu, který vám umožní na webu Azure Portal manifest **Upravit**. Najděte hodnotu „oauth2AllowImplicitFlow“ a nastavte ji na „true“. Ve výchozím nastavení je nastavená na „false“.
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. Aktualizovaný manifest uložte. Po uložení je webové rozhraní API nakonfigurované k používání implicitního udělení OAuth 2.0 pro ověřování uživatelů.

## <a name="next-steps"></a>Další postup

Získejte další informace o těchto rychlých startech souvisejících se správou aplikací používajících koncový bod Azure AD v1.0:
- [Přidání aplikace do Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Odebrání aplikace z Azure AD](quickstart-v1-remove-azure-ad-app.md)

Další informace o dvou objektech Azure AD, které představují zaregistrovanou aplikaci, a vztahu mezi nimi, najdete v článku o [objektech aplikací a instančních objektech](app-objects-and-service-principals.md).

Další informace o pokynech pro branding, kterými byste se měli řídit při vývoji aplikací s využitím Azure Active Directory, najdete v článku [Pokyny pro branding aplikací](howto-add-branding-in-azure-ad-apps.md).
