---
title: Volání webového rozhraní API ASP.NET chráněného platformou identit microsoftu
description: V tomto rychlém startu se dozvíte, jak volat ASP.NET webové rozhraní API chráněné platformou identit microsoftu z aplikace WPF (Windows Desktop). WPF klient ověřuje uživatele, požaduje přístupový token a volá webové rozhraní API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 582afef8929da2ba75aab70c1ed0fa9e57fd3f19
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76703469"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>Úvodní příručka: Volání webového rozhraní API ASP.NET chráněného platformou identit microsoftu

V tomto rychlém startu vystavit webové rozhraní API a chránit tak, aby pouze ověřený uživatel přístup k němu. Tato ukázka ukazuje, jak vystavit webové rozhraní API pro ASP.NET, aby mohlo přijímat tokeny vydané osobními účty (včetně outlook.com, live.com a dalších) a také pracovní a školní účty od jakékoli společnosti nebo organizace, která je integrována s identitou Microsoftu Platforma.

Ukázka také obsahuje klienta aplikace WPF (Windows Desktop), který ukazuje, jak můžete požádat o přístupový token pro přístup k webovému rozhraní API.

## <a name="prerequisites"></a>Požadavky

Chcete-li spustit tuto ukázku, budete potřebovat následující:

* Visual Studio 2017 nebo 2019.  Stáhněte si [Visual Studio zdarma](https://www.visualstudio.com/downloads/).

* Účet [Microsoft](https://www.outlook.com) nebo [vývojářská aplikace Office 365](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>Stáhnout nebo klonovat tuto ukázku

Tuto ukázku můžete naklonovat z prostředí nebo příkazového řádku:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

Nebo si můžete [stáhnout ukázku jako soubor ZIP](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api-in-the-application-registration-portal"></a>Registrace webového rozhraní API na portálu pro registraci aplikací

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Zvolte klienta Azure AD, kde chcete vytvářet aplikace

Pokud chcete aplikace zaregistrovat ručně, jako první krok budete muset:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud je váš účet k dispozici ve více než jednom tenantovi Azure AD, vyberte svůj profil v pravém horním rohu v nabídce v horní části stránky a **pak přepněte adresář**.
   Změňte relaci portálu na požadovaný klient Azure AD.

### <a name="register-the-service-app-todolistservice"></a>Registrace aplikace služby (TodoListService)

1. Přejděte na platformu identit Microsoftpro vývojáře [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
1. Vyberte **možnost Nová registrace**.
1. Po zobrazení **stránky Registrovat přihlášku** zadejte registrační údaje vaší přihlášky:
   - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `AppModelv2-NativeClient-DotNet-TodoListService`.
   - Změnit **podporované typy účtů** na účty v **libovolném organizačním adresáři**.
   - Výběrem možnosti **Registrovat** aplikaci vytvořte.

1. Na stránce **Přehled** aplikace najděte hodnotu **ID aplikace (klienta)** a zaznamenejte ji na později. Budete ji potřebovat ke konfiguraci konfiguračního`ClientId` `TodoListService\Web.config`souboru sady Visual Studio pro tento projekt (v).
1. Vyberte část **Vystavit rozhraní API** a:
   - Vybrat **přidat obor**
   - Přijmout navrhovaný identifikátor URI ID aplikace (api://{clientId}) výběrem **možnosti Uložit a pokračovat**
   - Zadejte následující parametry:
     - pro použití **názvu oboru**`access_as_user`
     - Ujistěte se, že je vybrána možnost **Správci a uživatelé** pro **kdo může souhlasit**
     - v typu **zobrazovaného názvu souhlasu správce**`Access TodoListService as a user`
     - v typu **popisu souhlasu správce**`Accesses the TodoListService Web API as a user`
     - v typu **zobrazovaného názvu uživatelského souhlasu**`Access TodoListService as a user`
     - v typu **popisu souhlasu uživatele**`Accesses the TodoListService Web API as a user`
     - Zachovat **stav** jako **povolený**
     - Vybrat **přidat obor**

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>Konfigurace projektu služby tak, aby odpovídal registrovanému webovému rozhraní API 

1. Otevřete řešení v sadě Visual Studio a potom otevřete soubor **Web.config** pod kořenem projektu **TodoListService.**
1. Nahraďte `ida:ClientId` hodnotu parametru **ID klienta (ID aplikace)** z aplikace, kterou jste právě zaregistrovali na portálu registrace aplikací.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Přidání nového oboru do souboru app.config *klienta TodoListClient*

1. Otevřete soubor **app.config** umístěný v kořenové složce projektu **TodoListClient** a potom vložte **ID aplikace** z aplikace, `{Enter the Application ID of your TodoListService from the app registration portal}`kterou jste právě zaregistrovali pro *službu TodoListService,* pod `TodoListServiceScope` parametrem a nahraďte řetězec .

   > Poznámka: Ujistěte se, že používá následující formát:
   >
   > `api://{TodoListService-Application-ID}/access_as_user` 
   >
   >(kde {TodoListService-Application-ID} je identifikátor GUID představující ID aplikace pro vaši službu TodoListService).

## <a name="register-the-client-app-todolistclient"></a>Registrace klientské aplikace (TodoListClient)

V tomto kroku nakonfigurujete projekt *TodoListClient* registrací nové aplikace na portálu pro registraci aplikací. V případech, kdy jsou klient a server považovány za *stejnou aplikaci,* můžete také znovu použít stejnou aplikaci zaregistrovánou v kroku 2. Použití stejné aplikace je potřeba, pokud chcete, aby se uživatelé přihlašují pomocí osobních účtů Microsoft

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>Registrace aplikace *TodoListClient* na *portálu pro registraci aplikací*

1. Přejděte na platformu identit Microsoftpro vývojáře [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
1. Vyberte **možnost Nová registrace**.
1. Po zobrazení **stránky Registrovat přihlášku** zadejte registrační údaje vaší přihlášky:
   - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `NativeClient-DotNet-TodoListClient`.
   - Změnit **podporované typy účtů** na účty v **libovolném organizačním adresáři**.
   - Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Na stránce Přehled aplikace vyberte část **Ověřování**.
   - V části **Přesměrovat identifikátory** | URI**suggested redirect uri pro veřejné klienty (mobilní, stolní)****https://login.microsoftonline.com/common/oauth2/nativeclient**
   - Vyberte **Uložit**.
1. Výběr oddílu **oprávnění rozhraní API**
   - Klikněte na tlačítko **Přidat oprávnění** a potom
   - Vyberte kartu **Moje api.**
   - V seznamu rozhraní API vyberte `AppModelv2-NativeClient-DotNet-TodoListService API`nebo název, který jste zadali pro webové rozhraní API.
   - Zkontrolujte **access_as_user** oprávnění, pokud ještě není zaškrtnuto. V případě potřeby použijte vyhledávací pole.
   - Vyberte tlačítko **Přidat oprávnění.**

### <a name="configure-your-todolistclient-project"></a>Konfigurace projektu *TodoListClient*

1. Na *portálu registrace aplikace*zkopírujte na stránce **Přehled** hodnotu **ID Aplikace (klienta)**
1. Otevřete soubor **app.config** umístěný v kořenové složce projektu **TodoListClient** a vložte hodnotu do hodnoty parametru. `ida:ClientId`

## <a name="run-your-project"></a>Spusťte projekt

1. Stisknutím `<F5>` spusťte projekt. Měl by se otevřít *váš todolista.*
1. Vyberte Přihlásit se **vpravo** nahoře a přihlaste se stejným uživatelem, který jste použili k registraci aplikace, nebo uživatelem ve stejném adresáři.
1. V tomto okamžiku, pokud se přihlašujete poprvé, můžete být vyzváni k souhlasu s Webovým rozhraním *TodoListService.*
1. Přihlášení také požádat o přístupový token *do access_as_user* oboru pro přístup *k todolistservice* webové api a manipulovat *seznam úkolů.*

## <a name="pre-authorize-your-client-application"></a>Předběžná autorizace klientské aplikace

Jedním ze způsobů, jak umožnit uživatelům z jiných adresářů přístup k webovému rozhraní API, je *předběžné autorizace* klientských aplikací pro přístup k webovému rozhraní API přidáním ID aplikací z klientských aplikací do seznamu *předautorizovaných* aplikací pro webové rozhraní API. Přidáním předem autorizovaného klienta nebudete vyžadovat, aby uživatel souhlasil s používáním webového rozhraní API. Chcete-li webovou aplikaci předem autorizovat, postupujte podle následujících kroků:

1. Vraťte se na *portál registrace aplikace* a otevřete vlastnosti **služby TodoListService**.
1. V části **Vystavit rozhraní API** klikněte na Přidat **klientskou aplikaci** v části *Autorizované klientské aplikace.*
1. Do pole *ID klienta* vložte ID `TodoListClient` aplikace.
1. V části *Autorizované obory* vyberte obor `api://<Application ID>/access_as_user`pro toto webové rozhraní API .
1. Stiskněte tlačítko **Přidat aplikaci** v dolní části stránky.

## <a name="run-your-project"></a>Spusťte projekt

1. Stisknutím `<F5>` spusťte projekt. Měl by se otevřít *váš todolista.*
1. Vpravém horním rohu (nebo vymazat mezipaměť/přihlášení) vyberte **Přihlásit** se a pak se přihlaste pomocí osobního účtu Microsoft (live.com nebo hotmail.com) nebo pracovního nebo školního účtu.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Volitelné: Omezení přístupu k přihlášení k aplikaci

Ve výchozím nastavení při stahování této ukázky kódu a konfiguraci aplikace tak, aby používala koncový bod Azure Active Directory v2 podle předchozích kroků, osobní účty – jako outlook.com, live.com a další – i pracovní nebo školní účty z libovolného organizace, které jsou integrované s Azure AD můžete požádat o tokeny a přístup k webovérozhraní API. 

Chcete-li omezit, kdo se může přihlásit k vaší aplikaci, použijte jednu z možností:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Možnost 1: Omezení přístupu k jedné organizaci (jednokliente)

Přístup k přihlášení pro vaši aplikaci můžete omezit pouze na uživatelské účty, které jsou v jednom tenantovi Azure AD – včetně *účtů guest* tohoto klienta. Tento scénář je společný pro *obchodní aplikace*:

1. Otevřete soubor **App_Start\Startup.Auth** a změňte hodnotu koncového bodu metadat, který je předán do `OpenIdConnectSecurityTokenProvider` `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` do `contoso.onmicrosoft.com`(můžete také použít název klienta, například ).
2. Ve stejném souboru `ValidIssuer` nastavte vlastnost `TokenValidationParameters` `"https://sts.windows.net/{Tenant ID}/"` na `ValidateIssuer` to `true`a argument na .

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Možnost 2: Ověření vystavitelů pomocí vlastní metody

Můžete implementovat vlastní metodu k ověření vystavitetele pomocí **Parametr IssuerValidator.** Další informace o použití tohoto parametru naleznete o [třídě TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky
Další informace o scénáři chráněného webového rozhraní API, který platforma identit microsoftu podporuje:
> [!div class="nextstepaction"]
> [Scénář chráněného webového rozhraní API](scenario-protected-web-api-overview.md)
