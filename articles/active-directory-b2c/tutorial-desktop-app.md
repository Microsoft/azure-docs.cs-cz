---
title: 'Kurz: Ověření uživatelů v nativní klientské aplikaci'
titleSuffix: Azure AD B2C
description: Návod, jak pomocí služby Azure Active Directory B2C poskytnout přihlášení uživatele pro desktopovou aplikaci .NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06d27c3a3daa4702653a2063d0ac70fd094e2d74
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78186195"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Kurz: Ověřování uživatelů v nativním desktopovém klientovi pomocí služby Azure Active Directory B2C

Tento kurz ukazuje, jak používat Azure Active Directory B2C (Azure AD B2C) k přihlášení a registraci uživatelů v desktopové aplikaci Windows Presentation Foundation (WPF). Azure AD B2C umožňuje vašim aplikacím ověřování na účty na sociálních sítích, podnikových účtech a účtech Azure Active Directory pomocí otevřených standardních protokolů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání nativní klientské aplikace
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- [Vytvořte toky uživatelů,](tutorial-create-user-flows.md) které umožní uživatelské prostředí ve vaší aplikaci.
- Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/downloads/) s **vývojem desktopů .NET** a **úlohami ASP.NET a vývoje webu.**

## <a name="add-the-native-client-application"></a>Přidání nativní klientské aplikace

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Zaznamenejte **ID aplikace (klienta)** pro pozdější krok.

## <a name="configure-the-sample"></a>Konfigurace ukázky

V tomto kurzu nakonfigurujete ukázku, kterou můžete stáhnout z GitHubu. Ukázková desktopová aplikace WPF demonstruje registraci, přihlášení a může volat chráněné webové rozhraní API v Azure AD B2C. [Stáhněte si soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip), [projděte si úložiště](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Aktualizace aplikace pro práci s klientem Azure AD B2C a vyvolání jejích uživatelských toků namísto toků ve výchozím ukázkovém tenantovi:

1. Otevřete řešení **active-directory-b2c-wpf** (`active-directory-b2c-wpf.sln`) v sadě Visual Studio.
2. V projektu **active-directory-b2c-wpf** otevřete soubor *App.xaml.cs* a vyhledejte následující definice proměnných. Nahraďte `{your-tenant-name}` název klienta Azure AD B2C a `{application-ID}` ID aplikace, které jste zaznamenali dříve.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. Aktualizujte proměnné názvu zásady názvy uživatelských toků, které jste vytvořili jako součást požadavků. Například:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>Spuštění ukázky

Stisknutím **klávesy F5** vytvořte a spusťte ukázku.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

1. Chcete-li se zaregistrovat jako uživatel, **vyberte Přihlásit** se. To používá **B2C_1_signupsignin1** tok uživatele.
2. Azure AD B2C představuje přihlašovací stránku s odkazem **Zaregistrovat se teď.** Vzhledem k tomu, že ještě nemáte účet, vyberte odkaz **Zaregistrovat nyní.**
3. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace také shromažďuje heslo uživatele a požadované atributy definované v toku uživatele.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů.

    ![Stránka registrace zobrazená jako součást pracovního postupu přihlášení/registrace](./media/tutorial-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. Vyberte **Vytvořit,** chcete-li vytvořit místní účet v tenantovi Azure AD B2C.

Uživatel může nyní použít svou e-mailovou adresu k přihlášení a používání desktopové aplikace. Po úspěšné registraci nebo přihlášení se zobrazí podrobnosti tokenu v dolním podokně aplikace WPF.

![Podrobnosti tokenu zobrazené v dolním podokně desktopové aplikace WPF](./media/tutorial-desktop-app/desktop-app-01-post-signin.png)

Pokud vyberete tlačítko **Rozhraní API volání,** zobrazí se **chybová zpráva.** K chybě dojde, protože v aktuálním stavu se aplikace pokouší o přístup `fabrikamb2c.onmicrosoft.com`k rozhraní API chráněnému ukázkovým tenantem . Vzhledem k tomu, že váš přístupový token je platný jenom pro vašeho klienta Azure AD B2C, volání rozhraní API je proto neoprávněné.

Pokračujte k dalšímu kurzu k registraci chráněného webového rozhraní API ve vlastním tenantovi a povolte funkci **rozhraní API pro volání.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání nativní klientské aplikace
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

Dále, chcete-li povolit funkci tlačítka **rozhraní API volání,** udělte desktopové aplikaci WPF přístup k webovému rozhraní API registrovanému ve vašem vlastním tenantovi Azure AD B2C:

> [!div class="nextstepaction"]
> [Kurz: Udělení přístupu k webovému rozhraní API Node.js z aplikace klasické pracovní plochy >](tutorial-desktop-app-webapi.md)
