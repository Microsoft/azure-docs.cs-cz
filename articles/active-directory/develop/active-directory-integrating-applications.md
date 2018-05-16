---
title: Integrace aplikací s Azure Active Directory
description: Postup přidání, aktualizace nebo odebrání aplikace v Azure Active Directory (Azure AD).
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
ms.openlocfilehash: 76c6ef7d4cf53872dda308628790994b35d8431c
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="integrating-applications-with-azure-active-directory"></a>Integrace aplikací s Azure Active Directory
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Podnikové vývojáře a poskytovatelé softwaru jako služba (SaaS) můžete vyvíjet komerční cloudové služby nebo -obchodní aplikace, které můžou být integrované se službou Azure Active Directory (Azure AD) k poskytování bezpečného přihlášení a autorizace pro jejich služby. Při integraci s Azure AD k aplikaci nebo službě, musí vývojář nejprve zaregistrovat aplikaci s Azure AD.

Tento článek ukazuje, jak přidávat, aktualizovat nebo odebrat registraci aplikace ve službě Azure AD. Informace o různých typech aplikací, které můžou být integrované se službou Azure AD, jak nakonfigurovat vaší aplikace pro přístup k jiným prostředkům, například webových rozhraní API a další.

Další informace o dva objekty Azure AD, které představují zaregistrovanou aplikaci a vztahů mezi nimi, najdete v části [objekty aplikací a hlavní objekty služeb](active-directory-application-objects.md); Další informace o značce pokynů byste měli používat při vývoji aplikací s Azure Active Directory, najdete v části [Branding pokyny pro integrované aplikace](active-directory-branding-guidelines.md).

## <a name="adding-an-application"></a>Přidání aplikace
Všechny aplikace, který chce využívat možnosti Azure AD musí být zaregistrován nejdřív v klientovi služby Azure AD. Zahrnuje tohoto procesu registrace poskytne Azure AD podrobnosti o aplikaci, jako je například adresa URL, kde je umístěn, adresu URL pro odeslání odpovědi po ověření uživatele je identifikátor URI, který identifikuje aplikaci a tak dále.

### <a name="to-register-a-new-application-using-the-azure-portal"></a>Zaregistrujte novou aplikaci pomocí portálu Azure
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet poskytuje přístup k více než jeden, klikněte na váš účet v pravém horním rohu a nastavte portálu relace do požadovaného Azure AD klienta.
3. V levém navigačním podokně klikněte na tlačítko **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikace**a klikněte na tlačítko **nové registrace aplikace**.

   ![Zaregistrujte novou aplikaci](./media/active-directory-integrating-applications/add-app-registration.png)

4. Když **vytvořit** se zobrazí stránka, zadejte informace o registraci vaší aplikace: 

  - **Název:** zadejte název smysluplný aplikace
  - **Typ aplikace:** 
    - Vyberte "Nativní" pro [klientské aplikace](active-directory-dev-glossary.md#client-application) , jsou nainstalovány místně na zařízení. Toto nastavení se používá pro veřejné OAuth [nativních klientů](active-directory-dev-glossary.md#native-client).
    - Vyberte "webovou aplikaci nebo API" pro [klientské aplikace](active-directory-dev-glossary.md#client-application) a [aplikace prostředků nebo rozhraní API](active-directory-dev-glossary.md#resource-server) které se instalují na zabezpečeného serveru. Toto nastavení se používá pro OAuth důvěrné [webových klientů](active-directory-dev-glossary.md#web-client) a veřejné [klienty na základě uživatelského agenta](active-directory-dev-glossary.md#user-agent-based-client). Stejnou aplikaci můžete také zveřejnit klient i prostředků nebo rozhraní API.
  - **Adresa URL přihlašování:** pro "webovou aplikaci nebo API" aplikace, zadejte základní adresu URL aplikace. Například `http://localhost:31544` může být adresa URL pro webovou aplikaci spuštěné v místním počítači. Uživatelé by používat tuto adresu URL pro přihlášení k webové aplikaci klienta. 
  - **Identifikátor URI pro přesměrování:** pro "Nativní" aplikace, zadejte identifikátor URI používá Azure AD k vrácení odpovědi tokenu. Zadejte hodnotu konkrétní k vaší aplikaci, například `http://MyFirstAADApp`

   ![Zaregistrujte novou aplikaci – vytvoření](./media/active-directory-integrating-applications/add-app-registration-create.png)

   Pokud chcete konkrétní příklady pro webové aplikace nebo nativních aplikací, podívejte se na naše [– elementy QuickStart](active-directory-developers-guide.md#get-started).

5. Po dokončení klikněte na tlačítko **vytvořit**. Azure AD přiřadí jedinečné ID aplikace do aplikace a se stránka hlavní registrace vaší aplikace. V závislosti na tom, zda je aplikace na web nebo nativní aplikaci najdete různé možnosti pro přidání dalších možností do vaší aplikace. Viz další část Přehled souhlasu a podrobnosti o povolení funkce další konfigurace v registraci vaší aplikace (přihlašovací údaje, oprávnění Povolit přihlášení pro uživatele z jiných klientů.)

  > [!NOTE]
  > Ve výchozím nastavení je nově zaregistrovaný webové aplikace nakonfigurovaná umožňující **pouze** uživatelé z stejné klienta pro přihlášení k aplikaci.
  > 
  > 

## <a name="updating-an-application"></a>Aktualizace aplikace
Jakmile aplikaci byl registrován u služby Azure AD, bude pravděpodobně nutné aktualizovat tak, aby poskytnout přístup k webovému rozhraní API, budou dostupné v jiných organizací a další. Tato část popisuje různé způsoby, ve kterých můžete nakonfigurovat další aplikace. Nejdříve začneme přehled souhlasu rozhraní, které je důležité si uvědomit, při vytváření aplikace, které je třeba používat jiné uživatele nebo aplikace.

### <a name="overview-of-the-consent-framework"></a>Přehled rozhraní souhlasu

Rozhraní Azure AD souhlasu usnadňuje vývoj víceklientské web a nativní klientské aplikace. Tyto aplikace povolí přihlášení uživatelské účty z tenanta služby Azure AD, liší od verze, kde je registrovaná aplikace. Také budou potřebovat pro přístup k webové rozhraní API, jako je například Microsoft Graph API (pro přístup k Azure Active Directory, Intune a službám Office 365) a rozhraní API jiných služeb společnosti Microsoft, kromě vlastní webové rozhraní API. Rozhraní je založena na uživatele nebo správce udělení souhlasu k aplikaci, která požaduje být registrováno v jejich adresáři, který může zahrnovat přístup k datům adresáře.

Například pokud webovou aplikaci klienta potřebuje ke čtení informací z kalendáře o uživateli z Office 365, tento uživatel je potřeba nejdřív souhlas do klientské aplikace. Po lze souhlasu, nebudou klientská aplikace volání rozhraní Microsoft Graph API jménem uživatele, a podle potřeby použijte informace v kalendáři. [Microsoft Graph API](https://graph.microsoft.io) poskytuje přístup k datům v Office 365 (jako je například kalendáře a zprávy z Exchange, weby a seznamy ze služby SharePoint, dokumenty z Onedrivu, poznámkových bloků z aplikace OneNote, úlohy z Planner, sešity z aplikace Excel, atd.), a také uživatelé a skupiny z Azure AD a jiných datových objektů z více cloudových služeb Microsoftu. 

Rozhraní framework souhlasu je založen na OAuth 2.0 a jeho různé toky, například kód grant a klient přihlašovací údaje pro autorizaci udělit, pomocí veřejného nebo důvěrné klientů. Pomocí standardu OAuth 2.0, Azure AD umožňuje vytvářet mnoho různých typů klientské aplikace, například na telefon, tablet, server nebo webovou aplikaci, a získat přístup k požadované prostředky.

Další informace o používání rozhraní souhlasu s autorizací udělí OAuth2.0 najdete v tématu [autorizace přístupu k webovým aplikacím pomocí OAuth 2.0 a Azure AD](active-directory-protocols-oauth-code.md) a [scénáře ověřování pro Azure AD](active-directory-authentication-scenarios.md). Informace o získání autorizovaný přístup k Office 365 přes Microsoft Graph najdete v tématu [ověřování aplikací s Microsoft Graph](https://graph.microsoft.io/docs/authorization/auth_overview).

#### <a name="example-of-the-consent-experience"></a>Příklad souhlasu prostředí

Následující kroky vám ukážou, jak souhlasu prostředí funguje pro vývojáře aplikací a uživatele.

1. Předpokládejme, že máte webové aplikace klienta, které je potřeba požádat o konkrétní oprávnění pro přístup k prostředku nebo rozhraní API. Dozvíte, jak to provést tuto konfiguraci v další části, ale v podstatě portálu Azure se používá k deklaraci žádosti o oprávnění v době konfigurace. Jako další nastavení konfigurace se stávají součástí registrace aplikace Azure AD:
   
  ![Oprávnění k ostatním aplikacím](./media/active-directory-integrating-applications/requiredpermissions.png)
    
2. Zvažte oprávnění aplikace byly aktualizovány, je aplikace spuštěna a uživatel je použijte první. Nejprve aplikace musí získat autorizační kód z Azure AD `/authorize` koncový bod. Autorizační kód se pak lze získat přístup k nové a aktualizovat token.

3. Pokud uživatel ještě není ověřený, Azure AD na `/authorize` koncový bod zobrazí výzvu k přihlášení.
   
  ![Uživatel nebo správce přihlášení do služby Azure AD](./media/active-directory-integrating-applications/usersignin.png)

4. Jakmile se uživatel přihlásil, Azure AD se určují, jestli uživatel musí zobrazený na stránce souhlasu. Toto rozhodnutí je založená na tom, jestli uživatel (nebo správce jeho organizace) již udělil souhlas aplikace. Pokud již byla nebyl udělen souhlas, Azure AD zobrazí výzvu k souhlasu a zobrazí požadovaná oprávnění, musí se funkce. Sadu oprávnění, které se zobrazují v dialogovém okně souhlasu odpovídat souborů vybraných v delegovaná oprávnění na webu Azure portal.
   
  ![Činnost koncového uživatele souhlasu](./media/active-directory-integrating-applications/consent.png)

5. Jakmile uživatel uděluje souhlas, autorizační kód se vrátí do vaší aplikace, které je uplatněn získat přístupový token a aktualizujte token. Další informace o tomto toku najdete v tématu [webové aplikace do webové části rozhraní API v scénáře ověřování pro Azure AD](active-directory-authentication-scenarios.md#web-application-to-web-api).

6. Jako správce můžete také souhlas přidělená oprávnění aplikace jménem všechny uživatele ve vašem klientovi. Správce souhlasu bránil zobrazení dialogu souhlasu pro každého uživatele v klientovi a můžete udělat v [portál Azure](https://portal.azure.com) uživatelé s rolí správce. Z **nastavení** stránky pro vaši aplikaci, klikněte na tlačítko **požadovaných oprávnění** a klikněte na **udělit oprávnění** tlačítko. 

  ![Udělení oprávnění pro explicitní správce souhlasu](./media/active-directory-integrating-applications/grantpermissions.png)
    
  > [!NOTE]
  > Udělení explicitní souhlas pomocí **udělit oprávnění** tlačítko je momentálně nevyžaduje pro jednostránkové aplikace (SPA), které používají ADAL.js. Aplikace, jinak selže, pokud se požaduje přístupový token. 

### <a name="configure-a-client-application-to-access-web-apis"></a>Konfigurovat klientskou aplikaci pro přístup k webové rozhraní API
Aby webové nebo důvěrné klientskou aplikaci, aby mohli účastnit tok udělení autorizace, který vyžaduje ověření (a získat přístupový token) je potřeba vytvořit zabezpečené přihlašovací údaje. Výchozí metoda ověřování nepodporuje portál Azure je ID klienta a tajný klíč. Tato část obsahuje kroky konfigurace, které jsou nutné k zajištění tajný klíč s přihlašovacími údaji vašeho klienta.

Kromě toho předtím, než klient může získat přístup k webové rozhraní API vystavené prostředků aplikace (například Microsoft Graph API), rozhraní souhlasu zajistí, klient získá udělení oprávnění, která je potřeba, na základě požadovaná oprávnění. Všechny aplikace ve výchozím nastavení, můžete vybrat oprávnění z "Windows Azure Active Directory" (rozhraní Graph API) a "Systém Windows Azure Service Management API." ["Přihlášení a čtení uživatelský profil" oprávnění rozhraní Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes#PermissionScopeDetails) , vybere se také ve výchozím nastavení. Pokud váš klient je registrována v klientovi, který má předplatné Office 365 účty, jsou k dispozici pro výběr webového rozhraní API a oprávnění pro služby SharePoint a Exchange Online. Můžete vybrat z [dva typy oprávnění](active-directory-dev-glossary.md#permissions) pro všechny potřeby webového rozhraní API:

- Oprávnění aplikací: Klientské aplikace potřebuje přístup k webové rozhraní API přímo jako samotný (žádný kontext uživatele). Tento typ oprávnění vyžaduje souhlas správce a není k dispozici pro nativní klientské aplikace.

- Přidělená oprávnění: Klientské aplikace potřebuje přístup k webové rozhraní API jako přihlášeného uživatele, ale přístup omezen vybrané oprávnění. Tento typ oprávnění můžete udělit uživatelem, pokud oprávnění vyžaduje souhlas správce. 

  > [!NOTE]
  > Přidání delegovaného oprávnění k aplikaci neuděluje automaticky souhlas pro uživatele v rámci klienta. Uživatelé musí ručně souhlas pro přidané přidělená oprávnění za běhu, pokud správce klikne **udělit oprávnění** tlačítko z **požadovaných oprávnění** části stránka aplikace v portálu Azure. 

#### <a name="to-add-application-credentials-or-permissions-to-access-web-apis"></a>Chcete-li přidat přihlašovací údaje aplikací nebo oprávnění pro přístup k webovým rozhraním API
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet poskytuje přístup k více než jeden, klikněte na váš účet v pravém horním rohu a nastavte portálu relace do požadovaného Azure AD klienta.
3. V levém navigačním podokně klikněte **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikace**, pak najít nebo klikněte na aplikaci, kterou chcete konfigurovat.

   ![Aktualizace registrace aplikace](./media/active-directory-integrating-applications/update-app-registration.png)

4. Budete přesměrováni na stránku registrace hlavní aplikace, což otevře **nastavení** stránku pro aplikaci. Chcete-li přidat tajný klíč pro přihlašovací údaje vaší webové aplikace:
  - Klikněte **klíče** části na **nastavení** stránky. 
  - Přidejte popis pro váš klíč.
  - Vyberte jeden nebo dva roky doba trvání.
  - Klikněte na **Uložit**. Sloupec nejvíce vpravo bude obsahovat hodnotou klíče, po uložení změn konfigurace. **Nezapomeňte zkopírovat klíč** pro použití v kódu aplikace klienta, protože není přístupná jednou tuto stránku opustíte.

  ![Aktualizace registrace aplikace - klíče](./media/active-directory-integrating-applications/update-app-registration-settings-keys.png)

5. Chcete-li přidat oprávnění pro přístup k prostředku rozhraní API z vašeho klienta
  - Klikněte **požadovaných oprávnění** části na **nastavení** stránky. 
  - Klikněte na tlačítko **Přidat**.
  - Klikněte na tlačítko **vybrat rozhraní API** vyberte typ prostředků, kterou chcete vybrat z.
  - Procházet seznam dostupných rozhraní API nebo použijte pole hledání a vyberte z dostupných prostředků aplikací ve vašem adresáři, které zveřejňují webového rozhraní API. Klikněte na prostředek zájem a pak klikněte na **vyberte**.
  - Se **povolit přístup** stránky. Vyberte oprávnění aplikací nebo delegovaná oprávnění aplikace musí při přístupu k rozhraní API.
   
  ![Aktualizace registrace aplikace - oprávnění rozhraní api](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-api.png)

  ![Aktualizace registrace aplikace - oprávnění oprávnění](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms.png)

6. Po dokončení klikněte na tlačítko **vyberte** na tlačítko **povolit přístup** stránky, pak se **provádí** na tlačítko **přístup přidat rozhraní API** stránky. Vrátíte se **požadovaná oprávnění** stránky, kde je do seznamu rozhraní API přidat nový prostředek.

  > [!NOTE]
  > Kliknutím **provádí** tlačítko taky automaticky nastaví oprávnění pro vaši aplikaci ve vašem adresáři na základě oprávnění k ostatním aplikacím, které jste nakonfigurovali. Tato oprávnění aplikací můžete zobrazit prohlížením aplikace **nastavení** stránky.
  > 
  > 

### <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurace prostředků aplikace ke zveřejnění webových rozhraní API

Můžete vyvíjet webové rozhraní API a nastavit jej jako dostupný pro klientské aplikace díky zpřístupnění přístup [obory](active-directory-dev-glossary.md#scopes) a [role](active-directory-dev-glossary.md#roles). Správně nakonfigurována webového rozhraní API je k dispozici stejně jako ostatní Microsoft webové rozhraní API, včetně rozhraní Graph API a rozhraní API Office 365. Obory přístupu a rolí se zveřejňují přes vaše [manifestu aplikace](active-directory-dev-glossary.md#application-manifest), což je soubor JSON, který představuje konfiguraci identity vaší aplikace. 

Následující části se dozvíte, jak vystavit oborů přístupu změnou manifest prostředků aplikace.

#### <a name="adding-access-scopes-to-your-resource-application"></a>Přidání oborů přístupu k vaší aplikaci prostředků

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet poskytuje přístup k více než jeden, klikněte na váš účet v pravém horním rohu a nastavte portálu relace do požadovaného Azure AD klienta.

3. V levém navigačním podokně klikněte **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikace**, pak najít nebo klikněte na aplikaci, kterou chcete konfigurovat.

   ![Aktualizace registrace aplikace](./media/active-directory-integrating-applications/update-app-registration.png)

4. Budete přesměrováni na stránku registrace hlavní aplikace, což otevře **nastavení** stránku pro aplikaci. Přepnout **úpravy manifest** stránku kliknutím **Manifest** ze stránky registrace aplikace. Otevře webové manifestu editor, umožní **upravit** manifestu v rámci portálu. Volitelně můžete kliknout na **Stáhnout** a upravit místně, potom použijte **nahrát** znovu do vaší aplikace.

5. V tomto příkladu bude zveřejňujeme nového oboru názvem `Employees.Read.All` na našem prostředků nebo rozhraní API, přidáním následující elementu JSON, který chcete `oauth2Permissions` kolekce. Existující `user_impersonation` obor je dostupné ve výchozím nastavení během registrace. `user_impersonation` Umožňuje klientskou aplikaci, aby žádala o oprávnění pro přístup k prostředku, v části identita přihlášeného uživatele. Nezapomeňte přidat čárkou po existující `user_impersonation` obor elementu a změnit hodnoty vlastností, aby odpovídaly potřebám vaší prostředků. 

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
  > Hodnota "id" musí být generována pomocí nástroj pro vytváření identifikátor GUID, jako [Guidgen –](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) nebo prostřednictvím kódu programu. Jedinečný identifikátor pro obor, představuje jako vystavené webové rozhraní API. Jakmile klient je správně nakonfigurovaný s oprávněními pro přístup k vašemu webovému rozhraní API, jeho vydání OAuth2.0 přístupový token služby Azure AD. Při volání klienta webového rozhraní API představuje přístupový token, který má obor (scp) deklarace identity nastavit oprávnění požadované v jeho registraci aplikace.
  >
  > Další obory později podle potřeby můžete vystavit. Zvažte, zda webového rozhraní API mohou být vystaveny víc oborů, které jsou spojené s celou řadu různých funkcí. Prostředek můžete řídit přístup k webovému rozhraní API v době běhu vyhodnocením oboru (`scp`) deklarace identity v přijatý přístupový token OAuth 2.0.
  > 

6. Po dokončení klikněte na tlačítko **Uložit**. Webové rozhraní API je teď nakonfigurovaný na používání jiné aplikace ve vašem adresáři. 

  ![Aktualizace registrace aplikace](./media/active-directory-integrating-applications/update-app-registration-manifest.png)

#### <a name="verify-the-web-api-is-exposed-to-other-applications-in-your-tenant"></a>Ověřte webová rozhraní API je vystaven k ostatním aplikacím ve vašem klientovi
1. Přejděte zpět do služby Azure AD klienta, klikněte na tlačítko **registrace aplikace** znovu, potom najít nebo klikněte na klientskou aplikaci, kterou chcete konfigurovat.

   ![Aktualizace registrace aplikace](./media/active-directory-integrating-applications/update-app-registration.png)

2. Krok 5 opakujte, stejně jako v [konfigurovat klientskou aplikaci pro přístup k webové rozhraní API](#configure-a-client-application-to-access-web-apis). Jakmile přejdete **vybrat rozhraní API** krok, vyhledejte prostředek zadáním názvu aplikace do pole hledání a klikněte na **vyberte**. 

3. Na **povolit přístup** stránky, měli byste vidět nový obor, k dispozici pro požadavky klientů oprávnění.

  ![Nová oprávnění jsou zobrazeny.](./media/active-directory-integrating-applications/update-app-registration-settings-permissions-perms-newscopes.png)

#### <a name="more-on-the-application-manifest"></a>Další informace o manifest aplikace

Manifest aplikace se ve skutečnosti slouží jako mechanismus pro aktualizaci entity aplikace, která definuje konfiguraci identity aplikaci Azure AD, včetně oborů přístupu rozhraní API, které jsme probrali všechny atributy. Další informace o entity aplikací a schématem, najdete v článku [aplikace Graph API entity dokumentaci](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). Článek obsahuje informace o úplný odkaz na členy entit aplikace, který slouží k zadání oprávnění pro vaše rozhraní API, včetně:  

- AppRoles člena, který je kolekce z [aplikační role](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) entity, používá k definování [oprávnění aplikací](active-directory-dev-glossary.md#permissions) pro webové rozhraní API. 
- Oauth2Permissions člena, který je kolekce z [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) entity, používá k definování [delegovaná oprávnění](active-directory-dev-glossary.md#permissions) pro webové rozhraní API.

Další informace o aplikaci manifest obecné koncepty, najdete v části [pochopení manifest aplikace Azure Active Directory](active-directory-application-manifest.md).

### <a name="accessing-the-azure-ad-graph-and-office-365-via-microsoft-graph-apis"></a>Přístup k Azure AD Graph a Office 365 přes rozhraní API pro Microsoft Graph  

Jak už bylo zmíněno dříve, kromě vystavení nebo přístup k rozhraní API pro vaše vlastní aplikace, můžete zaregistrovat klientskou aplikaci pro přístup k rozhraní API vystavené zdrojů společnosti Microsoft. Rozhraní Microsoft Graph API, označuje jako "Microsoft Graph" v seznamu prostředků nebo rozhraní API na portálu, je k dispozici pro všechny aplikace, které jsou registrovány s Azure AD. Pokud registrujete klientskou aplikaci v klientovi, který obsahuje účty, které jste se přihlásili pro předplatné služeb Office 365, můžete taky přejít obory vystavené různé prostředky Office 365.

Úplné informace o oborech vystavené Microsoft Graph API, najdete v článku [Microsoft Graph oprávnění odkaz](https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference) článku.

> [!NOTE]
> Kvůli aktuálním omezením nativní klientské aplikace může volat pouze do Azure AD Graph API pokud používají oprávnění "Přístup k adresáři vaší organizace". Toto omezení neplatí pro webové aplikace.
> 
> 

### <a name="configuring-multi-tenant-applications"></a>Konfigurace aplikací pro více klientů

Při registraci aplikace ve službě Azure AD, můžete aplikace měly přístup jenom uživatelé ve vaší organizaci. Alternativně můžete také vaší aplikaci, která bude přístupný pro uživatele v externími organizacemi. Tyto typy dvě aplikace se říká aplikace jednoho klienta a víc klientů. Tato část popisuje postup úpravy konfigurace aplikace jednoho klienta, aby víceklientské aplikace.

Je důležité si uvědomit, rozdíly mezi jednoho klienta a víceklientské aplikace:  

- Aplikace jednoho klienta je určena pro použití v jedné organizaci. Je to obvykle aplikace obchodní (LoB), vytvořené pomocí vývojář enterprise. Jednoho klienta aplikace jsou přístupné pouze uživatelé s účty ve stejném klientovi jako registraci aplikace. V důsledku toho se pouze musí být zřízená v jednom adresáři.
- Víceklientské aplikace je určený pro použití v mnoha organizacích. Označuje se jako webové aplikace software jako služba (SaaS), je obvykle zapsán pomocí nezávislý dodavatel softwaru (ISV). Víceklientské aplikace musí být zřízená v každém klientovi, kde uživatelé potřebovat přístup. U klientů, než kde je aplikace registrovaná je požadován pro registraci je uživatel nebo správce souhlas. Upozorňujeme, že nativní klientské aplikace jsou víceklientské ve výchozím nastavení, jako jsou nainstalované na zařízení vlastníka prostředku. Viz předchozí [přehled rozhraní souhlasu](#overview-of-the-consent-framework) část Podrobnosti o rozhraní souhlasu.

Provedení víceklientské aplikace vyžaduje obě změny registrace aplikace, a také změní na vlastní webové aplikace. Následující části se věnují i.

#### <a name="changing-the-application-registration-to-support-multi-tenant"></a>Změna registrace aplikace pro podporu více klientů

Pokud píšete aplikaci, kterou chcete zpřístupnit pro vaše zákazníky nebo partnery mimo organizaci, budete muset aktualizovat definice aplikace na portálu Azure.

> [!IMPORTANT]
> Azure AD vyžaduje identifikátor ID URI aplikace víceklientské aplikací být globálně jedinečný. Identifikátor ID URI aplikace je jedním ze způsobů, které aplikace je definována ve zprávách protokolu. Aplikace pomocí jednoho klienta je dostačující pro identifikátor ID URI aplikace být jedinečný v rámci tohoto klienta. Pro více klientů aplikace musí být globálně jedinečné, Azure AD můžete najít aplikaci přes všechny klienty. Globální jedinečnosti se vynucuje tím, že identifikátor ID URI aplikace tak, aby měl název hostitele, který odpovídá ověřené domény klienta Azure AD. Například pokud je název vašeho klienta contoso.onmicrosoft.com pak platný identifikátor ID URI aplikace by být https://contoso.onmicrosoft.com/myapp. Pokud má váš klient ověřené domény contoso.com, pak by také být platný identifikátor ID URI aplikace https://contoso.com/myapp. Pokud je identifikátor ID URI aplikace není postupujte podle tohoto vzoru, nastavení aplikace, protože víceklientské selže.
> 

Chcete-li uživatelům externí umožňuje přístup k aplikaci: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet poskytuje přístup k více než jeden, klikněte na váš účet v pravém horním rohu a nastavte portálu relace do požadovaného Azure AD klienta.
3. V levém navigačním podokně klikněte **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikace**, pak najít nebo klikněte na aplikaci, kterou chcete konfigurovat. Budete přesměrováni na stránku registrace hlavní aplikace, což otevře **nastavení** stránku pro aplikaci.
4. Z **nastavení** klikněte na tlačítko **vlastnosti** a změňte **nevyužívá dělené tabulky více** přepnout **Ano**.

Po provedení změny uživatelů a správců v jiných organizacích, mohou udělit svým uživatelům možnost se přihlásit k vaší aplikaci, umožňuje aplikaci přístup k prostředkům zabezpečené jejich klienta.

#### <a name="changing-the-application-to-support-multi-tenant"></a>Změna aplikace pro podporu více klientů

Podporu pro více klientů aplikace založena na rozhraní Azure AD souhlasu. Souhlas je mechanismus, který umožňuje uživateli z jiného klienta k udělení přístupu aplikace k prostředkům zabezpečené uživatele klienta. Toto prostředí se označuje jako "souhlas uživatele."

Webové aplikace může také nabízí:

- Možnost správcům "zaregistrujte naší společnosti." Toto prostředí, označuje jako "souhlas správce", poskytuje správce schopnosti pro udělení souhlasu jménem *všichni uživatelé* ve své organizaci. Jenom uživatel, který ověřuje pomocí účtu, který patří k roli globálního správce může poskytnout souhlas správce; ostatní uživatelé dostanou k chybě.

- Registrace prostředí pro uživatele. Očekává se, že uživatel je poskytován "registrace" tlačítko, které se přesměruje prohlížeč Azure AD OAuth2.0 `/authorize` koncového bodu nebo OpenID Connect `/userinfo` koncový bod. Tyto koncové body umožňuje aplikaci získat informace o novém uživateli zkontrolováním požadavku id_token. Následující registrace fáze, zobrazí se uživateli s výzvou souhlasu, podobně jako uvedeno v [přehled rozhraní souhlasu](#overview-of-the-consent-framework) části.

Další informace o změny aplikace, které jsou potřebné k podpoře nevyužívá dělené tabulky více přístupu a přihlašovací v nebo registrace-množství prostředí najdete v tématu:

- [Postup přihlášení libovolného uživatele služby Azure Active Directory (AD) pomocí vzoru aplikace s více tenanty](active-directory-devhowto-multi-tenant-overview.md)
- Seznam [ukázky kódu víceklientské](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant). 
- [Rychlé spuštění: Přidání firemního brandingu na přihlašovací stránku ve službě Azure AD](../customize-branding.md)

### <a name="enabling-oauth-20-implicit-grant-for-single-page-applications"></a>Povolení OAuth 2.0 implicitní udělit pro jednostránkové aplikace

Jednu stránku aplikace (SPA) je obvykle strukturovaná s JavaScript náročné front-end, který běží v prohlížeči, která volá webové aplikace rozhraní API back-end provést jeho obchodní logiku. Pro SPA hostované ve službě Azure AD použijete k ověření uživatele se službou Azure AD a získat token, který můžete použít k zabezpečení volání z aplikace JavaScript klienta jeho back endové webové rozhraní API implicitní udělení OAuth 2.0. 

Po udělení souhlasu uživatele Tento stejný protokol ověřování lze získat tokeny zabezpečení volání mezi klientem a dalších webových rozhraní API prostředků nakonfigurovaný pro aplikaci. Další informace o udělení implicitní autorizace a vám pomohou rozhodnout, zda je správný pro váš scénář aplikací najdete v tématu [pochopení OAuth2 implicitní tok v Azure Active Directory poskytování](active-directory-dev-understanding-oauth2-implicit-grant.md).

Ve výchozím nastavení je pro aplikace zakázaná implicitní Grant OAuth 2.0. Můžete povolit implicitní udělení OAuth 2.0 pro aplikaci nastavení `oauth2AllowImplicitFlow` hodnotu v jeho [manifest aplikace](active-directory-application-manifest.md).

#### <a name="to-enable-oauth-20-implicit-grant"></a>Chcete-li povolit implicitní grant OAuth 2.0

> [!NOTE]
> Pro informace o tom, jak upravit manifest aplikace, je nutné nejprve zkontrolovat přehled v předchozí části, [konfigurace prostředků aplikace ke zveřejnění webovým rozhraním API](#configuring-a-resource-application-to-expose-web-apis).
>

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet poskytuje přístup k více než jeden, klikněte na váš účet v pravém horním rohu a nastavte portálu relace do požadovaného Azure AD klienta.
3. V levém navigačním podokně klikněte **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikace**, pak najít nebo klikněte na aplikaci, kterou chcete konfigurovat. Budete přesměrováni na stránku registrace hlavní aplikace, což otevře **nastavení** stránku pro aplikaci.
4. Přepnout **úpravy manifest** stránku kliknutím **Manifest** ze stránky registrace aplikace. Otevře webové manifestu editor, umožní **upravit** manifestu v rámci portálu. Vyhledejte a nastavte hodnotu "oauth2AllowImplicitFlow" na hodnotu "true". Ve výchozím nastavení je nastavena na hodnotu "false".
   
  ```json
  "oauth2AllowImplicitFlow": true,
  ```
5. Uložte aktualizovaný manifestu. Po uložení webového rozhraní API je nyní nakonfigurováno pro udělení implicitní OAuth 2.0 používat k ověřování uživatelů.

## <a name="removing-an-application"></a>Odebrání aplikace
Tato část popisuje postup odebrání registrace aplikace z vašeho klienta Azure AD.

### <a name="removing-an-application-authored-by-your-organization"></a>Odebrání aplikace vytvořené ve vaší organizaci
Aplikace, které vaše organizace zaregistrovala vyskytovat v části "Aplikací" filtr na stránce hlavní "registrace aplikace" vašeho klienta. Tyto aplikace jsou ty, které jste ručně zaregistrovat prostřednictvím portálu Azure nebo programově pomocí prostředí PowerShell nebo rozhraní Graph API. Přesněji řečeno jsou reprezentované pomocí k aplikaci a instanční objekt objektu v klientovi. Další informace najdete v tématu [objekty aplikací a hlavní objekty služeb](active-directory-application-objects.md).

#### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Odebrání aplikace klienta jedním z adresáře
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet poskytuje přístup k více než jeden, klikněte na váš účet v pravém horním rohu a nastavte portálu relace do požadovaného Azure AD klienta.
3. V levém navigačním podokně klikněte **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikace**, pak najít nebo klikněte na aplikaci, kterou chcete konfigurovat. Budete přesměrováni na stránku registrace hlavní aplikace, což otevře **nastavení** stránku pro aplikaci.
4. Na stránce registrace hlavní aplikace, klikněte na tlačítko **odstranit**.
5. Klikněte na tlačítko **Ano** v potvrzovací zprávě.

#### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Odebrání víceklientské aplikace z jeho domovského adresáře
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud váš účet poskytuje přístup k více než jeden, klikněte na váš účet v pravém horním rohu a nastavte portálu relace do požadovaného Azure AD klienta.
3. V levém navigačním podokně klikněte **Azure Active Directory** služby, klikněte na tlačítko **registrace aplikace**, pak najít nebo klikněte na aplikaci, kterou chcete konfigurovat. Budete přesměrováni na stránku registrace hlavní aplikace, což otevře **nastavení** stránku pro aplikaci.
4. Z **nastavení** vyberte **vlastnosti** a změňte **nevyužívá dělené tabulky více** přepnout **ne**, nejprve změňte aplikace jednoho klienta, pak klikněte na tlačítko **Uložit**. Aplikace služby hlavní objekty zůstanou na klienty všechny organizace, které již souhlasili k němu.
5. Klikněte na **odstranit** tlačítko ze stránky hlavní registrace aplikace.
6. Klikněte na tlačítko **Ano** v potvrzovací zprávě.

### <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Odebráním víceklientské aplikace, autorizovat jiné organizaci
Část aplikace, které se zobrazí v části "Všechny aplikace" filtru (s výjimkou registrace "Aplikací") na hlavní stránce "Registrace aplikace" vašeho klienta jsou víceklientským aplikacím. V technické podmínky tyto víceklientské aplikace z jiného klienta a registraci do vašeho klienta během procesu souhlasu. Přesněji řečeno jsou zobrazeny v pouze službu objektu zabezpečení ve vašem klientovi bez odpovídající objektu aplikace. Další informace o rozdílech mezi aplikací a hlavní objekty služby najdete v tématu [aplikace a služby hlavní objekty ve službě Azure AD](active-directory-application-objects.md).

Chcete-li odebrat přístup k aplikaci víceklientské do vašeho adresáře (po s udělen souhlas), musíte odebrat správce společnosti jeho instanční objekt. Správce musí mít přístup globálního správce a můžete ho odebrat prostřednictvím portálu Azure nebo použít [rutin prostředí Azure AD PowerShell](http://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>Další postup
- Další informace o tom, jak funguje ověřování ve službě Azure AD najdete v tématu [scénáře ověřování pro Azure AD](active-directory-authentication-scenarios.md).
- Najdete v článku [Branding pokyny pro integrované aplikace](active-directory-branding-guidelines.md) tipy na visual pokyny pro vaši aplikaci.
- Další informace o vztah mezi objekty aplikace a služby hlavní aplikace v tématu [objekty aplikací a hlavní objekty služeb](active-directory-application-objects.md).
- Další informace o roli plní manifestu aplikace, najdete v části [pochopení manifest aplikace Azure Active Directory](active-directory-application-manifest.md)
- Najdete v článku [Glosář vývojáře Azure AD](active-directory-dev-glossary.md) definice některých koncepty pro vývojáře základní Azure AD.
- Přejděte [Příručka pro vývojáře Active Directory](active-directory-developers-guide.md) přehled veškerý obsah pro vývojáře.

