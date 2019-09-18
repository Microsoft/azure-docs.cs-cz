---
title: Kurz – udělení přístupu k webovému rozhraní API Node. js z desktopové aplikace – Azure Active Directory B2C | Microsoft Docs
description: Kurz popisující použití Active Directory B2C k ochraně webového rozhraní API Node.js a jeho volání z desktopové aplikace .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 75469d4522cea2914e0f69d5aa1850e468cb0d50
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064835"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Kurz: Udělení přístupu k webovému rozhraní API Node. js z desktopové aplikace pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak volat prostředek webového rozhraní API typu Azure Active Directory B2C (Azure AD B2C) z desktopové aplikace Windows Presentation Foundation (WPF).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Aktualizace ukázky pro použití aplikace

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Proveďte kroky a požadavky v [kurzu: Povolte ověřování desktopových aplikací pomocí účtů,](active-directory-b2c-tutorials-desktop-app.md)které používají Azure Active Directory B2C.

## <a name="add-a-web-api-application"></a>Přidání aplikace webového rozhraní API

Prostředky webového rozhraní API musí být ve vašem tenantovi zaregistrované, aby mohly přijímat a reagovat na požadavky na chráněné prostředky klientskými aplikacemi, které prezentují přístupový token.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikace**a pak vyberte **Přidat**.
5. Zadejte název aplikace. Například *webapi1*.
6. V případě **zahrnutí webové aplikace/webového rozhraní API** a **Povolení implicitního toku**vyberte **Ano**.
7. V případě **adresy URL odpovědi**Zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. V tomto kurzu se ukázka spouští místně a naslouchá na `https://localhost:5000`.
8. Pro **identifikátor URI ID aplikace**zadejte identifikátor použitý pro vaše webové rozhraní API. Vygeneruje se pro vás úplný identifikátor URI včetně domény. Například, `https://contosotenant.onmicrosoft.com/api`.
9. Klikněte na možnost **Vytvořit**.
10. Na stránce Vlastnosti Poznamenejte ID aplikace, které použijete při konfiguraci webové aplikace.

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob, jak řídit přístup k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Někteří uživatelé například můžou mít oprávnění ke čtení i zápisu, zatímco jiní uživatelé můžou mít oprávnění jen pro čtení. V tomto kurzu nadefinujete pro webové rozhraní API oprávnění ke čtení.

1. Vyberte **aplikace**a pak vyberte *webapi1*.
2. Vyberte **publikované obory**.
3. Pro **Rozsah**zadejte `Hello.Read`, a pro Popis zadejte `Read access to hello`.
4. Pro **Rozsah**zadejte `Hello.Write`, a pro Popis zadejte `Write access to hello`.
5. Klikněte na **Uložit**.

Publikované obory je možné použít k udělení oprávnění k webovému rozhraní API klientské aplikaci.

## <a name="grant-permissions"></a>Udělení oprávnění

Chcete-li volat chráněné webové rozhraní API z aplikace, musíte aplikaci udělit oprávnění k rozhraní API. V kurzu požadavků jste vytvořili webovou aplikaci v Azure AD B2C s názvem *app1*. Tuto aplikaci použijete pro volání webového rozhraní API.

1. Vyberte **aplikace**a pak vyberte *nativeapp1*.
2. Vyberte **přístup přes rozhraní API**a pak vyberte **Přidat**.
3. V rozevíracím seznamu **Vyberte rozhraní API** vyberte možnost *webapi1*.
4. V rozevíracím seznamu **Vybrat obory** vyberte obory **Hello. Read** a **Hello. Write** , které jste předtím definovali.
5. Klikněte na **OK**.

Uživatel se ověřuje pomocí Azure AD B2C pro použití desktopové aplikace WPF. Desktopová aplikace získá udělení autorizace z Azure AD B2C pro přístup k chráněnému webovému rozhraní API.

## <a name="configure-the-sample"></a>Konfigurace ukázky

Když je teď webové rozhraní API zaregistrované a máte definované obory, nakonfigurujete kód webového rozhraní API tak, aby používal vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkovou webovou aplikaci Node. js, kterou si můžete stáhnout z GitHubu.

[Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) nebo naklonujte ukázkovou webovou aplikaci z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```
Ukázka webového rozhraní API Node.js s využitím knihovny Passport.js umožňuje ochranu volání do tohoto rozhraní API pomocí služby Azure AD B2C.

1. Otevřete soubor `index.js`.
2. Nakonfigurujte ukázku s použitím informací o registraci tenanta Azure AD B2C. Změňte následující řádky kódu:

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Spuštění ukázky

1. Spusťte příkazový řádek Node.js.
2. Přejděte do adresáře obsahujícího ukázku Node.js. Příklad: `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Spusťte následující příkazy:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Spuštění desktopové aplikace

1. Otevřete řešení **Active-Directory-B2C-WPF** v aplikaci Visual Studio.
2. Stisknutím klávesy **F5** spusťte desktopovou aplikaci.
3. Přihlaste se pomocí e-mailové adresy a hesla, které jste použili v [kurzu Ověřování uživatelů pomocí Azure Active Directory D2C v desktopové aplikaci](active-directory-b2c-tutorials-desktop-app.md).
4. Klikněte na tlačítko **Call API** (Zavolat rozhraní API).

Aplikace klasické pracovní plochy vytvoří požadavek na webové rozhraní API a získá odpověď s zobrazovaným jménem přihlášeného uživatele. Chráněná desktopová aplikace volá chráněné webové rozhraní API ve vašem tenantovi Azure AD B2C.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Aktualizace ukázky pro použití aplikace

> [!div class="nextstepaction"]
> [Kurz: Přidejte do svých aplikací zprostředkovatele identity v Azure Active Directory B2C](tutorial-add-identity-providers.md)
