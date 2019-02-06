---
title: Kurz – poskytnutí přístupu k webovému rozhraní API Node.js z desktopové aplikace – Azure Active Directory B2C | Dokumentace Microsoftu
description: Kurz popisující použití Active Directory B2C k ochraně webového rozhraní API Node.js a jeho volání z desktopové aplikace .NET.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 90a6a88ff0dc5aab1163e471b24cd1d00e548a1b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755114"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Kurz: Poskytnutí přístupu k webovému rozhraní API Node.js z desktopové aplikace pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak volat prostředek webového rozhraní API Node.js chráněný službou Azure Active Directory (Azure AD) B2C z desktopové aplikace WPF (Windows Presentation Foundation).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání webového rozhraní API aplikaci
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění k webovému rozhraní API
> * Ukázku aktualizovat, a k používání aplikace

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Dokončete kroky a požadavky [kurzu: Povolení aplikace klasické pracovní plochy ověřování účtů pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Přidání webového rozhraní API aplikaci

Webové rozhraní API prostředky musí být zaregistrovaní ve vašem tenantovi předtím, než může přijímat a reagovat na požadavky na chráněný prostředek v klientských aplikacích, které se přístupový token. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikací**a pak vyberte **přidat**.
5. Zadejte název aplikace. Například *webapi1*.
6. Pro **zahrnout webovou aplikaci / webové rozhraní API** a **povolit implicitní tok**vyberte **Ano**.
7. Pro **adresy URL odpovědi**, zadejte koncový bod kam by měl Azure AD B2C vrátí všechny tokeny, které vaše aplikace požaduje. V tomto kurzu se ukázka spouští místně a naslouchá na `https://localhost:5000`.
8. Pro **identifikátor ID URI aplikace**, zadejte identifikátor použitý pro vaše webového rozhraní API. Vygeneruje se pro vás úplný identifikátor URI včetně domény. Například, `https://contosotenant.onmicrosoft.com/api`.
9. Klikněte na možnost **Vytvořit**.
10. Na stránce Vlastnosti zaznamenejte ID aplikace, které budete používat při konfiguraci webové aplikace.

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob řízení přístupu k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Někteří uživatelé například můžou mít oprávnění ke čtení i zápisu, zatímco jiní uživatelé můžou mít oprávnění jen pro čtení. V tomto kurzu nadefinujete pro webové rozhraní API oprávnění ke čtení.

1. Vyberte **aplikací**a pak vyberte *webapi1*.
2. Vyberte **publikované obory**.
3. Pro **oboru**, zadejte `Hello.Read`a popis, zadejte `Read access to hello`.
4. Pro **oboru**, zadejte `Hello.Write`a popis, zadejte `Write access to hello`.
5. Klikněte na **Uložit**.

Publikované obory je možné použít k udělení oprávnění k webovému rozhraní API klientské aplikaci.

## <a name="grant-permissions"></a>Udělení oprávnění

Pokud chcete volat chráněné webové rozhraní API z aplikace, budete muset udělit oprávnění aplikace rozhraní API. V požadovaném kurzu vytvořili webovou aplikaci v Azure AD B2C s názvem *app1*. Volání webového rozhraní API pomocí této aplikace.

1. Vyberte **aplikací**a pak vyberte *nativeapp1*.
2. Vyberte **přístup přes rozhraní API**a pak vyberte **přidat**.
3. V **vybrat rozhraní API** rozevíracím seznamu vyberte *webapi1*.
4. V **vyberte obory** rozevírací seznam, vyberte **Hello.Read** a **Hello.Write** obory, které jste dříve definovali.
5. Klikněte na **OK**.

Uživatel se ověřuje pomocí Azure AD B2C používat desktopová aplikace WPF. Desktopová aplikace získá z Azure AD B2C pro přístup k chráněné webové rozhraní API udělení autorizace.

## <a name="configure-the-sample"></a>Konfigurace ukázky aplikace

Teď, když je webové rozhraní API zaregistrované a máte definované obory, nakonfigurujte webové rozhraní API kódu, aby používala vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkovou webovou aplikaci Node.js, kterou si můžete stáhnout z Githubu. 

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

1. Otevřít **active directory b2c wpf** řešení v sadě Visual Studio.
2. Stisknutím klávesy **F5** spusťte desktopovou aplikaci.
3. Přihlaste se pomocí e-mailové adresy a hesla, které jste použili v [kurzu Ověřování uživatelů pomocí Azure Active Directory D2C v desktopové aplikaci](active-directory-b2c-tutorials-desktop-app.md).
4. Klikněte na tlačítko **Call API** (Zavolat rozhraní API). 

Desktopová aplikace odešle požadavek do webového rozhraní API k a získá odpověď obsahující zobrazované jméno přihlášeného uživatele. Vaše chráněná desktopová aplikace volá chráněné webové rozhraní API ve vašem tenantovi Azure AD B2C.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání webového rozhraní API aplikaci
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění k webovému rozhraní API
> * Ukázku aktualizovat, a k používání aplikace

> [!div class="nextstepaction"]
> [Kurz: Přidat zprostředkovatele identity pro vaše aplikace v Azure Active Directory B2C](tutorial-add-identity-providers.md)
