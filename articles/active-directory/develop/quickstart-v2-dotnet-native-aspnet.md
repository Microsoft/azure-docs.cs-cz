---
title: Volání webového rozhraní API ASP.NET chráněného technologií Microsoft Identity Platform
description: V tomto rychlém startu se dozvíte, jak volat webové rozhraní API ASP.NET chráněné platformou Microsoft identity z aplikace klasické pracovní plochy (WPF). Klient WPF ověří uživatele, požádá o přístupový token a zavolá webové rozhraní API.
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
ms.openlocfilehash: 62cebb4e774e2f86ed6a4a17edd6da71f7c7cd9f
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141325"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>Rychlý Start: volání webového rozhraní API ASP.NET chráněného technologií Microsoft Identity Platform

V tomto rychlém startu vystavíte webové rozhraní API a ochráníte ho, aby k němu měli přístup jenom ověřený uživatel. V této ukázce se dozvíte, jak vystavit webové rozhraní API ASP.NET, aby mohl přijímat tokeny vydané osobními účty (včetně outlook.com, live.com a dalších), jakož i pracovní a školní účty z libovolné společnosti nebo organizace, která je integrovaná s platformou Microsoft identity.

Ukázka zahrnuje také klienta aplikace pro stolní počítače (WPF) systému Windows, který ukazuje, jak můžete požádat o přístupový token pro přístup k webovému rozhraní API.

## <a name="prerequisites"></a>Předpoklady

Chcete-li spustit tuto ukázku, budete potřebovat následující:

* Visual Studio 2017 nebo 2019.  Stáhněte si [Visual Studio zdarma](https://www.visualstudio.com/downloads/).

* Buď [účet Microsoft](https://www.outlook.com) , nebo [Microsoft 365 vývojářský program](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>Stáhnout nebo naklonovat tuto ukázku

Tuto ukázku můžete klonovat z prostředí nebo příkazového řádku:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

Nebo můžete [ukázku stáhnout jako soubor zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api-in-the-application-registration-portal"></a>Registrace webového rozhraní API na portálu pro registraci aplikací

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Vyberte tenanta Azure AD, ve kterém chcete vytvářet aplikace.

Pokud chcete své aplikace registrovat ručně, je třeba nejprve tyto kroky:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud je váš účet přítomen ve více než jednom tenantovi služby Azure AD, vyberte profil v pravém horním rohu v nabídce v horní části stránky a pak **Přepněte do adresáře**.
   Změňte relaci portálu na požadovaného tenanta Azure AD.

### <a name="register-the-service-app-todolistservice"></a>Registrace aplikace služby (TodoListService)

1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
1. Vyberte **Nová registrace**.
1. Jakmile se zobrazí **Stránka Registrovat aplikaci** , zadejte informace o registraci vaší aplikace:
   - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `AppModelv2-NativeClient-DotNet-TodoListService`.
   - Změňte **podporované typy účtů** na **účty v libovolném organizačním adresáři**.
   - Výběrem možnosti **Registrovat** aplikaci vytvořte.

1. Na stránce **Přehled** aplikace vyhledejte hodnotu **ID aplikace (klienta)** a zaznamenejte ji pro pozdější použití. Budete ho potřebovat ke konfiguraci konfiguračního souboru sady Visual Studio pro tento projekt ( `ClientId` v `TodoListService\Web.config` ).
1. Vyberte část **vystavení rozhraní API** a:
   - Vyberte **Přidat obor** .
   - přijměte navrhovaný identifikátor URI ID aplikace (API://{clientId}) tak, že vyberete **Uložit a pokračovat** .
   - Zadejte následující parametry:
     - pro použití **oboru názvů**`access_as_user`
     - Ujistěte se, že je vybraná možnost **Správci a uživatelé** pro uživatele **, kteří můžou souhlasit** .
     - typ **zobrazovaného názvu pro zobrazení souhlasu správce**`Access TodoListService as a user`
     - typ **popisu souhlasu správce**`Accesses the TodoListService web API as a user`
     - typ **zobrazovaného názvu pro vyjádření souhlasu uživatele**`Access TodoListService as a user`
     - typ **popisu souhlasu uživatele**`Accesses the TodoListService web API as a user`
     - Zachovat **stav** jako **povolený**
     - Vybrat **Přidat obor**

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>Konfigurace projektu služby tak, aby odpovídal zaregistrovanému webovému rozhraní API

1. Otevřete řešení v aplikaci Visual Studio a pak otevřete soubor **Web.config** pod kořenem projektu **TodoListService** .
1. Nahraďte hodnotu `ida:ClientId` parametru **ID klienta (ID aplikace)** z aplikace, kterou jste právě zaregistrovali na portálu pro registraci aplikací.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Přidání nového oboru do app.config *TodoListClient*

* Otevřete **app.config** soubor umístěný v kořenové složce projektu **TodoListClient** a potom vložte **ID aplikace** z aplikace, kterou jste právě zaregistrovali pro svůj *TodoListService* v `TodoListServiceScope` parametru, a nahraďte řetězec `{Enter the Application ID of your TodoListService from the app registration portal}` .

  > [!NOTE]
  > Ujistěte se, že používá následující formát:
  >
  > `api://{TodoListService-Application-ID}/access_as_user`
  >
  >(kde {TodoListService-Application-ID} je identifikátor GUID, který představuje ID aplikace pro váš TodoListService).

## <a name="register-the-client-app-todolistclient"></a>Registrace klientské aplikace (TodoListClient)

V tomto kroku nakonfigurujete projekt *TodoListClient* tak, že zaregistrujete novou aplikaci na portálu pro registraci aplikací. V případech, kdy se klient a server považují za *stejnou aplikaci* , můžete také jednoduše znovu použít stejnou aplikaci registrovanou v kroku 2. Použití stejné aplikace je nutné, pokud chcete, aby se uživatelé přihlásili pomocí osobních účtů Microsoft.

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>Registrace aplikace *TodoListClient* na portálu pro *registraci aplikací*

1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
1. Vyberte **Nová registrace**.
1. Jakmile se zobrazí **Stránka Registrovat aplikaci** , zadejte informace o registraci vaší aplikace:
   - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `NativeClient-DotNet-TodoListClient`.
   - Změňte **podporované typy účtů** na **účty v libovolném organizačním adresáři**.
   - Výběrem možnosti **Registrovat** aplikaci vytvořte.
   
   > [!NOTE]
   > V **app.config**projektu *TodoListClient* je výchozí hodnota `ida:Tenant` nastavená na `common` .
   >
   > `common`znamená, že se můžete přihlásit pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft (protože jste vybrali **účty v jakékoli organizační složce**).
   >
   > `organizations`znamená, že se můžete přihlásit pomocí pracovního nebo školního účtu.
   >
   > `consumers`znamená, že se můžete přihlásit pouze pomocí osobního účtu společnosti Microsoft.
   >
   
1. Na stránce Přehled aplikace vyberte část **ověřování** .
   1. V části **konfigurace platformy**vyberte tlačítko **Přidat platformu** .
   1. Pro **mobilní a desktopové aplikace**vyberte **mobilní a desktopové aplikace**.
   1. V případě **identifikátorů URI přesměrování**zaškrtněte **https://login.microsoftonline.com/common/oauth2/nativeclient** políčko.
   1. Vyberte **Konfigurovat**.   
1. Vyberte oddíl **oprávnění rozhraní API** .
   1. Vyberte tlačítko **Přidat oprávnění**.
   1. Vyberte kartu **Moje rozhraní API** .
   1. V seznamu rozhraní API vyberte `AppModelv2-NativeClient-DotNet-TodoListService API` nebo zadejte název, který jste zadali pro webové rozhraní API.
   1. Zaškrtněte **access_as_user** oprávnění, pokud ještě není zaškrtnuté. V případě potřeby použijte vyhledávací pole.
   1. Klikněte na tlačítko **Přidat oprávnění** .

### <a name="configure-your-todolistclient-project"></a>Konfigurace projektu *TodoListClient*

1. Na *portálu pro registraci aplikací*zkopírujte na stránce **Přehled** hodnotu **ID aplikace (klienta)** .
1. Otevřete **app.config** soubor umístěný v kořenové složce projektu **TodoListClient** a vložte hodnotu do `ida:ClientId` hodnoty parametru.

## <a name="run-your-project"></a>Spusťte projekt

1. Stisknutím klávesy `<F5>` spusťte projekt. Váš *TodoListClient* by měl být otevřený.
1. Vyberte možnost **Přihlásit** se v pravém horním rohu a přihlaste se pomocí stejného uživatele, kterého jste použili k registraci aplikace, nebo uživatele ve stejném adresáři.
1. Pokud se v tuto chvíli přihlašujete poprvé, může se zobrazit výzva k zadání souhlasu s webovým rozhraním API *TodoListService* .
1. Pro přístup k webovému rozhraní API *TodoListService* a manipulaci se seznamem *úkolů* si také požádáte o přístupový token do oboru *access_as_user* .

## <a name="pre-authorize-your-client-application"></a>Předběžná autorizace klientské aplikace

Jedním z způsobů, jak uživatelům z jiných adresářů povolit přístup k webovému rozhraní API, je *předautorizací* klientských aplikací pro přístup k webovému rozhraní API přidáním ID aplikací z klientských aplikací v seznamu *předem autorizovaných* aplikací pro vaše webové rozhraní API. Přidáním předem autorizovaného klienta nebudete muset uživateli souhlasit s používáním vašeho webového rozhraní API. K předběžné autorizaci webové aplikace použijte následující postup:

1. Vraťte se zpátky na *portál pro registraci aplikací* a otevřete vlastnosti vašeho **TodoListService**.
1. V části **vystavení rozhraní API** klikněte na **Přidat klientskou aplikaci** v části *autorizované klientské aplikace* .
1. Do pole *ID klienta* Vložte ID aplikace `TodoListClient` .
1. V části *autorizované obory* vyberte obor pro toto webové rozhraní API `api://<Application ID>/access_as_user` .
1. V dolní části stránky stiskněte tlačítko **Přidat aplikaci** .

## <a name="run-your-project"></a>Spusťte projekt

1. Stisknutím klávesy `<F5>` spusťte projekt. Váš *TodoListClient* by měl být otevřený.
1. Vyberte možnost **Přihlásit** se v pravém horním rohu (nebo zrušte zaškrtnutí políčka mezipaměť/přihlášení) a pak se přihlaste pomocí osobního účet Microsoft (live.com nebo Hotmail.com) nebo pracovního nebo školního účtu.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Volitelné: Omezte přístup k vaší aplikaci.

Ve výchozím nastavení platí, že když si stáhnete tuto ukázku kódu a nakonfigurujete aplikaci tak, aby používala koncový bod Azure Active Directory v2 po předchozích krocích, můžou žádosti o tokeny a přístup k vašemu webovému rozhraní API vyžadovat i pracovní nebo školní účty ze všech organizací integrovaných se službou Azure AD.

Chcete-li omezit, kdo se může přihlásit ke své aplikaci, použijte jednu z možností:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Možnost 1: omezení přístupu k jedné organizaci (jeden tenant)

Přístup pro přihlášení k aplikaci můžete omezit jenom na uživatelské účty, které jsou v jednom tenantovi Azure AD – včetně *účtů hostů* tohoto tenanta. Tento scénář je společný pro *obchodní aplikace*:

1. Otevřete soubor **app_start \Startup.auth** a změňte hodnotu koncového bodu metadat, který se předává do `OpenIdConnectSecurityTokenProvider` do `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` (můžete také použít název tenanta, například `contoso.onmicrosoft.com` ).
2. Ve stejném souboru nastavte `ValidIssuer` vlastnost na `TokenValidationParameters` `"https://sts.windows.net/{Tenant ID}/"` a a `ValidateIssuer` argument na `true` .

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Možnost 2: použití vlastní metody pro ověření vystavitelů

Můžete implementovat vlastní metodu pro ověření vystavitelů pomocí parametru **IssuerValidator** . Další informace o tom, jak tento parametr použít, naleznete v tématu [Třída TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o scénáři chráněného webového rozhraní API, který podporuje platforma Microsoft Identity Platform:
> [!div class="nextstepaction"]
> [Scénář chráněného webového rozhraní API](scenario-protected-web-api-overview.md)
