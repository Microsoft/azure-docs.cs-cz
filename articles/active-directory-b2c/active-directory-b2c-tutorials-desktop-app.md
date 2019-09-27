---
title: Kurz – povolení ověřování v nativní klientské aplikaci – Azure Active Directory B2C | Microsoft Docs
description: Kurz týkající se použití Azure Active Directory B2C k poskytnutí přihlášení uživatele pro desktopovou aplikaci .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3740a032db6ca9fd0fb88ce348610684d9f895bc
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326333"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>Kurz: Povolení ověřování v nativní klientské aplikaci pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak používat Azure Active Directory B2C (Azure AD B2C) k přihlašování a registraci uživatelů v desktopové aplikaci Windows Presentation Foundation (WPF). Azure AD B2C umožňuje vašim aplikacím ověřovat účty v sociálních sítích, podnikové účty a účty Azure Active Directory pomocí protokolů Open Standard.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání nativní klientské aplikace
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- [Vytvořte toky uživatelů](tutorial-create-user-flows.md) a povolte ve své aplikaci uživatelské prostředí.
- Nainstalujte [Visual Studio 2019](https://www.visualstudio.com/downloads/) s pracovními procesy pro vývoj desktopových aplikací pro **.NET** a **ASP.NET a web** .

## <a name="add-the-native-client-application"></a>Přidání nativní klientské aplikace

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Poznamenejte si **ID aplikace** pro použití v pozdějším kroku.

## <a name="configure-the-sample"></a>Konfigurace ukázky

V tomto kurzu nakonfigurujete ukázku, kterou si můžete stáhnout z GitHubu. Ukázková desktopová aplikace WPF ukazuje registraci, přihlašování a volání chráněného webového rozhraní API v Azure AD B2C. [Stáhněte si soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [projděte si úložiště](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Pokud chcete změnit nastavení aplikace, nahraďte `<your-tenant-name>` název názvem tenanta a nahraďte`<application-ID`> číslem ID aplikace, kterou jste si poznamenali.

1. Otevřete řešení `active-directory-b2c-wpf` v sadě Visual Studio.
2. V projektu `active-directory-b2c-wpf` otevřete soubor **App.xaml.cs** a proveďte následující aktualizace:

    ```csharp
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. Aktualizujte proměnnou **PolicySignUpSignIn** s názvem toku uživatele, který jste vytvořili.

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Spuštění ukázky

Stisknutím klávesy **F5** Sestavte a spusťte ukázku.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

1. Pokud se chcete zaregistrovat jako uživatel, klikněte na **Přihlásit** se. Používá se tok uživatele **B2C_1_signupsignin1** .
2. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Protože ještě nemáte účet, klikněte na odkaz **Sign up now** (Zaregistrovat se).
3. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace také shromažďuje heslo uživatele a požadované atributy definované v toku uživatele.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů.

    ![Přihlašovací stránka zobrazená jako součást pracovního postupu přihlášení/přihlášení](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

4. Kliknutím na **Create** (Vytvořit) vytvořte místní účet v tenantovi Azure AD B2C.

Teď se uživatel může přihlásit pomocí své e-mailové adresy a používat desktopovou aplikaci.

> [!NOTE]
> Pokud klikněte na tlačítko **Call API** (Zavolat rozhraní API), zobrazí se chyba Neautorizováno. Tato chyba se zobrazí, protože se pokoušíte o přístup k prostředku z ukázkového tenanta. Vzhledem k tomu, že je váš přístupový token platný pouze pro vašeho tenanta Azure AD, je toto volání rozhraní API neautorizované. Pokračujte k dalšímu kurzu, kde pro svého tenanta vytvoříte chráněné webové rozhraní API.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání nativní klientské aplikace
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

> [!div class="nextstepaction"]
> [Kurz: Udělení přístupu k webovému rozhraní API Node. js z desktopové aplikace pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
