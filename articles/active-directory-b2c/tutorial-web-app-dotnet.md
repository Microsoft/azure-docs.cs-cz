---
title: 'Kurz: Povolení ověřování ve webové aplikaci'
titleSuffix: Azure AD B2C
description: Kurz týkající se použití Azure Active Directory B2C k zajištění přihlašování uživatelů ve webové aplikaci ASP.NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e4b56f18bf8a2ed1c22b00b8a57efdbf06eb7fa2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183311"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Kurz: Povolení ověřování ve webové aplikaci pomocí služby Azure Active Directory B2C

Tento kurz ukazuje, jak používat Azure Active Directory B2C (Azure AD B2C) k přihlášení a registraci uživatelů ve webové aplikaci ASP.NET. Azure AD B2C umožňuje vašim aplikacím ověřování na účty na sociálních sítích, podnikových účtech a účtech Azure Active Directory pomocí otevřených standardních protokolů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* [Vytvořte toky uživatelů,](tutorial-create-user-flows.md) které umožní uživatelské prostředí ve vaší aplikaci.
* Nainstalujte [Sivisual Studio 2019](https://www.visualstudio.com/downloads/) s **ASP.NET a zatížením vývoje webu.**

## <a name="update-the-application-registration"></a>Aktualizace registrace aplikace

V kurzu, který jste dokončili jako součást předpokladů, jste zaregistrovali webovou aplikaci v Azure AD B2C. Chcete-li povolit komunikaci s ukázkou v tomto kurzu, je třeba přidat identifikátor URI přesměrování a vytvořit tajný klíč klienta (klíč) pro registrovanou aplikaci.

### <a name="add-a-redirect-uri-reply-url"></a>Přidání identifikátoru URI přesměrování (adresa URL odpovědi)

K aktualizaci aplikace můžete použít aktuální prostředí **aplikací** nebo naše nové jednotné **registrace aplikací (Preview).** [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Aplikace**a pak vyberte aplikaci *webapp1.*
1. V části Adresa `https://localhost:44316`URL **odpovědi**přidejte .
1. Vyberte **Uložit**.
1. Na stránce vlastností zaznamenejte ID aplikace pro pozdější krok při konfiguraci webové aplikace.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (náhled),** vyberte kartu **Vlastní aplikace** a pak vyberte aplikaci *webapp1.*
1. Vyberte **Ověřování**, pak vyberte **Vyzkoušet nové prostředí** (pokud je zobrazeno).
1. V části **Web**vyberte odkaz `https://localhost:44316`Přidat identifikátor **URI,** zadejte a pak vyberte **Uložit**.
1. Vyberte **Přehled**.
1. Zaznamenejte **ID aplikace (klienta)** pro pozdější krok při konfiguraci webové aplikace.

* * *

### <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Dále vytvořte tajný klíč klienta pro registrovanou webovou aplikaci. Ukázka kódu webové aplikace používá k prokázání své identity při vyžádání tokenů.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>Konfigurace ukázky

V tomto kurzu nakonfigurujete ukázku, kterou můžete stáhnout z GitHubu. Ukázka používá ASP.NET k poskytnutí jednoduchého seznamu úkolů. Ukázka používá [součásti Middleware společnosti Microsoft OWIN](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) nebo naklonujte ukázku z GitHubu. Ujistěte se, že cesta ke složce, do které extrahujete ukázkový soubor, obsahuje méně než 260 znaků.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Následující dva projekty jsou v ukázkovém řešení:

* **TaskWebApp** - Vytvoření a úprava seznamu úkolů. Ukázka používá **tok registrace nebo přihlášení** uživatele k registraci a přihlášení uživatelů.
* **TaskService** - Podporuje vytvoření, čtení, aktualizaci a odstranění funkce seznamu úkolů. Rozhraní API je chráněné Azure AD B2C a voláno TaskWebApp.

Ukázku změníte tak, aby používala aplikaci registrovanou ve vašem tenantovi, která zahrnuje ID aplikace a klíč, který jste dříve zaznamenali. Můžete také nakonfigurovat toky uživatelů, které jste vytvořili. Ukázka definuje hodnoty konfigurace jako nastavení v souboru *Web.config.*

Aktualizujte nastavení v souboru Web.config tak, aby fungovalo s uživatelským tokem:

1. Otevřete řešení **B2C-WebAPI-DotNet** v sadě Visual Studio.
1. V projektu **TaskWebApp** otevřete soubor **Web.config.**
    1. Aktualizujte `ida:Tenant` hodnotu `ida:AadInstance` a s názvem klienta Azure AD B2C, který jste vytvořili. Například nahraďte . `fabrikamb2c` `contoso`
    1. Nahraďte `ida:ClientId` hodnotu ID aplikace, které jste zaznamenali.
    1. Nahraďte hodnotu `ida:ClientSecret` klíčem, který jste si poznamenali. Pokud tajný klíč klienta obsahuje všechny předdefinované entity`<`XML,`>`například menší`&`než ( ),`"`větší než ( ), ampersand ( ), nebo dvojité uvozovky ( ), musíte tyto znaky před přidáním do souboru Web.config unikat pomocí kódování XML.
    1. Nahraďte `ida:SignUpSignInPolicyId` hodnotu . `b2c_1_signupsignin1`
    1. Nahraďte `ida:EditProfilePolicyId` hodnotu . `b2c_1_profileediting1`
    1. Nahraďte `ida:ResetPasswordPolicyId` hodnotu . `b2c_1_passwordreset1`

## <a name="run-the-sample"></a>Spuštění ukázky

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt **TaskWebApp** a potom klikněte na **příkaz Nastavit jako počáteční projekt**.
1. Stiskněte **klávesu F5**. Spustí se výchozí prohlížeč a otevře se adresa místního webu `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

1. Vyberte **Zaregistrovat se / Přihlásit** se, abyste se zaregistrovali jako uživatel aplikace. Používá **se b2c_1_signupsignin1** tok uživatele.
1. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Vzhledem k tomu, že ještě nemáte účet, vyberte **zaregistrovat se nyní**. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace také shromažďuje heslo uživatele a požadované atributy definované v toku uživatele.
1. Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů.

    ![Stránka registrace zobrazená jako součást pracovního postupu přihlášení/registrace](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. Vyberte **Vytvořit,** chcete-li vytvořit místní účet v tenantovi Azure AD B2C.

Uživatel aplikace nyní může použít svou e-mailovou adresu k přihlášení a použití webové aplikace.

Funkce **Seznam úkolů** však nebude fungovat, dokud nedokončíte další kurz v řadě, [kurz: Použijte Azure AD B2C k ochraně ASP.NET webové rozhraní API](tutorial-web-api-dotnet.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Aktualizace aplikace v Azure AD B2C
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

Nyní přejděte k dalšímu kurzu, který umožní funkci **Seznam úkolů** webové aplikace. V něm zaregistrujete aplikaci webového rozhraní API ve vlastním tenantovi Azure AD B2C a pak upravíte ukázku kódu tak, aby používala vašeho tenanta pro ověřování rozhraní API.

> [!div class="nextstepaction"]
> [Kurz: Pomocí Azure Active Directory B2C k ochraně ASP.NET webového rozhraní API >](tutorial-web-api-dotnet.md)
