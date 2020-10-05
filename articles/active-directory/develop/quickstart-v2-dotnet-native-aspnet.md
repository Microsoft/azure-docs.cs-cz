---
title: 'Rychlý Start: volání webového rozhraní API ASP.NET, které je chráněné technologií Microsoft Identity Platform | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak volat webové rozhraní API ASP.NET, které je chráněno platformou Microsoft identity z aplikace klasické pracovní plochy (WPF).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: bf9c92d631d1d48527cd3a2734879d400d3e0bf0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91631609"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>Rychlý Start: volání webového rozhraní API ASP.NET, které je chráněné technologií Microsoft Identity Platform

V tomto rychlém startu vystavíte webové rozhraní API a ochráníte ho, aby k němu měli přístup jenom ověření uživatelé. Tento článek ukazuje, jak vystavit webové rozhraní API ASP.NET, aby mohl přijímat tokeny, které jsou vydávané osobními účty, jako je outlook.com nebo live.com, a pracovní nebo školní účty z libovolné společnosti nebo organizace, která je integrovaná s platformou Microsoft identity.

Článek také používá aplikaci Windows Presentation Foundation (WPF) k předvedení toho, jak můžete požádat o přístupový token pro přístup k webovému rozhraní API.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Visual Studio 2017 nebo 2019. Stáhněte si [Visual Studio zdarma](https://www.visualstudio.com/downloads/).

## <a name="clone-or-download-the-sample"></a>Klonovat nebo Stáhnout ukázku

Ukázku můžete získat jedním ze dvou způsobů:

* Naklonujte ho z prostředí nebo příkazového řádku:
   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```
* [Stáhněte si ho jako soubor zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api"></a>Registrace webového rozhraní API

V této části zaregistrujete webové rozhraní API v **Registrace aplikací** Azure Portal.

### <a name="choose-your-azure-ad-tenant"></a>Výběr tenanta Azure AD

Pokud chcete své aplikace zaregistrovat ručně, vyberte tenanta Azure Active Directory (Azure AD), ve kterém chcete své aplikace vytvořit.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. Pokud je váš účet přítomen ve více než jednom tenantovi služby Azure AD, vyberte profil v pravém horním rohu a pak vyberte **Přepnout adresář**.
1. Změňte relaci portálu na klienta služby Azure AD, kterého chcete použít.

### <a name="register-the-todolistservice-app"></a>Registrace aplikace TodoListService

1. Přejít na portál Microsoft Identity Platform for Developers [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Portal.
1. Vyberte **Nová registrace**.
1. Po otevření **stránky Registrovat aplikaci** zadejte informace o registraci vaší aplikace:

    1. V části **název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace. Zadejte například **AppModelv2-NativeClient-dotnet-TodoListService**.
    1. U **podporovaných typů účtů**vyberte **účty v libovolném organizačním adresáři**.
    1. Výběrem možnosti **Registrovat** aplikaci vytvořte.

1. Na stránce **Přehled** aplikace vyhledejte hodnotu **ID aplikace (klienta)** a potom ji zaznamenejte pro pozdější použití. Budete ho potřebovat ke konfiguraci konfiguračního souboru sady Visual Studio pro tento projekt (to znamená `ClientId` v souboru *TodoListService\Web.config* ).

1. V části **vystavení rozhraní API** vyberte **Přidat obor**, přijměte navrhovaný identifikátor URI () navrhované aplikace `api://{clientId}` , a to tak, že vyberete **Uložit a pokračovat**a pak zadáte následující informace:

    1. Jako **název oboru**zadejte **access_as_user**.
    1. Pro **uživatele, kteří můžou vyjádřit souhlas**, se ujistěte, že je vybraná možnost **Správci a uživatelé** .
    1. V poli **Zobrazovaný název souhlasu správce** zadejte **přístup TodoListService jako uživatel**.
    1. V poli **Popis souhlasu správce** zadejte přístup k **webovému rozhraní API TodoListService jako uživatel**.
    1. Do pole **Zobrazovaný název souhlasu uživatele** zadejte **přístup TodoListService jako uživatel**.
    1. Do pole **Popis souhlasu uživatele** zadejte přístup k **webovému rozhraní API TodoListService jako uživatel**.
    1. V případě **stavu**nechte **Povolit**.
    1. Vyberte **Přidat obor**.

### <a name="configure-the-service-project"></a>Konfigurace projektu služby

Nakonfigurujte projekt služby tak, aby odpovídal zaregistrovanému webovému rozhraní API, a to následujícím způsobem:

1. Otevřete řešení v aplikaci Visual Studio a pak otevřete soubor *Web.config* pod kořenem projektu TodoListService.

1. Nahraďte hodnotu `ida:ClientId` parametru hodnotou ID klienta (ID aplikace) z aplikace, kterou jste právě zaregistrovali na portálu **Registrace aplikací** .

### <a name="add-the-new-scope-to-the-appconfig-file"></a>Přidání nového oboru do souboru app.config

Chcete-li přidat nový obor do souboru *app.config* TodoListClient, postupujte takto:

1. V kořenové složce projektu TodoListClient otevřete soubor *app.config* .

1. Vložte ID aplikace z aplikace, kterou jste právě zaregistrovali pro projekt TodoListService v `TodoListServiceScope` parametru, a nahraďte `{Enter the Application ID of your TodoListService from the app registration portal}` řetězec.

  > [!NOTE]
  > Ujistěte se, že ID aplikace používá následující formát: `api://{TodoListService-Application-ID}/access_as_user` (kde `{TodoListService-Application-ID}` je identifikátor GUID, který představuje ID aplikace pro vaši aplikaci TodoListService).

## <a name="register-the-todolistclient-client-app"></a>Registrace klientské aplikace TodoListClient

V této části zaregistrujete aplikaci TodoListClient v **Registrace aplikací** Azure Portal a potom nakonfigurujete kód v projektu TodoListClient. Pokud se klient a server považují za *stejnou aplikaci*, můžete aplikaci, která je zaregistrovaná v kroku 2, použít znovu. Stejnou aplikaci použijte v případě, že chcete, aby se uživatelé přihlásili pomocí osobního účtu Microsoft.

### <a name="register-the-app"></a>Registrace aplikace

K registraci aplikace TodoListClient postupujte takto:

1. Přejít na portál Microsoft Identity Platform for Developers [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Portal.
1. Vyberte **Nová registrace**.
1. Po otevření **stránky Registrovat aplikaci** zadejte informace o registraci vaší aplikace:

    1. V části **název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace (například **NativeClient-dotnet-TodoListClient**).
    1. U **podporovaných typů účtů**vyberte **účty v libovolném organizačním adresáři**.
    1. Výběrem možnosti **Registrovat** aplikaci vytvořte.

   > [!NOTE]
   > V souboru *app.config* projektu TodoListClient je výchozí hodnota `ida:Tenant` nastavená na `common` . Možné hodnoty jsou:
   > - `common`: Přihlaste se pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft (protože jste vybrali **účty v libovolné organizační složce** v kroku 3b).
   > - `organizations`: Můžete se přihlásit pomocí pracovního nebo školního účtu.
   > - `consumers`: Můžete se přihlásit pouze pomocí osobního účtu společnosti Microsoft.

1. Na stránce **Přehled** aplikace vyberte **ověřování**a pak postupujte takto:

    1. V části **konfigurace platformy**vyberte tlačítko **Přidat platformu** .
    1. Pro **mobilní a desktopové aplikace**vyberte **mobilní a desktopové aplikace**.
    1. V případě **identifikátorů URI přesměrování**zaškrtněte **https://login.microsoftonline.com/common/oauth2/nativeclient** políčko.
    1. Vyberte **Konfigurovat**.

1. Vyberte **oprávnění rozhraní API**a pak postupujte takto:

    1. Vyberte tlačítko **Přidat oprávnění**.
    1. Vyberte kartu **Moje rozhraní API** .
    1. V seznamu rozhraní API vyberte **AppModelv2-NativeClient-dotnet-TODOLISTSERVICE API** nebo název, který jste zadali pro webové rozhraní API.
    1. Zaškrtněte políčko **access_as_user** oprávnění, pokud již není vybráno. V případě potřeby použijte vyhledávací pole.
    1. Klikněte na tlačítko **Přidat oprávnění** .

### <a name="configure-your-project"></a>Konfigurace projektu

Ke konfiguraci projektu TodoListClient postupujte takto:

1. Na portálu **Registrace aplikací** na stránce **Přehled** Zkopírujte hodnotu **ID aplikace (klienta)**.

1. V kořenové složce projektu TodoListClient otevřete soubor *app.config* a vložte hodnotu ID aplikace do `ida:ClientId` parametru.

## <a name="run-your-todolistclient-project"></a>Spuštění projektu TodoListClient

Chcete-li spustit projekt TodoListClient, postupujte takto:

1. Stisknutím klávesy F5 otevřete projekt TodoListClient. Stránka projektu by se měla otevřít.

1. V pravém horním rohu vyberte **Přihlásit**se a pak se přihlaste se stejnými přihlašovacími údaji, které jste použili k registraci aplikace, nebo se přihlaste jako uživatel ve stejném adresáři.

   Pokud se přihlašujete poprvé, může se zobrazit výzva k vyjádření souhlasu s webovým rozhraním API TodoListService.

   Aby vám pomohli získat přístup k webovému rozhraní API TodoListService a manipulovat se seznamem *úkolů* , přihlašování se také požádá o přístupový token do oboru *access_as_user* .

## <a name="pre-authorize-your-client-application"></a>Předběžná autorizace klientské aplikace

Jedním ze způsobů, jak uživatelům z jiných adresářů povolit přístup k webovému rozhraní API, je předběžnou autorizaci klientské aplikace pro přístup k webovému rozhraní API. To provedete tak, že přidáte ID aplikace z klientské aplikace do seznamu předem autorizovaných aplikací pro vaše webové rozhraní API. Přidáním předem autorizovaného klienta umožníte uživatelům přístup k webovému rozhraní API bez nutnosti vyjádřit souhlas. K předběžné autorizaci klientské aplikace proveďte tyto kroky:

1. Na portálu **Registrace aplikací** otevřete vlastnosti aplikace TodoListService.
1. V části **vystavení rozhraní API** v části **autorizované klientské aplikace**vyberte **Přidat klientskou aplikaci**.
1. Do pole **ID klienta** Vložte ID aplikace TodoListClient.
1. V části **autorizované obory** vyberte rozsah `api://<Application ID>/access_as_user` webového rozhraní API.
1. Vyberte **Přidat aplikaci**.

### <a name="run-your-project"></a>Spusťte projekt

1. Stisknutím klávesy F5 spusťte projekt. Vaše aplikace TodoListClient by se měla otevřít.
1. V pravém horním rohu vyberte **Přihlásit**se a pak se přihlaste pomocí osobního účet Microsoft, jako je například live.com nebo Hotmail.com, nebo pracovní nebo školní účet.

## <a name="optional-limit-sign-in-access-to-certain-users"></a>Volitelné: Omezte přístup k určitým uživatelům na základě přihlášení.

Ve výchozím nastavení platí, že pokud jste provedli předchozí kroky, můžou všechny osobní účty, například outlook.com nebo live.com, nebo pracovní nebo školní účty z organizací, které jsou integrované s Azure AD, vyžadovat tokeny a přistupovat k vašemu webovému rozhraní API.

K určení toho, kdo se může přihlásit k aplikaci, použijte jednu z následujících možností:

### <a name="option-1-limit-access-to-a-single-organization-single-tenant"></a>Možnost 1: omezení přístupu k jedné organizaci (jeden tenant)

Přístup k aplikaci můžete omezit na uživatelské účty, které jsou v jednom tenantovi služby Azure AD, včetně *účtů hostů* tohoto tenanta. Tento scénář je běžný pro *obchodní aplikace*.

1. Otevřete soubor *app_start \Startup.auth* a pak změňte hodnotu koncového bodu metadat, který se předává do `OpenIdConnectSecurityTokenProvider` do `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` . Můžete také použít název tenanta, jako je například `contoso.onmicrosoft.com` .
2. Ve stejném souboru nastavte `ValidIssuer` vlastnost na `TokenValidationParameters` k a `"https://sts.windows.net/{Tenant ID}/"` nastavte `ValidateIssuer` argument na `true` .

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Možnost 2: použití vlastní metody pro ověření vystavitelů

Můžete implementovat vlastní metodu pro ověření vystavitelů pomocí `IssuerValidator` parametru. Další informace o tomto parametru naleznete v tématu [Třída TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet&preserve-view=true).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o scénáři chráněného webového rozhraní API, který podporuje platforma Microsoft Identity Platform:
> [!div class="nextstepaction"]
> [Scénář chráněného webového rozhraní API](scenario-protected-web-api-overview.md)
