---
title: Kurz – povolení ověřování v případě webové aplikace – Azure Active Directory B2C | Dokumentace Microsoftu
description: Kurz týkající se použití Azure Active Directory B2C k zajištění přihlašování uživatelů ve webové aplikaci ASP.NET.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: ced74cc5af829c3677a12aaf4bffdf9a518f6053
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755624"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Kurz: Povolení ověřování ve webové aplikaci pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak používat Azure Active Directory (Azure AD) B2C k přihlášení registraci a přihlašování uživatelů ve webové aplikaci ASP.NET. Azure AD B2C umožňuje vaší aplikace k ověřování účtů na sociálních sítích, podnikových účtů a účtů služby Azure Active Directory pomocí protokolů s otevřenými standardy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky aplikace k používání aplikace
> * Registrace pomocí tok uživatele.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- [Vytvořit toky uživatelů](tutorial-create-user-flows.md) umožňující činnosti koncových uživatelů ve vaší aplikaci. 
- Nainstalujte sadu [Visual Studio 2017](https://www.visualstudio.com/downloads/) se sadou funkcí **Vývoj pro ASP.NET a web**.

## <a name="update-the-application"></a>Aktualizace aplikace

V tomto kurzu jste dokončili jako součást požadavků jste přidali webové aplikace v Azure AD B2C. Pokud chcete povolit komunikaci s ukázkou v tomto kurzu, budete muset přidat identifikátor URI přesměrování pro aplikaci v Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikací**a pak vyberte *webapp1* aplikace.
5. V části **adresy URL odpovědi**, přidejte `https://localhost:44316`.
6. Vyberte **Uložit**.
7. Na stránce Vlastnosti zaznamenejte ID aplikace, které budete používat při konfiguraci webové aplikace.
8. Vyberte **klíče**vyberte **vygenerovat klíč**a vyberte **Uložit**. Záznam klíče, které budete používat při konfiguraci webové aplikace.

## <a name="configure-the-sample"></a>Konfigurace ukázky aplikace

V tomto kurzu nakonfigurujete vzorku, který si můžete stáhnout z Githubu. Ukázka používá ASP.NET uvést seznam jednoduchý úkol. Ukázka používá [komponent middlewaru Microsoft OWIN](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) nebo naklonujte ukázku z GitHubu. Ujistěte se, že cesta ke složce, do které extrahujete ukázkový soubor, obsahuje méně než 260 znaků.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

V ukázkovém řešení jsou tyto dva projekty:

- **TaskWebApp** – vytvoření a úpravy seznamu úkolů. Ukázka používá **registrace nebo přihlašování** tok uživatele k registraci nebo přihlašování uživatelů.
- **TaskService** – podporuje vytvoření, čtení, aktualizaci a odstranění seznamu úkolů. Rozhraní API je chráněné službou Azure AD B2C a volané TaskWebApp.

Můžete změnit ukázku použití aplikace, které je registrované ve vašem tenantovi, což zahrnuje ID aplikace a klíč, který jste si předtím poznamenali. Můžete také nakonfigurovat toky uživatelů, které jste vytvořili. Ukázka definuje hodnoty konfigurace jako nastavení v souboru Web.config. Chcete-li změnit nastavení:

1. Otevřete řešení **B2C-WebAPI-DotNet** v sadě Visual Studio.
2. V **TaskWebApp** projekt, otevřete **Web.config** souboru. Nahraďte hodnotu `ida:Tenant` názvem tenanta, kterého jste vytvořili. Nahraďte hodnotu `ida:ClientId` za ID aplikace, které jste si poznamenali. Nahraďte hodnotu `ida:ClientSecret` klíčem, který jste si poznamenali.
3. V souboru **Web.config** nahraďte hodnotu `ida:SignUpSignInPolicyId` za `b2c_1_signupsignin1`. Nahraďte hodnotu `ida:EditProfilePolicyId` za `b2c_1_profileediting1`. Nahraďte hodnotu `ida:ResetPasswordPolicyId` za `b2c_1_passwordreset1`.

## <a name="run-the-sample"></a>Spuštění ukázky

1. V Průzkumníku řešení klikněte pravým tlačítkem myši **TaskWebApp** projektu a pak klikněte na tlačítko **nastavit jako spouštěný projekt**.
2. Stisknutím klávesy **F5**. Spustí se výchozí prohlížeč a otevře se adresa místního webu `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

1. Klikněte na tlačítko **registrace / přihlášení** se zaregistrujte jako uživatel aplikace. **B2c_1_signupsignin1** toku uživatele se používá.
2. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Protože ještě nemáte účet, vyberte **zaregistrujte se hned teď**. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace shromažďuje také heslo uživatele a požadované atributy definované v toku uživatele.
3. Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů. 

    ![Pracovní postup registrace](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. Kliknutím na **Create** (Vytvořit) vytvořte místní účet v tenantovi Azure AD B2C.

Teď uživatel může pomocí e-mailová adresa se přihlásit a používat webovou aplikaci.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky aplikace k používání aplikace
> * Registrace pomocí tok uživatele.

> [!div class="nextstepaction"]
> [Kurz: Použití Azure Active Directory B2C k ochraně webového rozhraní API ASP.NET](active-directory-b2c-tutorials-web-api.md)
