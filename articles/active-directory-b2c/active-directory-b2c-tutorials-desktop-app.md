---
title: Kurz – povolení ověřování v nativní klientské aplikace – Azure Active Directory B2C | Dokumentace Microsoftu
description: Kurz týkající se použití Azure Active Directory B2C k zajištění přihlašování uživatelů pro desktopové aplikace .NET.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: a1842859723173412df2053a242ebe9ca4cf7f32
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754021"
---
# <a name="tutorial-enable-authentication-in-a-native-client-application-using-azure-active-directory-b2c"></a>Kurz: Povolení ověřování v nativní klientské aplikace pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak použít Azure Active Directory (Azure AD) B2C k registraci a přihlašování uživatelů v desktopové aplikaci WPF (Windows Presentation Foundation). Azure AD B2C umožňuje vaší aplikace k ověřování účtů na sociálních sítích, podnikových účtů a účtů služby Azure Active Directory pomocí protokolů s otevřenými standardy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat nativní klientskou aplikaci
> * Konfigurace ukázky aplikace k používání aplikace
> * Registrace pomocí tok uživatele.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

- [Vytvořit toky uživatelů](tutorial-create-user-flows.md) umožňující činnosti koncových uživatelů ve vaší aplikaci. 
- Nainstalujte sadu [Visual Studio 2017](https://www.visualstudio.com/downloads/) se sadami funkcí **Vývoj desktopových aplikací .NET** a **Vývoj pro ASP.NET a web**.

## <a name="add-the-native-client-application"></a>Přidat nativní klientskou aplikaci

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikací**a pak vyberte **přidat**.
5. Zadejte název aplikace. Například *nativeapp1*.
6. Pro **zahrnout webovou aplikaci / webové rozhraní API**vyberte **ne**.
7. Pro **zahrnout nativního klienta**vyberte **Ano**.
8. Pro **identifikátor URI pro přesměrování**, zadejte na platný identifikátor URI přesměrování s vlastním schématem. Existují dva důležité aspekty při výběru identifikátoru URI přesměrování:

    - **Jedinečné** – schéma identifikátoru URI přesměrování by mělo být pro každou aplikaci jedinečné. V příkladu `com.onmicrosoft.contoso.appname://redirect/path`, `com.onmicrosoft.contoso.appname` schéma com.onmicrosoft.contoso.appname. Tento model má následovat. Pokud dvě aplikace sdílejí stejné schéma, uživateli je možnost volby vybrat aplikace. Pokud uživatel použije nesprávnou volbu, přihlášení selže.
    - **Kompletní** – identifikátor URI pro přesměrování musí mít schéma a cestu. Cesta musí obsahovat za doménou alespoň jedno lomítko. Například `//contoso/` funguje a `//contoso` selže. Ujistěte se, že identifikátor URI přesměrování neobsahuje speciální znaky jako podtržítka.

9. Klikněte na možnost **Vytvořit**.
10. Na stránce Vlastnosti zaznamenejte ID aplikace, které budete používat při konfiguraci ukázky.

## <a name="configure-the-sample"></a>Konfigurace ukázky aplikace

V tomto kurzu nakonfigurujete vzorku, který si můžete stáhnout z Githubu. Ukázková desktopová aplikace WPF předvádí registrace, přihlašování a volá chráněné webové rozhraní API v Azure AD B2C. [Stáhněte si soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [projděte si úložiště](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) nebo naklonujte ukázku z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Chcete-li změnit nastavení aplikace, nahraďte `<your-tenant-name>` s názvem tenanta a nahradit`<application-ID`> zadejte ID aplikace, které jste si poznamenali.

1. Otevřete řešení `active-directory-b2c-wpf` v sadě Visual Studio.
2. V projektu `active-directory-b2c-wpf` otevřete soubor **App.xaml.cs** a proveďte následující aktualizace:

    ```C#
    private static string Tenant = "<your-tenant-name>.onmicrosoft.com";
    private static string ClientId = "<application-ID>";
    ```

3. Aktualizace **PolicySignUpSignIn** proměnné s názvem, který jste vytvořili tok uživatele.

    ```C#
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Spuštění ukázky

Stisknutím klávesy **F5** k sestavení a spuštění ukázky.

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

1. Klikněte na tlačítko **Sign In** se zaregistrujte jako uživatel. Tady se používá **B2C_1_signupsignin1** tok uživatele.
2. Azure AD B2C zobrazí přihlašovací stránku s odkazem na registraci. Protože ještě nemáte účet, klikněte na odkaz **Sign up now** (Zaregistrovat se). 
3. Pracovní postup registrace zobrazí stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace shromažďuje také heslo uživatele a požadované atributy definované v toku uživatele.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů. 

    ![Pracovní postup registrace](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Kliknutím na **Create** (Vytvořit) vytvořte místní účet v tenantovi Azure AD B2C.

Teď se uživatel může přihlásit pomocí své e-mailové adresy a používat desktopovou aplikaci.

> [!NOTE]
> Pokud klikněte na tlačítko **Call API** (Zavolat rozhraní API), zobrazí se chyba Neautorizováno. Tato chyba se zobrazí, protože se pokoušíte o přístup k prostředku z ukázkového tenanta. Vzhledem k tomu, že je váš přístupový token platný pouze pro vašeho tenanta Azure AD, je toto volání rozhraní API neautorizované. Pokračujte k dalšímu kurzu, kde pro svého tenanta vytvoříte chráněné webové rozhraní API.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidat nativní klientskou aplikaci
> * Konfigurace ukázky aplikace k používání aplikace
> * Registrace pomocí tok uživatele.

> [!div class="nextstepaction"]
> [Kurz: Poskytnutí přístupu k webovému rozhraní API Node.js z desktopové aplikace pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
