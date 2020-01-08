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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c6c51b0a7ae7255391fd35d234b5ee47b7a9525
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424038"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-microsoft-identity-platform"></a>Rychlý Start: volání webového rozhraní API ASP.NET chráněného technologií Microsoft Identity Platform

V tomto rychlém startu vystavíte webové rozhraní API a ochráníte ho, aby k němu měli přístup jenom ověřený uživatel. V této ukázce se dozvíte, jak vystavit webové rozhraní API ASP.NET, aby mohl přijímat tokeny vydané osobními účty (včetně outlook.com, live.com a dalších), jakož i pracovní a školní účty z jakékoli společnosti nebo organizace, která je integrovaná s identitou Microsoft. platformy.

Ukázka zahrnuje také klienta aplikace pro stolní počítače (WPF) systému Windows, který ukazuje, jak můžete požádat o přístupový token pro přístup k webovému rozhraní API.

## <a name="prerequisites"></a>Požadavky

Chcete-li spustit tuto ukázku, budete potřebovat následující:

* Visual Studio 2017 nebo 2019.  Stáhněte si [Visual Studio zdarma](https://www.visualstudio.com/downloads/).

* [Účet Microsoft](https://www.outlook.com) nebo [vývojářský Program sady Office 365](/office/developer-program/office-365-developer-program)

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
1. Když se zobrazí **stránka Registrace aplikace**, zadejte registrační informace vaší aplikace:
   - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `AppModelv2-NativeClient-DotNet-TodoListService`.
   - Změňte **podporované typy účtů** na **účty v libovolném organizačním adresáři**.
   - Výběrem možnosti **Registrovat** aplikaci vytvořte.

1. Na stránce **Přehled** aplikace vyhledejte hodnotu **ID aplikace (klienta)** a zaznamenejte ji pro pozdější použití. Budete ho potřebovat ke konfiguraci konfiguračního souboru sady Visual Studio pro tento projekt (`ClientId` v `TodoListService\Web.config`).
1. Vyberte část **vystavení rozhraní API** a:
   - Vyberte **Přidat obor** .
   - přijměte navrhovaný identifikátor URI ID aplikace (API://{clientId}) tak, že vyberete **Uložit a pokračovat** .
   - Zadejte následující parametry:
     - pro **název oboru** použijte `access_as_user`
     - Ujistěte se, že je vybraná možnost **Správci a uživatelé** pro uživatele **, kteří můžou souhlasit** .
     - do `Access TodoListService as a user` typ **zobrazovaného jména souhlasu správce**
     - v **popisu souhlasu správce** zadejte `Accesses the TodoListService Web API as a user`
     - do `Access TodoListService as a user` zadejte **název zobrazení souhlasu uživatele** .
     - typ **popisu souhlasu uživatele** `Accesses the TodoListService Web API as a user`
     - Zachovat **stav** jako **povolený**
     - Vybrat **Přidat obor**

### <a name="configure-the-service-project-to-match-the-registered-web-api"></a>Konfigurace projektu služby tak, aby odpovídal zaregistrovanému webovému rozhraní API 

1. Otevřete řešení v aplikaci Visual Studio a otevřete soubor **Web. config** v kořenovém adresáři projektu **TodoListService** .
1. Nahraďte hodnotu parametru `ida:ClientId` parametrem **ID klienta (ID aplikace)** z aplikace, kterou jste právě zaregistrovali v portálu pro registraci aplikací.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Přidání nového oboru do souboru App. config pro *TodoListClient*

1. Otevřete soubor **App. config** umístěný v kořenové složce projektu **TodoListClient** a potom vložte **ID aplikace** z aplikace, kterou jste právě zaregistrovali pro *TodoListService* , v části `TodoListServiceScope` parametr a nahraďte `{Enter the Application ID of your TodoListService from the app registration portal}`řetězce.

   > Poznámka: Ujistěte se, že používá následující formát:
   >
   > `api://{TodoListService-Application-ID}/access_as_user` 
   >
   >(kde {TodoListService-Application-ID} je identifikátor GUID, který představuje ID aplikace pro váš TodoListService).

## <a name="register-the-client-app-todolistclient"></a>Registrace klientské aplikace (TodoListClient)

V tomto kroku nakonfigurujete projekt *TodoListClient* tak, že zaregistrujete novou aplikaci na portálu pro registraci aplikací. V případech, kdy se klient a server považují za *stejnou aplikaci* , můžete také jednoduše znovu použít stejnou aplikaci registrovanou v kroku 2. Použití stejné aplikace je nutné, pokud chcete, aby se uživatelé přihlásili pomocí osobních účtů Microsoft.

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>Registrace aplikace *TodoListClient* na portálu pro *registraci aplikací*

1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
1. Vyberte **Nová registrace**.
1. Když se zobrazí **stránka Registrace aplikace**, zadejte registrační informace vaší aplikace:
   - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `NativeClient-DotNet-TodoListClient`.
   - Změňte **podporované typy účtů** na **účty v libovolném organizačním adresáři**.
   - Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. Na stránce Přehled aplikace vyberte část **ověřování** .
   - V části **identifikátory URI pro přesměrování** | **Doporučené identifikátory URI pro přesměrování pro veřejné klienty (mobilní zařízení, stolní počítače)** , ověřte **https://login.microsoftonline.com/common/oauth2/nativeclient**
   - Vyberte **Uložit**.
1. Vyberte oddíl **oprávnění rozhraní API** .
   - Klikněte na tlačítko **Přidat oprávnění** a pak na
   - Vyberte kartu **Moje rozhraní API** .
   - V seznamu rozhraní API vyberte `AppModelv2-NativeClient-DotNet-TodoListService API`nebo název, který jste zadali pro webové rozhraní API.
   - Zaškrtněte **access_as_user** oprávnění, pokud ještě není zaškrtnuté. V případě potřeby použijte vyhledávací pole.
   - Vyberte tlačítko **Přidat oprávnění** .

### <a name="configure-your-todolistclient-project"></a>Konfigurace projektu *TodoListClient*

1. Na *portálu pro registraci aplikací*zkopírujte na stránce **Přehled** hodnotu **ID aplikace (klienta)** .
1. Otevřete soubor **App. config** umístěný v kořenové složce projektu **TodoListClient** a vložte hodnotu do hodnoty parametru `ida:ClientId`

## <a name="run-your-project"></a>Spusťte projekt

1. Kliknutím na tlačítko `<F5>` spusťte projekt. Váš *TodoListClient* by měl být otevřený.
1. Vyberte možnost **Přihlásit** se v pravém horním rohu a přihlaste se pomocí stejného uživatele, kterého jste použili k registraci aplikace, nebo uživatele ve stejném adresáři.
1. Pokud se v tuto chvíli přihlašujete poprvé, může se zobrazit výzva k zadání souhlasu s webovým rozhraním API *TodoListService* .
1. Pro přístup k webovému rozhraní API *TodoListService* a manipulaci se seznamem *úkolů* si také požádáte o přístupový token do oboru *access_as_user* .

## <a name="pre-authorize-your-client-application"></a>Předběžná autorizace klientské aplikace

Jedním z způsobů, jak uživatelům z jiných adresářů povolit přístup k webovému rozhraní API, je *předautorizací* klientských aplikací pro přístup k webovému rozhraní API přidáním ID aplikací z klientských aplikací v seznamu *předem autorizovaných* aplikací pro vaše webové rozhraní API. Přidáním předem autorizovaného klienta nebudete muset uživateli souhlasit s používáním vašeho webového rozhraní API. K předběžné autorizaci webové aplikace použijte následující postup:

1. Vraťte se zpátky na *portál pro registraci aplikací* a otevřete vlastnosti vašeho **TodoListService**.
1. V části **vystavení rozhraní API** klikněte na **Přidat klientskou aplikaci** v části *autorizované klientské aplikace* .
1. Do pole *ID klienta* vložte id aplikace `TodoListClient`.
1. V části *autorizované obory* vyberte obor pro toto `api://<Application ID>/access_as_user`webového rozhraní API.
1. V dolní části stránky stiskněte tlačítko **Přidat aplikaci** .

## <a name="run-your-project"></a>Spusťte projekt

1. Kliknutím na tlačítko `<F5>` spusťte projekt. Váš *TodoListClient* by měl být otevřený.
1. Vyberte možnost **Přihlásit** se v pravém horním rohu (nebo zrušte zaškrtnutí políčka mezipaměť/přihlášení) a pak se přihlaste pomocí osobního účet Microsoft (live.com nebo Hotmail.com) nebo pracovního nebo školního účtu.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>Volitelné: Omezte přístup k vaší aplikaci.

Ve výchozím nastavení, když si stáhnete tuto ukázku kódu a nakonfigurujete aplikaci tak, aby používala koncový bod Azure Active Directory v2, podle předchozích kroků, jak osobní účty, jako jsou outlook.com, live.com a další, i pracovní nebo školní účty z libovolného organizace, které jsou integrované s Azure AD, můžou žádat o tokeny a přistupovat k webovému rozhraní API. 

Chcete-li omezit, kdo se může přihlásit ke své aplikaci, použijte jednu z možností:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Možnost 1: omezení přístupu k jedné organizaci (jeden tenant)

Přístup pro přihlášení k aplikaci můžete omezit jenom na uživatelské účty, které jsou v jednom tenantovi Azure AD – včetně *účtů hostů* tohoto tenanta. Tento scénář je společný pro *obchodní aplikace*:

1. Otevřete soubor **app_start \Startup.auth** a změňte hodnotu koncového bodu metadat, který se předává do `OpenIdConnectSecurityTokenProvider` `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` (můžete také použít název tenanta, jako je například `contoso.onmicrosoft.com`).
2. Ve stejném souboru nastavte vlastnost `ValidIssuer` v `TokenValidationParameters` na `"https://sts.windows.net/{Tenant ID}/"` a argument `ValidateIssuer` `true`.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Možnost 2: použití vlastní metody pro ověření vystavitelů

Můžete implementovat vlastní metodu pro ověření vystavitelů pomocí parametru **IssuerValidator** . Další informace o tom, jak tento parametr použít, naleznete v tématu [Třída TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o scénáři chráněného webového rozhraní API, který podporuje platforma Microsoft Identity Platform:
> [!div class="nextstepaction"]
> [Scénář chráněného webového rozhraní API](scenario-protected-web-api-overview.md)
