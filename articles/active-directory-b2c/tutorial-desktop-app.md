---
title: 'Kurz: ověřování uživatelů v nativní klientské aplikaci'
titleSuffix: Azure AD B2C
description: Kurz týkající se použití Azure Active Directory B2C k poskytnutí přihlášení uživatele pro desktopovou aplikaci .NET.
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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "78186195"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Kurz: ověřování uživatelů v nativním klientském počítači pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak používat Azure Active Directory B2C (Azure AD B2C) k přihlašování a registraci uživatelů v desktopové aplikaci Windows Presentation Foundation (WPF). Azure AD B2C umožňuje vašim aplikacím ověřovat účty v sociálních sítích, podnikové účty a účty Azure Active Directory pomocí protokolů Open Standard.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání nativní klientské aplikace
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

- [Vytvořte toky uživatelů](tutorial-create-user-flows.md) a povolte ve své aplikaci uživatelské prostředí.
- Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/downloads/) s pracovními procesy pro vývoj **desktopových** aplikací pro .NET a **ASP.NET a web** .

## <a name="add-the-native-client-application"></a>Přidání nativní klientské aplikace

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Poznamenejte si **ID aplikace (klienta)** pro použití v pozdějším kroku.

## <a name="configure-the-sample"></a>Konfigurace ukázky

V tomto kurzu nakonfigurujete ukázku, kterou si můžete stáhnout z GitHubu. Ukázková desktopová aplikace WPF ukazuje registraci, přihlášení a může volat chráněné webové rozhraní API v Azure AD B2C. [Stáhněte si soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip), [projděte si úložiště](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Chcete-li aktualizovat aplikaci pro práci s vaším klientem Azure AD B2C a vyvolat jeho toky uživatelů místo těch, kteří jsou ve výchozím ukázkovém tenantovi:

1. Otevřete řešení **Active-Directory-B2C-WPF** ( `active-directory-b2c-wpf.sln` ) v aplikaci Visual Studio.
2. V projektu **Active-Directory-B2C-WPF** otevřete soubor *App. XAML. cs* a vyhledejte následující definice proměnných. Nahraďte `{your-tenant-name}` názvem klienta Azure AD B2C a `{application-ID}` ID aplikace, které jste si poznamenali dříve.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. Aktualizujte proměnné názvu zásad pomocí názvů toků uživatelů, které jste vytvořili v rámci požadavků. Například:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>Spuštění ukázky

Stisknutím klávesy **F5** Sestavte a spusťte ukázku.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

1. Vyberte **Přihlásit** se a zaregistrujte se jako uživatel. Používá tok uživatele **B2C_1_signupsignin1** .
2. Azure AD B2C zobrazí přihlašovací stránku s odkazem na **registraci** . Vzhledem k tomu, že ještě nemáte účet, vyberte odkaz **Registrovat nyní** .
3. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace také shromažďuje heslo uživatele a požadované atributy definované v toku uživatele.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů.

    ![Přihlašovací stránka zobrazená jako součást pracovního postupu přihlášení/přihlášení](./media/tutorial-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. Vyberte **vytvořit** k vytvoření místního účtu v Azure AD B2C tenantovi.

Uživatel teď může pomocí své e-mailové adresy přihlásit se a použít desktopovou aplikaci. Po úspěšné registraci nebo přihlášení se podrobnosti o tokenu zobrazí v dolním podokně aplikace WPF.

![Podrobnosti tokenu zobrazené v dolním podokně desktopové aplikace WPF](./media/tutorial-desktop-app/desktop-app-01-post-signin.png)

Pokud vyberete tlačítko **rozhraní API pro volání** , zobrazí se **chybová zpráva** . Narazíte na chybu, protože v jejím aktuálním stavu se aplikace pokouší o přístup k rozhraní API chráněného demonstračním klientem `fabrikamb2c.onmicrosoft.com` . Vzhledem k tomu, že váš přístupový token je platný jenom pro vašeho tenanta Azure AD B2C, volání rozhraní API je proto neautorizované.

Přejděte k dalšímu kurzu, který zaregistruje chráněné webové rozhraní API ve vašem tenantovi a povolí funkci **rozhraní API pro volání** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání nativní klientské aplikace
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

Pokud chcete povolit funkci tlačítka **rozhraní API volání** , udělte desktopové aplikaci WPF přístup k webovému rozhraní API zaregistrovanému ve vašem tenantovi Azure AD B2C:

> [!div class="nextstepaction"]
> [Kurz: poskytnutí přístupu k webovému rozhraní API Node.js z desktopové aplikace >](tutorial-desktop-app-webapi.md)
