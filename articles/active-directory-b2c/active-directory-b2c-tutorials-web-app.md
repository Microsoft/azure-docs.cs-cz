---
title: Kurz – povolení ověřování v rámci webové aplikace – Azure Active Directory B2C | Microsoft Docs
description: Kurz týkající se použití Azure Active Directory B2C k zajištění přihlašování uživatelů ve webové aplikaci ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: bcfd1ef02c68de7709cb8642b94f23a6884ea156
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464760"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Kurz: Povolení ověřování ve webové aplikaci pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak pomocí Azure Active Directory (Azure AD) B2C přihlašovat a registrovat uživatele ve webové aplikaci ASP.NET. Azure AD B2C umožňuje vašim aplikacím ověřovat účty v sociálních sítích, podnikové účty a účty Azure Active Directory pomocí protokolů Open Standard.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- [Vytvořte toky uživatelů](tutorial-create-user-flows.md) a povolte ve své aplikaci uživatelské prostředí.
- Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/downloads/) s úlohou **vývoje ASP.NET a webu** .

## <a name="update-the-application"></a>Aktualizace aplikace

V kurzu, který jste dokončili v rámci požadavků, jste přidali webovou aplikaci do Azure AD B2C. Pokud chcete povolit komunikaci s ukázkou v tomto kurzu, musíte do aplikace v Azure AD B2C přidat identifikátor URI přesměrování.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikace**a pak vyberte aplikaci *WebApp1* .
5. V části **Adresa URL odpovědi**přidejte `https://localhost:44316`.
6. Vyberte **Uložit**.
7. Na stránce Vlastnosti Poznamenejte ID aplikace, které použijete při konfiguraci webové aplikace.
8. Vyberte **klíče**, vyberte **Generovat klíč**a vyberte **Uložit**. Poznamenejte si klíč, který budete používat při konfiguraci webové aplikace.

## <a name="configure-the-sample"></a>Konfigurace ukázky

V tomto kurzu nakonfigurujete ukázku, kterou si můžete stáhnout z GitHubu. Ukázka používá ASP.NET k poskytnutí jednoduchého seznamu úkolů. Ukázka používá [Microsoft Owin middleware Components](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) nebo naklonujte ukázku z GitHubu. Ujistěte se, že cesta ke složce, do které extrahujete ukázkový soubor, obsahuje méně než 260 znaků.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Následující dva projekty jsou v ukázkovém řešení:

- **TaskWebApp** – vytvoření a úprava seznamu úkolů V ukázce se k registraci nebo přihlašování uživatelů používá tok uživatelů **registrace nebo přihlašování** .
- **TaskService** – podporuje funkce vytvoření, čtení, aktualizace a odstranění seznamu úkolů. Rozhraní API je chráněno pomocí Azure AD B2C a volá ho TaskWebApp.

Tuto ukázku změníte tak, aby používala aplikaci, která je zaregistrovaná ve vašem tenantovi, což zahrnuje ID aplikace a klíč, který jste předtím nahráli. Také nakonfigurujete toky uživatelů, které jste vytvořili. Ukázka definuje konfigurační hodnoty jako nastavení v souboru Web. config. Postup změny nastavení:

1. Otevřete řešení **B2C-WebAPI-DotNet** v sadě Visual Studio.
2. V projektu **TaskWebApp** otevřete soubor **Web. config** . Nahraďte hodnotu `ida:Tenant` názvem tenanta, kterého jste vytvořili. Nahraďte hodnotu `ida:ClientId` za ID aplikace, které jste si poznamenali. Nahraďte hodnotu `ida:ClientSecret` klíčem, který jste si poznamenali. Před přidáním tajného klíče klienta do souboru Web. config je nutné kód XML zakódovat.
3. V souboru **Web.config** nahraďte hodnotu `ida:SignUpSignInPolicyId` za `b2c_1_signupsignin1`. Nahraďte hodnotu `ida:EditProfilePolicyId` za `b2c_1_profileediting1`. Nahraďte hodnotu `ida:ResetPasswordPolicyId` za `b2c_1_passwordreset1`.


## <a name="run-the-sample"></a>Spuštění ukázky

1. V Průzkumník řešení klikněte pravým tlačítkem na projekt **TaskWebApp** a pak klikněte na **nastavit jako spouštěný projekt**.
2. Stiskněte klávesu **F5**. Spustí se výchozí prohlížeč a otevře se adresa místního webu `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

1. Pokud se chcete zaregistrovat jako uživatel aplikace, klikněte na **zaregistrovat nebo přihlásit** se. Použije se tok uživatele **b2c_1_signupsignin1** .
2. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Protože ještě nemáte účet, vyberte **zaregistrovat se hned teď**. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace také shromažďuje heslo uživatele a požadované atributy definované v toku uživatele.
3. Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů.

    ![Přihlašovací stránka zobrazená jako součást pracovního postupu přihlášení/přihlášení](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.PNG)

4. Kliknutím na **Create** (Vytvořit) vytvořte místní účet v tenantovi Azure AD B2C.

Uživatel teď může použít svou e-mailovou adresu k přihlášení a používání webové aplikace.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

> [!div class="nextstepaction"]
> [Kurz: Použití Azure Active Directory B2C k ochraně webového rozhraní API ASP.NET](active-directory-b2c-tutorials-web-api.md)
