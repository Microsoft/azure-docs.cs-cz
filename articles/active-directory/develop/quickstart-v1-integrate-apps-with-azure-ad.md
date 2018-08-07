---
title: Integrace aplikací s Azure Active Directory
description: Postup přidání, aktualizace nebo odebrání aplikace ve službě Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: luleon
ms.openlocfilehash: bb1f53b2ea014bfc8e658cf840e0a22368ba9f7c
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579659"
---
# <a name="integrating-applications-with-azure-active-directory"></a>Integrace aplikací s Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Podnikoví vývojáři a poskytovatelé software-as-a-service (SaaS) můžou vyvíjet komerční cloudové služby nebo -obchodní aplikace, které je možné integrovat se službou Azure Active Directory (Azure AD) pro zabezpečené přihlašování a autorizaci pro jejich služby. K integraci aplikace nebo služby s využitím Azure AD, musí vývojář nejprve zaregistroval aplikaci s využitím Azure AD.

V tomto článku se dozvíte, jak přidat, aktualizovat nebo odebrat registraci aplikace ve službě Azure AD. Další informace o různých typech aplikací, které je možné integrovat se službou Azure AD, jak nakonfigurovat aplikace přístup k jiným prostředkům, jako jsou webové rozhraní API a další.

Další informace o dva objekty Azure AD, které představují zaregistrovanou aplikaci a vztahů mezi nimi, naleznete v tématu [aplikace a instanční objekty](app-objects-and-service-principals.md); Další informace o značce, měli byste Při vývoji aplikací s Azure Active Directory, najdete v tématu [Branding pokyny pro integrované aplikace](howto-add-branding-in-azure-ad-apps.md).

## <a name="adding-an-application"></a>Přidání aplikace
Všechny aplikace, které chce využívat možnosti služby Azure AD musí být nejprve registrována v tenantovi Azure AD. Tento proces registrace zahrnuje poskytuje Azure AD podrobnosti o vaší aplikaci, jako je například adresa URL, kde je umístěn, adresa URL pro odeslání odpovědi po ověření uživatele, identifikátor URI, který identifikuje aplikaci a tak dále.

### <a name="to-register-a-new-application-using-the-azure-portal"></a>Registrace nové aplikace pomocí webu Azure portal
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet poskytuje přístup k více než jeden, klikněte na svůj účet v pravém horním rohu a nastavte portálu relace na požadované služby Azure AD tenanta.
3. V levém navigačním podokně klikněte **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikací**a klikněte na tlačítko **registrace nové aplikace**.

   ![Registrace nové aplikace](./media/quickstart-v1-integrate-apps-with-azure-ad/add-app-registration.png)

4. Když **vytvořit** se zobrazí stránka, zadejte informace o registraci vaší aplikace: 

  - **Název:** zadejte název smysluplné aplikace
  - **Typ aplikace:** 
    - Vyberte "Nativní" pro [klientské aplikace](active-directory-dev-glossary.md#client-application) , které jsou nainstalovány místně na zařízení. Toto nastavení se používá pro veřejnost OAuth [nativní klienty](active-directory-dev-glossary.md#native-client).
    - Vyberte "webovou aplikaci / rozhraní API" pro [klientské aplikace](active-directory-dev-glossary.md#client-application) a [prostředků nebo rozhraní API aplikace](active-directory-dev-glossary.md#resource-server) , které jsou nainstalovány na zabezpečení serveru. Toto nastavení se používá pro OAuth důvěrné [webových klientů](active-directory-dev-glossary.md#web-client) a veřejné [uživatelského agenta – klienti se systémem](active-directory-dev-glossary.md#user-agent-based-client). Stejné aplikace může také zpřístupnit klienta a prostředků nebo rozhraní API.
  - **Přihlašovací adresa URL:** pro "webové aplikace a rozhraní API" aplikace, zadejte základní adresu URL vaší aplikace. Například `http://localhost:31544` může být adresa URL pro webovou aplikaci spuštěnou na místním počítači. Uživatelé by pomocí této adresy URL pro přihlášení k webové klientské aplikace. 
  - **Identifikátor URI pro přesměrování:** u aplikací "Nativní" Zadejte identifikátor URI, které používají Azure AD k vracení odpovědí na tokeny. Zadejte hodnotu specifickou pro vaši aplikaci, například `http://MyFirstAADApp`

   ![Registrace nové aplikace – vytvoření](./media/quickstart-v1-integrate-apps-with-azure-ad/add-app-registration-create.png)

   Pokud chcete konkrétní příklady webových nebo nativních aplikací, přečtěte si naše [rychlých startů](azure-ad-developers-guide.md#get-started).

5. Jakmile budete hotovi, klikněte na **Vytvořit**. Azure AD přiřadí jedinečné ID aplikace pro vaši aplikaci a budete přesměrováni na stránku hlavního registrace vaší aplikace. V závislosti na tom, zda je vaše aplikace webovou nebo nativní aplikaci jsou k dispozici různé možnosti pro přidání dalších možností do vaší aplikace. Najdete v další části přehledu vyjádření souhlasu a podrobnosti o povolení další konfigurace je součástí vaší registrace aplikace (přihlašovací údaje, oprávnění, povolit přihlášení pro uživatele z jiných tenantů.)

  > [!NOTE]
  > Ve výchozím nastavení je nově zaregistrovaný webovou aplikaci nakonfigurovaná umožňující **pouze** uživatelů ze stejného tenanta k přihlášení do vaší aplikace.
  > 
  > 

## <a name="updating-an-application"></a>Aktualizace aplikace
Jakmile vaše aplikace byl zaregistrován u služby Azure AD, bude pravděpodobně nutné aktualizovat tak, aby poskytují přístup k webovým rozhraním API, budou dostupné v jiných organizacích a další. Tato část popisuje různé způsoby, ve kterých můžete nakonfigurovat další aplikace. Nejdříve začneme s přehledem rozhraní pro udělování souhlasu, což je důležité pochopit, při vytváření aplikací, které je potřeba použít jiné uživatele nebo aplikace.

### <a name="overview-of-the-consent-framework"></a>Přehled rozhraní pro udělování souhlasu

Rozhraní pro udělování souhlasu Azure AD usnadňuje vývoj víceklientské webových a nativních klientských aplikací. Tyto aplikace povolit přihlášení podle uživatelské účty z tenanta služby Azure AD, jiné než ten, ve kterém aplikace bude zaregistrovaná. Může také potřebují přístup k webové rozhraní API jiných služeb společnosti Microsoft, kromě vlastní webová rozhraní API a rozhraní API, jako je například rozhraní Microsoft Graph API (pro přístup ke službám Office 365, Azure Active Directory a Intune). Rozhraní je založené na uživatele nebo správce udělení souhlasu pro aplikaci, která požádá o registrovaný v jejich adresáře, který může zahrnovat přístup k datům adresáře.

Například pokud webové klientská aplikace potřebuje číst kalendář informace o uživateli z Office 365, tento uživatel je muset vyjádřit souhlas s klientskou aplikaci nejdřív. Po souhlas, klientská aplikace bude moct volat rozhraní Microsoft Graph API jménem uživatele a podle potřeby použijte informace o kalendáři. [Microsoft Graph API](https://graph.microsoft.io) poskytuje přístup k datům v Office 365 (např. zprávy ze systému Exchange, webů a seznamech Sharepointu, dokumenty z Onedrivu, poznámkové bloky z Onenotu, úkoly Planneru, pracovní sešity v Excelu a kalendáře atd.), a také uživatelů a skupin ze služby Azure AD a dalších datových objektů z více cloudovým službám Microsoftu. 

Rozhraní pro udělování souhlasu je postavená na OAuth 2.0 a jeho různé toky, jako je autorizační kód udělení a klientské přihlašovací údaje, pomocí veřejné nebo důvěrných klientů. S použitím OAuth 2.0, Azure AD umožňuje vytvořit mnoho různých typů klientských aplikací, jako na telefonu, tabletu, server nebo webovou aplikaci a získat přístup k potřebným prostředkům.

Další informace o použití rozhraní pro udělování souhlasu s udělení autorizace OAuth 2.0 najdete v tématu [autorizaci přístupu k webovým aplikacím pomocí OAuth 2.0 a Azure AD](v1-protocols-oauth-code.md) a [scénáře ověřování pro službu Azure AD](authentication-scenarios.md). Informace o tom, jak autorizovaný přístup k Office 365 prostřednictvím Microsoft Graphu najdete v tématu [ověřování aplikací s Microsoft Graphem](https://graph.microsoft.io/docs/authorization/auth_overview).

#### <a name="example-of-the-consent-experience"></a>Příklad prostředí pro vyjádření souhlasu

Následující kroky ukazují, jak souhlasu prostředí funguje pro aplikace pro vývojáře a uživatele.

1. Předpokládejme, že máte webovou aplikaci klienta, která potřebuje požádat o určitá oprávnění pro přístup k prostředku nebo rozhraní API. Dozvíte jak provést tuto konfiguraci v další části, ale v podstatě na webu Azure portal slouží k deklaraci žádosti oprávnění v době konfigurace. Jako další nastavení konfigurace se stanou součástí registrace služby Azure AD aplikace:
   
  ![Oprávnění k ostatním aplikacím](./media/quickstart-v1-integrate-apps-with-azure-ad/requiredpermissions.png)
    
2. Vezměte v úvahu, že oprávnění aplikace se aktualizovaly, je aplikace spuštěna a uživatele je použití poprvé. Nejprve je potřeba získat autorizační kód z Azure AD aplikace `/authorize` koncového bodu. Autorizační kód lze potom získat nový přístupový a obnovovací token.

3. Pokud uživatel ještě není ověřený, Azure AD `/authorize` koncového bodu zobrazí výzvu k přihlášení.
   
  ![Uživatel nebo správce přihlášení ke službě Azure AD](./media/quickstart-v1-integrate-apps-with-azure-ad/usersignin.png)

4. Až se uživatel přihlásil, určí Azure AD, pokud uživatel musí zobrazit stránka pro odsouhlasení podmínek. Toto rozhodnutí je založená na, jestli uživatel (nebo správce ve vaší organizaci) už udělené souhlasu s aplikací. Pokud ještě nebyl udělený souhlas, Azure AD zobrazí výzvu k souhlasu a zobrazí požadovaná oprávnění, je potřeba pracovat. Sadu oprávnění, která se zobrazí v dialogovém okně souhlasu odpovídat vybraných v delegovaná oprávnění na webu Azure Portal.
   
  ![Prostředí pro vyjádření souhlasu uživatele](./media/quickstart-v1-integrate-apps-with-azure-ad/consent.png)

5. Jakmile uživatel udělí svůj souhlas, se vrátí autorizační kód do vaší aplikace, který je k získání přístupového tokenu a obnovovací token uplatnit. Další informace o tomto toku, najdete v článku [webové aplikace přes webové rozhraní API oddílu ve scénářích ověřování pro službu Azure AD](authentication-scenarios.md#web-application-to-web-api).

6. Jako správce může také souhlas delegovaná oprávnění aplikací schválit za všechny uživatele ve vašem tenantovi. Souhlas správce zabraňuje dialogové okno souhlasu zobrazilo pro každého uživatele v tenantovi a to provést [webu Azure portal](https://portal.azure.com) uživatelé s rolí správce. Z **nastavení** stránce pro vaši aplikaci, klikněte na tlačítko **požadovaná oprávnění** a klikněte na **udělit oprávnění** tlačítko. 

  ![Udělení oprávnění pro vyjádření souhlasu explicitní správce](./media/quickstart-v1-integrate-apps-with-azure-ad/grantpermissions.png)
    
  > [!NOTE]
  > Explicitní udělení souhlasu pomocí **udělit oprávnění** je aktuálně vyžaduje pro jednostránkové aplikace (SPA), které používají ADAL.js tlačítko. V opačném případě aplikace selže při vyžádání tokenu přístupu. 

### <a name="configure-a-client-application-to-access-web-apis"></a>Konfigurovat klientskou aplikaci pro přístup k webovým rozhraním API
Aby web/důvěrné klientská aplikace bude moct zúčastnit toku udělení autorizace, který vyžaduje ověření (a získat přístupový token) je potřeba vytvořit zabezpečené přihlašovací údaje. Na webu Azure portal nepodporuje výchozí metodu ověřování je ID klienta a tajný klíč. Tato část popisuje kroky konfigurace vyžaduje zadání tajného klíče s přihlašovacími údaji vašeho klienta.

Kromě toho předtím, než klient může získat přístup k webové rozhraní API, které jsou vystavené aplikace prostředků (například rozhraní Microsoft Graph API), rozhraní pro udělování souhlasu zajišťuje klient obdrží udělit oprávnění, požadovaná, na základě požadovaná oprávnění. Ve výchozím nastavení všechny aplikace můžou vybírat oprávnění "Windows Azure Active Directory" (rozhraní Graph API) a "Windows Azure Service Management API." ["Přihlášení a čtení uživatelského profilu" oprávnění rozhraní Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) vybrána ve výchozím nastavení. Pokud se váš klient je registrována v tenantovi, který má předplatné služby Office 365 účty, jsou k dispozici pro výběr webového rozhraní API a oprávnění pro SharePoint a Exchange Online. Můžete vybrat z [dva typy oprávnění](active-directory-dev-glossary.md#permissions) pro každé požadované rozhraní web API:

- Oprávnění aplikací: Klientská aplikace potřebuje přístup k webovému rozhraní API jako samotný (žádný kontext uživatele). Tento typ oprávnění vyžaduje souhlas správce a není k dispozici pro nativní klientské aplikace.

- Delegovaná oprávnění: Klientská aplikace potřebuje přístup k webovému rozhraní API jako přihlášený uživatel, ale přístup omezen vybrané oprávnění. Tento typ oprávnění lze udělit uživatelem, pokud oprávnění vyžaduje souhlas správce. 

  > [!NOTE]
  > Přidání delegovaná oprávnění k aplikaci neuděluje automaticky souhlas pro uživatele v rámci tenanta. Uživatelé musí ručně udělit souhlas pro přidání delegovaná oprávnění za běhu, pokud správce klikne **udělit oprávnění** tlačítko **požadovaná oprávnění** část stránka aplikace na webu Azure Portal. 

#### <a name="to-add-application-credentials-or-permissions-to-access-web-apis"></a>Chcete-li přidat přihlašovací údaje aplikací nebo oprávnění pro přístup k webovým rozhraním API
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet poskytuje přístup k více než jeden, klikněte na svůj účet v pravém horním rohu a nastavte portálu relace na požadované služby Azure AD tenanta.
3. V levém navigačním podokně klikněte **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikací**, potom hledání nebo klikněte na aplikaci, kterou chcete konfigurovat.

   ![Aktualizovat registraci aplikace.](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Budete přesměrováni na hlavní registrační stránku aplikace, což otevře **nastavení** stránky pro aplikaci. Chcete-li přidat tajný klíč pro přihlašovací údaje vaší webové aplikace:
  - Klikněte na tlačítko **klíče** části na **nastavení** stránky. 
  - Přidáte popis pro váš klíč.
  - Vyberte jeden nebo dva roky trvání.
  - Klikněte na **Uložit**. Krajní pravý sloupec bude obsahovat hodnotu, po uložení změn konfigurace. **Nezapomeňte si zkopírovat klíč** pro použití v kódu aplikace klienta, protože není k dispozici po opuštění této stránky.

  ![Aktualizace registrace aplikace – klíče](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-keys.png)

5. Chcete-li přidat oprávnění pro přístup k prostředku rozhraní API z klienta
  - Klikněte na tlačítko **požadovaná oprávnění** části na **nastavení** stránky. 
  - Klikněte na tlačítko **Přidat**.
  - Klikněte na tlačítko **vyberte rozhraní API** vyberte typ prostředků chcete si můžete vybrat z.
  - Projděte si seznam dostupných rozhraní API nebo použijte vyhledávací pole a vyberte z dostupných prostředků aplikací ve vašem adresáři, která zpřístupňují webové rozhraní API. Klikněte na prostředek mají zájem, a klikněte na tlačítko **vyberte**.
  - Přejdete tak **povolit přístup z** stránky. Vyberte oprávnění aplikace a/nebo delegovaná oprávnění aplikace musí při přístupu k rozhraní API.
   
  ![Aktualizace registrace aplikace – oprávnění rozhraní api](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-api.png)

  ![Aktualizace registrace aplikace – oprávnění oprávnění](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms.png)

6. Až budete hotovi, klikněte na tlačítko **vyberte** tlačítko **povolit přístup z** stránky, pak bude **provádí** tlačítko **přístup přes rozhraní API přidat** stránky. Budete přesměrováni **požadovaná oprávnění** stránku, kde nový prostředek se přidá do seznamu rozhraní API.

  > [!NOTE]
  > Kliknutím **provádí** tlačítko také automaticky nastaví oprávnění pro vaši aplikaci ve vašem adresáři na základě oprávnění k ostatním aplikacím, které jste nakonfigurovali. Tato oprávnění aplikací můžete zobrazit pomocí aplikace **nastavení** stránky.
  > 
  > 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurace aplikace prostředků k vystavení webové rozhraní API

Můžete vyvíjet webové rozhraní API a zpřístupní ji pro klientské aplikace při zavedení přístup [obory](active-directory-dev-glossary.md#scopes) a [role](active-directory-dev-glossary.md#roles). Správně nakonfigurovanou webové rozhraní API je k dispozici stejně jako ostatní Microsoft webové rozhraní API, včetně rozhraní Graph API a rozhraní API Office 365. Obory přístupu a role jsou přístupné prostřednictvím vaší [manifest aplikace](active-directory-dev-glossary.md#application-manifest), což je soubor JSON, který představuje konfigurace identity vaší aplikace. 

Následující části se dozvíte, jak vystavit oborů přístupu úpravou manifestu aplikace prostředků.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Přidání oborů přístup do vaší aplikace prostředků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet poskytuje přístup k více než jeden, klikněte na svůj účet v pravém horním rohu a nastavte portálu relace na požadované služby Azure AD tenanta.

3. V levém navigačním podokně klikněte **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikací**, potom hledání nebo klikněte na aplikaci, kterou chcete konfigurovat.

   ![Aktualizovat registraci aplikace.](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

4. Budete přesměrováni na hlavní registrační stránku aplikace, což otevře **nastavení** stránky pro aplikaci. Přepněte **úpravy manifestu** stránku kliknutím **Manifest** ze stránky registrace vaší aplikace. Otevře se editor manifestu založeného na webu, umožňuje **upravit** manifest v rámci portálu. Volitelně můžete kliknout na **Stáhnout** a upravovat místně, a potom pomocí **nahrát** znovu do vaší aplikace.

5. V tomto příkladu jsme bude vystavovat nový obor volá `Employees.Read.All` na náš prostředek nebo rozhraní API, tak, že přidáte následující element JSON do `oauth2Permissions` kolekce. Existující `user_impersonation` oboru je dostupné ve výchozím nastavení během registrace. `user_impersonation` Umožňuje klientské aplikaci požádat o oprávnění k přístupu k prostředku, v části identita přihlášeného uživatele. Nezapomeňte přidat čárku za stávající `user_impersonation` obor element a změnit hodnoty vlastností tak, aby vyhovovala potřebám vašeho prostředku. 

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
  > Hodnota "id" je nutné vygenerovat pomocí nástroje pro generování identifikátoru GUID, například [Guidgen –](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) nebo prostřednictvím kódu programu. Jak je zveřejněn prostřednictvím webového rozhraní API představuje jedinečný identifikátor pro tento obor. Jakmile klient je správně nakonfigurovaný s oprávněními pro přístup k webové rozhraní API, jeho vydání přístupového tokenu OAuth 2.0 ve službě Azure AD. Při volání klienta webového rozhraní API představuje přístupový token, který má obor (scp) deklarace identity nastavena oprávnění požadovaná v jeho registraci aplikace.
  >
  > Další obory později podle potřeby můžete zveřejnit. Vezměte v úvahu, že vaše webové rozhraní API může zveřejnit víc oborů, které jsou spojené s celou řadu různých funkcí. Váš prostředek můžete řídit přístup k webovému rozhraní API za běhu, vyhodnocení oboru (`scp`) deklarace identity v přijatý přístupový token OAuth 2.0.
  > 

6. Až budete hotovi, klikněte na tlačítko **Uložit**. Webové rozhraní API je nyní nakonfigurován pro použití jinými aplikacemi ve vašem adresáři. 

  ![Aktualizovat registraci aplikace.](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-manifest.png)

#### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Ověření webové rozhraní API je k dispozici k ostatním aplikacím ve vašem tenantovi
1. Přejděte zpět do služby Azure AD tenanta, klikněte na tlačítko **registrace aplikací** znovu, potom hledání nebo klikněte na klientské aplikace, kterou chcete konfigurovat.

   ![Aktualizovat registraci aplikace.](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration.png)

2. Krok 5 opakujte, jako jste to udělali [konfigurovat klientskou aplikaci pro přístup k webovým rozhraním API](#configure-a-client-application-to-access-web-apis). Když se zobrazí **vyberte rozhraní API** kroku, vyhledejte prostředek tak, že do vyhledávacího pole zadáte její název aplikace a klikněte na tlačítko **vyberte**. 

3. Na **povolit přístup z** stránky, měli byste vidět nový obor, která je k dispozici pro požadavky klientů oprávnění.

  ![Nová oprávnění jsou uvedeny.](./media/quickstart-v1-integrate-apps-with-azure-ad/update-app-registration-settings-permissions-perms-newscopes.png)

#### <a name="more-on-the-application-manifest"></a>Další informace o manifestu aplikace

Manifest aplikace se ve skutečnosti slouží jako mechanismus pro aktualizaci entity aplikaci, která definuje všechny atributy konfigurace vytvoří aplikaci Azure AD identity, včetně oborů přístupu rozhraní API, kterou jsme probírali. Další informace o aplikaci entity a jeho schématu, najdete v článku [dokumentace entity aplikace rozhraní API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Tento článek obsahuje úplnou referenční informace u členů entity aplikace používá k určení oprávnění pro vaše rozhraní API, včetně:  

- Člen appRoles, což je kolekce z [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) entity, používá k definování [oprávnění aplikace](active-directory-dev-glossary.md#permissions) webového rozhraní API. 
- Člen oauth2Permissions, což je kolekce z [instancí OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) entity, používá k definování [delegovaná oprávnění](active-directory-dev-glossary.md#permissions) webového rozhraní API.

Další informace o aplikaci manifest obecné koncepty, najdete v článku [vysvětlení manifestu aplikace Azure Active Directory](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Přístup k Azure AD Graphu a Office 365 přes rozhraní API Microsoft Graphu  

Jak už bylo zmíněno dříve, kromě vystavení/přístup k rozhraní API pro vlastní aplikace, můžete se zaregistrovat klientská aplikace mohla přístup k rozhraním API zpřístupněné nástrojem prostředky Microsoftu. Rozhraní Microsoft Graph API, označuje jako "Microsoft Graphu" v seznamu prostředků nebo rozhraní API na portálu, je k dispozici pro všechny aplikace, které jsou registrované pomocí Azure AD. Pokud registrujete klientskou aplikaci v tenantovi, která obsahuje správcovské účty, které si zaregistrovali předplatné Office 365, můžete také přistupovat obory vystavené různým prostředkům Office 365.

Úplné informace o oborech vystavené Microsoft Graph API najdete v článku [referenční dokumentace k Microsoft Graphu oprávněním](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference) článku.

> [!NOTE]
> Kvůli aktuálním omezením nativní klientské aplikace může volat pouze do Azure AD Graph API je-li využívají oprávnění "Přístup k adresáři vaší organizace.". Toto omezení neplatí pro webové aplikace.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Konfigurace aplikace s více tenanty

Při registraci aplikace ve službě Azure AD, můžete aplikaci mít přístup jenom uživatelé ve vaší organizaci. Alternativně můžete také vaši aplikaci, která bude přístupné uživatelům v externích organizacích. Tyto typy aplikací se nazývají jednoho tenanta a víceklientské aplikace. Tato část popisuje, jak změnit konfiguraci aplikace jedním tenantem, aby byla aplikace s více tenanty.

Je důležité si uvědomit rozdíly mezi jednoho tenanta a víceklientské aplikace:  

- Aplikace jedním tenantem je určena pro použití v jedné organizaci. Obvykle se jedná o aplikaci (LoB) z podnikové autorem podnikový vývojář. Aplikace jedním tenantem je přístupný pouze uživatelé s účty ve stejném tenantovi jako registrace aplikací. V důsledku toho je pouze potřeba zřídit v jednom adresáři.
- Aplikace s více tenanty je určena pro použití v mnoha organizacích. Označuje jako webová aplikace software jako služba (SaaS), to je obvykle zapsal nezávislý výrobce softwaru (ISV). Aplikace s více tenanty musí být zřízený v každého klienta, kde uživatelé potřebovat přístup. U klientů, než je ten, ve kterém aplikace bude zaregistrovaná je potřeba, abyste mohli zaregistrovat souhlas uživatele nebo správce. Všimněte si, že nativní klientské aplikace jsou ve výchozím nastavení s více tenanty, jako jsou nainstalované na zařízení vlastníka prostředku. Viz předchozí [přehled rozhraní pro udělování souhlasu](#overview-of-the-consent-framework) podrobné informace o rozhraní pro udělování souhlasu.

Vytváření víceklientských aplikací vyžaduje obě aplikace registrace změny, jakož i změny v samotné webové aplikace. Následující části se věnují i.

#### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Změna registrace aplikace pro podporu více tenantů

Pokud vytváříte aplikaci, kterou chcete zpřístupnit vašim zákazníkům nebo partnerům mimo vaši organizaci, musíte aktualizovat definici aplikace na webu Azure Portal.

> [!IMPORTANT]
> Azure AD vyžaduje z aplikací s více tenanty být globálně jedinečný identifikátor URI ID aplikace. Identifikátor URI ID aplikace je jedním ze způsobů, které aplikace je definována ve zprávách protokolu. Pro jednoho tenanta aplikaci je dostatečná pro identifikátor URI ID aplikace být jedinečný v rámci tohoto tenanta. Pro aplikaci s více tenanty musí být globálně jedinečný, Azure AD, najdete tuto aplikaci ve všech tenantech. Globální jedinečnost vynucuje vyžadováním identifikátor URI ID aplikace, aby název hostitele, který odpovídá ověřené domény tenanta Azure AD. Například pokud je název tenanta contoso.onmicrosoft.com pak platný identifikátor URI ID aplikace by https://contoso.onmicrosoft.com/myapp. Pokud váš tenant má ověřenou doménu contoso.com, pak by také platný identifikátor URI ID aplikace https://contoso.com/myapp. Pokud identifikátor URI ID aplikace není postupovat podle tohoto vzoru, nastavení aplikace jako více tenantů se nezdaří.
> 

Chcete-li umožnit externím uživatelům přístup k aplikaci: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet poskytuje přístup k více než jeden, klikněte na svůj účet v pravém horním rohu a nastavte portálu relace na požadované služby Azure AD tenanta.
3. V levém navigačním podokně klikněte **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikací**, potom hledání nebo klikněte na aplikaci, kterou chcete konfigurovat. Budete přesměrováni na hlavní registrační stránku aplikace, což otevře **nastavení** stránky pro aplikaci.
4. Z **nastavení** klikněte na **vlastnosti** a změnit **víceklientských** přepnout na **Ano**.

Po provedení změny uživatelů a správců v jiných organizacích můžou poskytnout svým uživatelům možnost přihlásit do aplikace, tím umožní vaší aplikaci přístup k prostředkům zabezpečeným přes svého tenanta.

#### <a name="changing-the-application-to-support-multi-tenant"></a>Změna aplikace pro podporu více tenantů

Podpora pro aplikace s více tenanty spoléhá na rozhraní pro udělování souhlasu Azure AD. Vyjádření souhlasu je mechanismus, který umožňuje uživateli z jiného tenanta k udělení přístupu aplikace k prostředkům zabezpečeným přes tenanta uživatele. Toto prostředí se označuje jako "souhlas uživatele".

Webové aplikace může také nabídky:

- Možnosti pro správce "Zaregistrujte své firmě." Toto prostředí, označuje jako "souhlas správce", nabízí správce funkce udělit souhlas jménem *všichni uživatelé* v jejich organizaci. Pouze uživatel, který se ověřuje pomocí účtu, který patří k roli globálního správce může poskytnout souhlas správce; ostatní uživatelé dostanou k chybě.

- Registrace prostředí pro uživatele. Očekává se, že uživateli se nabídnou "registrace" tlačítko, které Azure AD OAuth2.0 přesměruje prohlížeč `/authorize` koncového bodu nebo OpenID Connect `/userinfo` koncového bodu. Tyto koncové body umožňují aplikaci zobrazíte informace o novém uživateli zkontrolováním požadavku id_token. Následující fáze registrace se zobrazí uživatele s výzvou souhlasu, podobně jako z článku [přehled rozhraní pro udělování souhlasu](#overview-of-the-consent-framework) oddílu.

Další informace o změny aplikace, které jsou potřeba pro podporu přístupu s více klienty a možnosti přihlašování – v registrace/přihlášení naleznete v tématu:

- [Postup přihlášení libovolného uživatele služby Azure Active Directory (AD) pomocí vzoru aplikace s více tenanty](howto-convert-app-to-be-multi-tenant.md)
- Seznam [ukázky kódu pro více tenantů](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant). 
- [Rychlý start: Přidání firemního brandingu na přihlašovací stránku ve službě Azure AD](../fundamentals/customize-branding.md)

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Povolení OAuth 2.0 implicitní udělení pro jednostránkové aplikace

Jediné stránce aplikace (SPA) jsou obvykle strukturované s front-endem JavaScript náročná na výkon, na kterém běží v prohlížeči, která volá vaší aplikace webového rozhraní API back-end pro provádění své obchodní logiky. Pro SPA hostované ve službě Azure AD použijte implicitní Grant OAuth 2.0 k ověření uživatele pomocí Azure AD a získá token, který vám pomůže zabezpečit volání z aplikace JavaScript klienta do jeho back endové webové rozhraní API. 

Po udělení souhlasu uživatele Tento stejný protokol ověřování lze použít k získání tokenů zabezpečení volání mezi klientem a dalších webových rozhraní API prostředcích nakonfigurovaných pro aplikaci. Další informace o implicitním udělení autorizace a vám pomohou rozhodnout, jestli je vhodná pro váš scénář aplikace najdete v tématu [Principy OAuth2 implicitní tok ve službě Azure Active Directory udělit](v1-oauth2-implicit-grant-flow.md).

Implicitní Grant OAuth 2.0 je ve výchozím nastavení zakázána pro aplikace. Implicitní Grant OAuth 2.0 můžete povolit pro vaši aplikaci tím, že nastavíte `oauth2AllowImplicitFlow` hodnota v jeho [manifest aplikace](active-directory-application-manifest.md).

#### <a name="to-enable-oauth-20-implicit-grant"></a>Povolit implicitní grant OAuth 2.0

> [!NOTE]
> Pro podrobnosti o tom, jak upravit manifest aplikace je potřeba nejdřív podívat na téma v předchozí části [konfigurace aplikace prostředků k vystavení webové rozhraní API](#configuring-a-resource-application-to-expose-web-apis).
>

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet poskytuje přístup k více než jeden, klikněte na svůj účet v pravém horním rohu a nastavte portálu relace na požadované služby Azure AD tenanta.
3. V levém navigačním podokně klikněte **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikací**, potom hledání nebo klikněte na aplikaci, kterou chcete konfigurovat. Budete přesměrováni na hlavní registrační stránku aplikace, což otevře **nastavení** stránky pro aplikaci.
4. Přepněte **úpravy manifestu** stránku kliknutím **Manifest** ze stránky registrace vaší aplikace. Otevře se editor manifestu založeného na webu, umožňuje **upravit** manifest v rámci portálu. Vyhledejte a nastavte hodnotu "oauth2AllowImplicitFlow" na "true". Ve výchozím nastavení je nastavena na hodnotu "false".
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. Uložte aktualizovaný manifest. Po uložení vašeho webového rozhraní API je nyní nakonfigurováno pro použití implicitní Grant OAuth 2.0 pro ověřování uživatelů.

## <a name="removing-an-application"></a>Odebrání aplikace
Tato část popisuje, jak odebrat registraci aplikace ve svém tenantovi Azure AD.

### <a name="removing-an-application-authored-by-your-organization"></a>Odebrání aplikace vytvořené vaší organizací
Aplikace, které vaše organizace zaregistrovala se zobrazí v rámci filtr "Aplikace" na stránce hlavní "registrace aplikací" vašeho tenanta. Tyto aplikace jsou ty, které jste zaregistrovali ručně přes Azure portal nebo prostřednictvím kódu programu prostřednictvím Powershellu nebo rozhraní Graph API. Přesněji řečeno jsou reprezentované pomocí aplikaci a instanční objekt ve vašem tenantovi. Další informace najdete v tématu [aplikace a instanční objekty](app-objects-and-service-principals.md).

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Pro jednoho tenanta aplikaci odebrat z adresáře
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet poskytuje přístup k více než jeden, klikněte na svůj účet v pravém horním rohu a nastavte portálu relace na požadované služby Azure AD tenanta.
3. V levém navigačním podokně klikněte **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikací**, potom hledání nebo klikněte na aplikaci, kterou chcete konfigurovat. Budete přesměrováni na hlavní registrační stránku aplikace, což otevře **nastavení** stránky pro aplikaci.
4. Na stránce hlavní registrace vaší aplikace, klikněte na tlačítko **odstranit**.
5. Klikněte na tlačítko **Ano** v potvrzovací zprávě.

#### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Chcete-li odebrat aplikaci s více tenanty z jeho domovského adresáře
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet poskytuje přístup k více než jeden, klikněte na svůj účet v pravém horním rohu a nastavte portálu relace na požadované služby Azure AD tenanta.
3. V levém navigačním podokně klikněte **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikací**, potom hledání nebo klikněte na aplikaci, kterou chcete konfigurovat. Budete přesměrováni na hlavní registrační stránku aplikace, což otevře **nastavení** stránky pro aplikaci.
4. Z **nastavení** zvolte **vlastnosti** a změnit **víceklientských** přepnout na **ne**, nejprve změn v aplikaci na jedním tenantem, pak klikněte na tlačítko **Uložit**. Instanční objekty aplikace zůstanou v tenantech všechny organizace, které mají k němu je už odsouhlasený.
5. Klikněte na **odstranit** tlačítko z hlavní registrační stránku aplikace.
6. Klikněte na tlačítko **Ano** v potvrzovací zprávě.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Odebrání aplikace s více tenanty autorizoval jiné organizaci
Podmnožinu aplikace, které se zobrazí v rámci filtr "Všechny aplikace" (s výjimkou registrace "Aplikace") na hlavní stránce "Registrace aplikací" vašeho tenanta, jsou aplikace s více tenanty. V technické podmínky tyto aplikace s více tenanty se z jiného tenanta a byly během procesu souhlasu zaregistrovat do vašeho tenanta. Přesněji řečeno jsou zobrazeny v pouze instanční objekt ve vašem tenantovi, bez odpovídajícího objektu aplikace. Další informace o rozdílech mezi aplikací a instančních objektů najdete v tématu [aplikace a instanční objekty ve službě Azure AD](app-objects-and-service-principals.md).

Pokud chcete odebrat přístup k aplikaci s více tenanty do svého adresáře (po by bylo udělení souhlasu), musí správce společnosti odebrání jeho instančního objektu. Správce musí mít přístup globální správce a můžete ji odstranit prostřednictvím webu Azure portal nebo pomocí [rutin Powershellu pro Azure AD](http://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>Další postup
- Další informace o tom, jak funguje ověřování ve službě Azure AD najdete v tématu [scénáře ověřování pro službu Azure AD](authentication-scenarios.md).
- Najdete v článku [Branding pokyny pro integrované aplikace](howto-add-branding-in-azure-ad-apps.md) tipy na vizuální pokyny pro vaši aplikaci.
- Další informace o vztahu mezi aplikace a instanční objekty aplikace najdete v tématu [aplikace a instanční objekty](app-objects-and-service-principals.md).
- Další informace o roli plní manifestu aplikace, najdete v článku [vysvětlení manifestu aplikace Azure Active Directory](active-directory-application-manifest.md)
- Zobrazit [Glosář vývojáře Azure AD](active-directory-dev-glossary.md) obsahuje definice některých koncepce pro vývojáře základní služby Azure AD.
- Přejděte [příručky pro vývojáře Active Directory](azure-ad-developers-guide.md) získáte přehled o veškerý obsah týkající se vývojářů.

