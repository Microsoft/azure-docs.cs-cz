---
title: 'Kurz: povolení ověřování ve webové aplikaci'
titleSuffix: Azure AD B2C
description: Kurz týkající se použití Azure Active Directory B2C k zajištění přihlašování uživatelů ve webové aplikaci ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b4305e9b3cfdb5e05ce76ee1811dc0d2dcc265b7
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950191"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Kurz: povolení ověřování ve webové aplikaci pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak pomocí Azure Active Directory B2C (Azure AD B2C) přihlašovat a zaregistrovat uživatele ve webové aplikaci v ASP.NET. Azure AD B2C umožňuje vašim aplikacím ověřovat účty v sociálních sítích, podnikové účty a účty Azure Active Directory pomocí protokolů Open Standard.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

* [Vytvořte toky uživatelů](tutorial-create-user-flows.md) a povolte ve své aplikaci uživatelské prostředí.
* Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/downloads/) s úlohou **vývoje ASP.NET a webu** .

## <a name="update-the-application-registration"></a>Aktualizace registrace aplikace

V kurzu, který jste dokončili v rámci požadavků, jste zaregistrovali webovou aplikaci v Azure AD B2C. Pokud chcete povolit komunikaci s ukázkou v tomto kurzu, je nutné přidat identifikátor URI přesměrování a vytvořit tajný klíč klienta (klíč) pro registrovanou aplikaci.

### <a name="add-a-redirect-uri-reply-url"></a>Přidat identifikátor URI přesměrování (adresa URL odpovědi)

K aktualizaci aplikace můžete použít aktuální prostředí **aplikací** nebo naše nové prostředí Unified **Registrace aplikací (Preview)** . [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace**a pak vyberte aplikaci *WebApp1* .
1. V části **Adresa URL odpovědi**přidejte `https://localhost:44316`.
1. Vyberte **Save** (Uložit).
1. Na stránce Vlastnosti Poznamenejte ID aplikace pro použití v pozdějším kroku při konfiguraci webové aplikace.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registrace aplikací (Preview)](#tab/app-reg-preview/)

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (Preview)** , vyberte kartu **vlastněné aplikace** a pak vyberte aplikaci *WebApp1* .
1. Vyberte **ověřování**a pak vyberte **vyzkoušet nové prostředí** (Pokud je zobrazeno).
1. V části **Web**vyberte odkaz **Přidat identifikátor URI** , zadejte `https://localhost:44316`a pak vyberte **Uložit**.
1. Vyberte **Přehled**.
1. Poznamenejte si **ID aplikace (klienta)** pro použití v pozdějším kroku při konfiguraci webové aplikace.

* * *

### <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Dále vytvořte tajný klíč klienta pro registrovanou webovou aplikaci. Ukázka kódu webové aplikace tuto identitu používá k prokázání své identity při žádosti o tokeny.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>Konfigurace ukázky

V tomto kurzu nakonfigurujete ukázku, kterou si můžete stáhnout z GitHubu. Ukázka používá ASP.NET k poskytnutí jednoduchého seznamu úkolů. Ukázka používá [Microsoft Owin middleware Components](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) nebo naklonujte ukázku z GitHubu. Ujistěte se, že cesta ke složce, do které extrahujete ukázkový soubor, obsahuje méně než 260 znaků.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Následující dva projekty jsou v ukázkovém řešení:

* **TaskWebApp** – vytvoření a úprava seznamu úkolů Ukázka používá k registraci a přihlašování uživatelů tok uživatelů **registrace nebo přihlašování** .
* **TaskService** – podporuje funkce vytvoření, čtení, aktualizace a odstranění seznamu úkolů. Rozhraní API je chráněno pomocí Azure AD B2C a volá ho TaskWebApp.

Tuto ukázku změníte tak, aby používala aplikaci, která je zaregistrovaná ve vašem tenantovi, což zahrnuje ID aplikace a klíč, který jste předtím nahráli. Také nakonfigurujete toky uživatelů, které jste vytvořili. Ukázka definuje konfigurační hodnoty jako nastavení v souboru *Web. config* .

Aktualizujte nastavení v souboru Web. config tak, aby fungovala s vaším uživatelským tokem:

1. Otevřete řešení **B2C-WebAPI-DotNet** v sadě Visual Studio.
1. V projektu **TaskWebApp** otevřete soubor **Web. config** .
    1. Aktualizujte hodnotu `ida:Tenant` a `ida:AadInstance` název vytvořeného tenanta Azure AD B2C. Název `fabrikamb2c` nahraďte třeba názvem `contoso`.
    1. Nahraďte hodnotu `ida:ClientId` číslem ID aplikace, kterou jste si poznamenali.
    1. Nahraďte hodnotu `ida:ClientSecret` klíčem, který jste si poznamenali. Před přidáním tajného klíče klienta do souboru Web. config je nutné kód XML zakódovat.
    1. Hodnotu `ida:SignUpSignInPolicyId` nahraďte `b2c_1_signupsignin1`.
    1. Hodnotu `ida:EditProfilePolicyId` nahraďte `b2c_1_profileediting1`.
    1. Hodnotu `ida:ResetPasswordPolicyId` nahraďte `b2c_1_passwordreset1`.

## <a name="run-the-sample"></a>Spuštění ukázky

1. V Průzkumník řešení klikněte pravým tlačítkem na projekt **TaskWebApp** a pak klikněte na **nastavit jako spouštěný projekt**.
1. Stiskněte klávesu **F5**. Spustí se výchozí prohlížeč a otevře se adresa místního webu `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

1. Pokud se chcete zaregistrovat jako uživatel aplikace, vyberte **zaregistrovat nebo přihlásit** se. Použije se tok uživatele **b2c_1_signupsignin1** .
1. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Protože ještě nemáte účet, vyberte **zaregistrovat se hned teď**. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace také shromažďuje heslo uživatele a požadované atributy definované v toku uživatele.
1. Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů.

    ![Přihlašovací stránka zobrazená jako součást pracovního postupu přihlášení/přihlášení](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.PNG)

1. Vyberte **vytvořit** k vytvoření místního účtu v Azure AD B2C tenantovi.

Uživatel aplikace teď může používat svou e-mailovou adresu k přihlášení a používání webové aplikace.

Funkce **seznam úkolů** ale nebude fungovat, dokud nedokončíte další kurz v řadě, [kurz: použití Azure AD B2C k ochraně webového rozhraní API ASP.NET](active-directory-b2c-tutorials-web-api.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

Nyní přejděte k dalšímu kurzu a povolte funkci **seznamu úkolů** webové aplikace. V takovém případě zaregistrujete aplikaci webového rozhraní API ve vašem tenantovi Azure AD B2C a pak upravíte ukázku kódu pro použití vašeho tenanta pro ověřování rozhraní API.

> [!div class="nextstepaction"]
> [Kurz: použití Azure Active Directory B2C k ochraně webového rozhraní API v ASP.NET >](active-directory-b2c-tutorials-web-api.md)
