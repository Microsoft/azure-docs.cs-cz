---
title: Kurz – Poskytnutí přístupu k webovému rozhraní API Node.js z desktopové aplikace pomocí Azure Active Directory B2C | Microsoft Docs
description: Kurz popisující použití Active Directory B2C k ochraně webového rozhraní API Node.js a jeho volání z desktopové aplikace .NET.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/01/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 1ce74afd8ee2266439a591730686b4f5277e1b03
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322001"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Kurz: Poskytnutí přístupu k webovému rozhraní API Node.js z desktopové aplikace pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak volat prostředek webového rozhraní API Node.js chráněný službou Azure Active Directory (Azure AD) B2C z desktopové aplikace WPF (Windows Presentation Foundation).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Registrace webového rozhraní API v tenantovi Azure AD B2C
> * Definice a konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění k webovému rozhraní API aplikaci
> * Aktualizace vzorového kódu tak, aby pomocí Azure AD B2C chránil webové rozhraní API

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Dokončete [kurz Ověřování uživatelů pomocí Azure Active Directory B2C v desktopové aplikaci](active-directory-b2c-tutorials-desktop-app.md).
* Nainstalujte sadu [Visual Studio 2017](https://www.visualstudio.com/downloads/) se sadami funkcí **Vývoj desktopových aplikací .NET** a **Vývoj pro ASP.NET a web**.
* Instalovat [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>Registrace webového rozhraní API

Prostředky webového rozhraní API je nejprve potřeba zaregistrovat ve vašem tenantovi, a až pak můžou přijímat a reagovat na [požadavky na chráněné prostředky](../active-directory/develop/developer-glossary.md#resource-server) od [klientských aplikací](../active-directory/develop/developer-glossary.md#client-application), které obsahují [přístupový token](../active-directory/develop/developer-glossary.md#access-token) ze služby Azure Active Directory. Registrací se ve vašem tenantovi vytvoří [objekt aplikace a instanční objekt](../active-directory/develop/developer-glossary.md#application-object). 

Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Ze seznamu služeb na webu Azure Portal vyberte **Azure AD B2C**.

2. V nastavení B2C klikněte na **Aplikace** a pak klikněte na **Přidat**.

    K registraci ukázkového webového rozhraní API ve vašem tenantovi použijte následující nastavení.
    
    ![Přidání nového rozhraní API](media/active-directory-b2c-tutorials-desktop-app-webapi/web-api-registration.png)
    
    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | My sample Node.js web API | Zadejte **Název**, který popíše vaše webové rozhraní API pro vývojáře. |
    | **Zahrnout webovou aplikaci nebo webové rozhraní API** | Ano | Pro webové rozhraní API vyberte **Ano**. |
    | **Povolit implicitní tok** | Ano | Vyberte **Ano**, protože rozhraní API používá [Přihlášení OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **Adresa URL odpovědi** | `http://localhost:5000` | Adresy URL odpovědí jsou koncové body, kam Azure AD B2C vrací všechny tokeny, které vaše rozhraní API požaduje. V tomto kurzu se ukázkové webové rozhraní API spouští místně (localhost) a naslouchá na portu 5000. |
    | **Identifikátor URI ID aplikace** | demoapi | Tento identifikátor URI jednoznačně identifikuje rozhraní API v tenantovi. Díky tomu můžete v jednom tenantovi registrovat více rozhraní API. [Obory](../active-directory/develop/developer-glossary.md#scopes) řídí přístup k chráněnému prostředku rozhraní API a definují se pro jednotlivé identifikátory URI ID aplikace. |
    | **Nativní klient** | Ne | Vzhledem k tomu, že se jedná o webové rozhraní API, a ne nativního klienta, vyberte Ne. |
    
3. Kliknutím na **Vytvořit** své rozhraní API zaregistrujte.

Zaregistrovaná rozhraní API se zobrazí v seznamu aplikací pro příslušného tenanta Azure AD B2C. Vyberte ze seznamu vaše webové rozhraní API. Zobrazí se podokno vlastností webového rozhraní API.

![Vlastnosti webového rozhraní API](./media/active-directory-b2c-tutorials-web-api/b2c-web-api-properties.png)

Poznamenejte si **ID klienta aplikace**. Toto ID jednoznačně identifikuje rozhraní API a je potřeba při konfiguraci rozhraní API později v tomto kurzu.

Registrací webového rozhraní API v Azure AD B2C se nadefinuje vztah důvěryhodnosti. Vzhledem k tomu, že je rozhraní API zaregistrované v B2C, může teď důvěřovat přístupovým tokenům B2C přijímaným z jiných aplikací.

## <a name="define-and-configure-scopes"></a>Definice a konfigurace oborů

[Obory](../active-directory/develop/developer-glossary.md#scopes) poskytují způsob řízení přístupu k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Někteří uživatelé například můžou mít oprávnění ke čtení i zápisu, zatímco jiní uživatelé můžou mít oprávnění jen pro čtení. V tomto kurzu nadefinujete pro webové rozhraní API oprávnění ke čtení i zápisu.

### <a name="define-scopes-for-the-web-api"></a>Definice oborů pro webové rozhraní API

Zaregistrovaná rozhraní API se zobrazí v seznamu aplikací pro příslušného tenanta Azure AD B2C. Vyberte ze seznamu vaše webové rozhraní API. Zobrazí se podokno vlastností webového rozhraní API.

Klikněte na **Publikované obory (Preview)**.

Nakonfigurujte obory pro rozhraní API přidáním následujících záznamů. 

![obory definované ve webovém rozhraní API](media/active-directory-b2c-tutorials-web-api/scopes-defined-in-web-api.png)

| Nastavení      | Navrhovaná hodnota  | Popis                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Rozsah** | demo.read | Oprávnění ke čtení pro ukázkové rozhraní API|

Klikněte na **Uložit**.

Publikované obory je možné použít k udělení oprávnění k webovému rozhraní API klientské aplikaci.

### <a name="grant-app-permissions-to-web-api"></a>Udělení oprávnění k webovému rozhraní API aplikaci

Pokud chcete volat chráněné webové rozhraní API z aplikace, musíte aplikaci udělit oprávnění k rozhraní API. V tomto kurzu použijete desktopovou aplikaci vytvořenou v kurzu [Ověřování uživatelů pomocí Azure Active Directory D2C v desktopové aplikaci](active-directory-b2c-tutorials-desktop-app.md).

1. Na webu Azure Portal vyberte ze seznamu služeb **Azure AD B2C** a kliknutím na **Aplikace** zobrazte seznam zaregistrovaných aplikací.

2. Ze seznamu aplikací vyberte **My Sample WPF App**, klikněte na **Přístup přes rozhraní API (Preview)** a pak na **Přidat**.

3. V rozevíracím seznamu **Vybrat rozhraní API** vyberte své zaregistrované webové rozhraní API **My sample Node.js web API**.

4. V rozevíracím seznamu **Vybrat obory** vyberte obory, které jste definovali v registraci webového rozhraní API.

    ![výběr oborů pro aplikaci](media/active-directory-b2c-tutorials-web-api/selecting-scopes-for-app.png)

5. Klikněte na **OK**.

Vaše aplikace **My Sample WPF App** je zaregistrovaná a může volat rozhraní **My sample Node.js web API**. Uživatel se [ověří](../active-directory/develop/developer-glossary.md#authentication) v Azure AD B2C, aby mohl použít desktopovou aplikaci WPF. Desktopová aplikace získá z Azure AD B2C [udělení autorizace](../active-directory/develop/developer-glossary.md#authorization-grant) pro přístup k chráněnému webovému rozhraní API.

## <a name="update-web-api-code"></a>Aktualizace kódu webového rozhraní API

Když je teď webové rozhraní API zaregistrované a máte definované obory, je potřeba nakonfigurovat kód webového rozhraní API tak, aby používalo vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkovou webovou aplikaci Node.js, kterou si můžete stáhnout z GitHubu. 

[Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) nebo naklonujte ukázkovou webovou aplikaci z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
Ukázka webového rozhraní API Node.js s využitím knihovny Passport.js umožňuje ochranu volání do tohoto rozhraní API pomocí služby Azure AD B2C. 

### <a name="configure-the-web-api"></a>Konfigurace webového rozhraní API

1. Otevřete soubor `index.js` v ukázce webového rozhraní API Node.js.
2. Nakonfigurujte ukázku s použitím informací o registraci tenanta Azure AD B2C. Změňte následující řádky kódu:

```nodejs
var tenantID = "<your-tenant-name>.onmicrosoft.com";
var clientID = "<Application ID for your Node.js Web API>";
var policyName = "B2C_1_SiUpIn";  // Sign-in / sign-up policy name
```

### <a name="configure-the-desktop-app"></a>Konfigurace desktopové aplikace

1. Otevřete v sadě Visual Studio řešení `active-directory-b2c-wpf` z [kurzu Ověřování uživatelů pomocí Azure Active Directory B2C v desktopové aplikaci](active-directory-b2c-tutorials-desktop-app.md).

## <a name="run-the-sample"></a>Spuštění ukázky

Spuštění webového rozhraní API Node.js:

1. Spusťte příkazový řádek Node.js.
2. Přejděte do adresáře obsahujícího ukázku Node.js. Příklad: `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Spusťte následující příkazy:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```
Spuštění desktopové aplikace:

1. Stisknutím klávesy **F5** spusťte desktopovou aplikaci.
2. Přihlaste se pomocí e-mailové adresy a hesla, které jste použili v [kurzu Ověřování uživatelů pomocí Azure Active Directory D2C v desktopové aplikaci](active-directory-b2c-tutorials-desktop-app.md).
3. Klikněte na tlačítko **Call API** (Zavolat rozhraní API). 

Desktopová aplikace odešle požadavek do webového rozhraní API a získá odpověď obsahující zobrazované jméno přihlášeného uživatele. Vaše chráněná desktopová aplikace volá chráněné webové rozhraní API ve vašem tenantovi Azure AD B2C.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Svého tenanta Azure AD B2C můžete použít i k vyzkoušení dalších kurzů k Azure AD B2C. Jakmile už ho nebudete potřebovat, můžete [svého tenanta Azure AD B2C odstranit](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Další postup

Tento článek vás provedl ochranou webového rozhraní API ASP.NET prostřednictvím registrace a definice oborů v Azure AD B2C. Další informace najdete v dostupných ukázkách kódu pro Azure AD B2C.

> [!div class="nextstepaction"]
> [Ukázky kódu pro Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
