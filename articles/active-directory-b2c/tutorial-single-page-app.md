---
title: 'Kurz: Povolení ověřování v jednostránkové aplikaci'
titleSuffix: Azure AD B2C
description: V tomto kurzu se dozvíte, jak pomocí služby Azure Active Directory B2C poskytnout přihlášení uživatele pro jednostránkovou aplikaci (SPA) založenou na jazyce JavaScript.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 435800d9c6bfd9131d50681a9808f9836104fac0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183331"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c-azure-ad-b2c"></a>Kurz: Povolení ověřování v jednostránkové aplikaci pomocí služby Azure Active Directory B2C (Azure AD B2C)

Tento kurz ukazuje, jak používat Azure Active Directory B2C (Azure AD B2C) k přihlášení a registraci uživatelů v jednostránkové aplikaci (SPA). Azure AD B2C umožňuje vašim aplikacím ověřování na účty na sociálních sítích, podnikových účtech a účtech Azure Active Directory pomocí otevřených standardních protokolů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Před pokračováním v krocích v tomto kurzu potřebujete následující prostředky Azure AD B2C na místě:

* [Klient Azure AD B2C](tutorial-create-tenant.md)
* [Aplikace registrovaná](tutorial-register-applications.md) ve vašem tenantovi
* [Toky uživatelů vytvořené](tutorial-create-user-flows.md) ve vašem tenantovi

Kromě toho budete potřebovat následující v prostředí místního rozvoje:

* Editor kódu, například [Visual Studio Code](https://code.visualstudio.com/) nebo Visual Studio [2019](https://www.visualstudio.com/downloads/)
* [Sada .NET Core SDK 2.2](https://dotnet.microsoft.com/download) nebo novější
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Aktualizace aplikace

Ve druhém kurzu, který jste dokončili jako součást předpokladů, jste zaregistrovali webovou aplikaci v Azure AD B2C. Chcete-li povolit komunikaci s ukázkou v kurzu, musíte přidat identifikátor URI přesměrování do aplikace v Azure AD B2C.

K aktualizaci aplikace můžete použít aktuální prostředí **aplikací** nebo naše nové jednotné **registrace aplikací (Preview).** [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
1. V levém horním rohu portálu Azure vyberte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Aplikace**a pak vyberte aplikaci *webapp1.*
1. V části Adresa `http://localhost:6420`URL **odpovědi**přidejte .
1. Vyberte **Uložit**.
1. Na stránce vlastností zaznamenejte **ID aplikace**. ID aplikace se použije v pozdějším kroku při aktualizaci kódu ve webové aplikaci s jednou stránkou.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (náhled),** vyberte kartu **Vlastní aplikace** a pak vyberte aplikaci *webapp1.*
1. Vyberte **Ověřování**, pak vyberte **Vyzkoušet nové prostředí** (pokud je zobrazeno).
1. V části **Web**vyberte odkaz `http://localhost:6420`Přidat identifikátor **URI,** zadejte a pak vyberte **Uložit**.
1. Vyberte **Přehled**.
1. Zaznamenejte **ID aplikace (klienta)** pro pozdější krok při aktualizaci kódu ve webové aplikaci s jednou stránkou.

* * *

## <a name="get-the-sample-code"></a>Získání ukázkového kódu

V tomto kurzu nakonfigurujete ukázku kódu, kterou stáhnete z GitHubu. Ukázka ukazuje, jak jednostránková aplikace můžete použít Azure AD B2C pro registraci uživatele a přihlášení a volání chráněné webové rozhraní API.

[Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Aktualizace ukázky

Teď, když jste získali ukázku, aktualizujte kód s názvem klienta Azure AD B2C a ID aplikace, které jste zaznamenali v předchozím kroku.

1. Otevřete `index.html` soubor v kořenovém adresáři ukázkového adresáře.
1. V `msalConfig` definici upravte hodnotu **ClientId** pomocí ID aplikace, které jste zaznamenali v předchozím kroku. Dále aktualizujte **hodnotu IDENTIFIKÁTORU** URI autority s názvem klienta Azure AD B2C. Také aktualizujte identifikátor URI názvem toku uživatele pro přihlášení nebo přihlášení, který jste vytvořili v jednom z předpokladů (například *B2C_1_signupsignin1).*

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Název toku uživatele použitého v tomto kurzu je **B2C_1_signupsignin1**. Pokud používáte jiný název toku uživatele, zadejte tento název v hodnotě. `authority`

## <a name="run-the-sample"></a>Spuštění ukázky

1. Otevřete okno konzoly a změňte na adresář obsahující ukázku. Například:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Spusťte následující příkazy:

    ```
    npm install && npm update
    node server.js
    ```

    V okně konzoly se zobrazí číslo portu místně spuštěného serveru Node.js:

    ```
    Listening on port 6420...
    ```

1. Chcete-li aplikaci zobrazit, přejděte `http://localhost:6420` do prohlížeče.

Ukázka podporuje registraci, přihlášení, úpravy profilu a resetování hesla. Tento kurz upozorňuje na to, jak se uživatel zaregistruje pomocí e-mailové adresy.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

> [!WARNING]
> Po registraci nebo přihlášení se může zobrazit [chyba nedostatečná oprávnění](#error-insufficient-permissions). Vzhledem k aktuální implementaci ukázky kódu se očekává tato chyba. Tento problém bude vyřešen v budoucí verzi ukázky kódu, kdy bude toto upozornění odebráno.

1. Vyberte **Přihlásit,** chcete-li zahájit *tok B2C_1_signupsignin1* uživatele, který jste zadali v předchozím kroku.
1. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Vzhledem k tomu, že ještě nemáte účet, vyberte odkaz **Zaregistrovat nyní.**
1. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace také shromažďuje heslo uživatele a požadované atributy definované v toku uživatele.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů.

    ![Registrační stránka prezentovaná tokem uživatelů pro přihlášení/registraci](./media/tutorial-single-page-app/azure-ad-b2c-sign-up-workflow.png)

1. Vyberte **Vytvořit,** chcete-li vytvořit místní účet v adresáři Azure AD B2C.

Když vyberete **Vytvořit**, zaregistrovaná stránka se zavře a přihlašovací stránka se znovu zobrazí.

Nyní se můžete přihlásit k aplikaci pomocí e-mailové adresy a hesla.

### <a name="error-insufficient-permissions"></a>Chyba: nedostatečná oprávnění

Po přihlášení může aplikace vrátit chybu nedostatečná oprávnění:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

Tato chyba se zobrazí, protože webová aplikace se pokouší o přístup k webovému rozhraní API chráněnému ukázkovým adresářem *fabrikamb2c*. Vzhledem k tomu, že váš přístupový token je platný jenom pro váš adresář Azure AD, volání rozhraní API je neoprávněné.

Chcete-li tuto chybu opravit, pokračujte dalším kurzem v řadě (viz [Další kroky)](#next-steps)a vytvořte chráněné webové rozhraní API pro váš adresář.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

Nyní přejít na další kurz v sérii udělit přístup k chráněné webové api z SPA:

> [!div class="nextstepaction"]
> [Kurz: Udělit přístup k webovému rozhraní API ASP.NET Core z spa pomocí azure ad b2c >](tutorial-single-page-app-webapi.md)
