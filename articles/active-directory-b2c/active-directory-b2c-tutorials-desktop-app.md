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
ms.openlocfilehash: 9a3c11c7303d467a1a993c49d983131639683239
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064880"
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

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikace**a pak vyberte **Přidat**.
5. Zadejte název aplikace. Například *nativeapp1*.
6. Pro **Zahrnout webovou aplikaci nebo webové rozhraní API**vyberte **ne**.
7. V případě **Zahrnout nativního klienta**vyberte **Ano**.
8. Pro **identifikátor URI přesměrování**zadejte platný identifikátor URI přesměrování s vlastním schématem. Pro výběr identifikátoru URI přesměrování existují dva důležité informace:

    - **Jedinečné** – schéma identifikátoru URI pro přesměrování by mělo být pro každou aplikaci jedinečné. V příkladu `com.onmicrosoft.contoso.appname://redirect/path` `com.onmicrosoft.contoso.appname` je schéma. Tento vzor by měl následovat. Pokud dvě aplikace sdílejí stejné schéma, uživateli je dána možnost zvolit aplikaci. Pokud uživatel vytvoří nesprávnou volbu, přihlášení se nezdařilo.
    - **Dokončit** – identifikátor URI pro přesměrování musí mít schéma a cestu. Cesta musí po doméně obsahovat alespoň jedno lomítko. Například `//contoso/` funguje a `//contoso` neúspěch. Ujistěte se, že identifikátor URI pro přesměrování neobsahuje speciální znaky, například podtržítka.

9. Klikněte na možnost **Vytvořit**.
10. Na stránce Vlastnosti Poznamenejte ID aplikace, které použijete při konfiguraci ukázky.

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

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání nativní klientské aplikace
> * Konfigurace ukázky pro použití aplikace
> * Registrace pomocí toku uživatele

> [!div class="nextstepaction"]
> [Kurz: Udělení přístupu k webovému rozhraní API Node. js z desktopové aplikace pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
