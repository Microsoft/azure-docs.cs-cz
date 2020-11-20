---
title: 'Kurz: povolení ověřování ve webové aplikaci Pythonu'
titleSuffix: Azure AD B2C
description: V tomto kurzu se naučíte používat Azure Active Directory B2C k poskytnutí přihlašovacích údajů uživatele pro webovou aplikaci v Pythonu.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 06/12/2020
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.custom: devx-track-python
ms.openlocfilehash: b4455f21ae7243ab7a15e8d746d6674289f9fdb5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952994"
---
# <a name="tutorial-enable-authentication-in-a-python-web-application-with-azure-ad-b2c"></a>Kurz: povolení ověřování ve webové aplikaci Pythonu pomocí Azure AD B2C

V tomto kurzu se dozvíte, jak používat Azure Active Directory B2C (Azure AD B2C) k registraci a přihlašování uživatelů ve webové aplikaci v baňce Pythonu.

V tomto kurzu:

> [!div class="checklist"]
> * Přidání adresy URL odpovědi do aplikace zaregistrované ve vašem tenantovi Azure AD B2C
> * Stažení ukázky kódu z GitHubu
> * Úprava kódu ukázkové aplikace pro spolupráci s vaším klientem
> * Zaregistrujte se pomocí uživatelského toku pro registraci a přihlašování.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat v krocích v tomto kurzu, budete potřebovat následující Azure AD B2C prostředky:

* [Tenant Azure AD B2C](tutorial-create-tenant.md)
* [Aplikace zaregistrovaná](tutorial-register-applications.md) ve vašem tenantovi a její *ID aplikace (klienta)* a *tajný kód klienta*
* [Toky uživatelů vytvořené](tutorial-create-user-flows.md) ve vašem tenantovi

V místním vývojovém prostředí budete navíc potřebovat následující:

* [Visual Studio Code](https://code.visualstudio.com/) nebo jiný Editor kódu
* [Python](https://nodejs.org/en/download/) 2.7 + nebo 3 +

## <a name="add-a-redirect-uri"></a>Přidat identifikátor URI pro přesměrování

V druhém kurzu, který jste dokončili v rámci požadavků, jste zaregistrovali webovou aplikaci v Azure AD B2C. Pokud chcete povolit komunikaci s ukázkami kódu v tomto kurzu, přidejte do registrace aplikace adresu URL odpovědi (označuje se taky jako identifikátor URI přesměrování).

Pokud chcete aktualizovat aplikaci ve vašem tenantovi Azure AD B2C, můžete využít nové jednotné prostředí pro **Registrace aplikací** nebo naše starší verze  **aplikací (zastaralé)** . [Další informace o novém prostředí](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registrace aplikací](#tab/app-reg-ga/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací**, vyberte kartu **vlastněné aplikace** a pak vyberte aplikaci *WebApp1* .
1. V části **Spravovat** vyberte **ověřování**.
1. V části **Web** vyberte odkaz **Přidat identifikátor URI** a potom `http://localhost:5000/getAToken` do textového pole zadejte.
1. Vyberte **Uložit**.

#### <a name="applications-legacy"></a>[Aplikace (starší verze)](#tab/applications-legacy/)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace (starší verze)** a pak vyberte aplikaci *WebApp1* .
1. V části **Adresa URL odpovědi** přidejte `http://localhost:5000/getAToken` .
1. Vyberte **Uložit**.
* * *

## <a name="get-the-sample-code"></a>Získání ukázkového kódu

V tomto kurzu nakonfigurujete ukázku kódu, který stáhnete z GitHubu, abyste mohli pracovat s vaším klientem B2C. Ukázka ukazuje, jak může webová aplikace v Pythonu použít Azure AD B2C pro registraci a přihlášení uživatelů.

[Stáhněte si. Archiv ZIP](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip) nebo naklonujte [úložiště ukázek kódu](https://github.com/Azure-Samples/ms-identity-python-webapp) z GitHubu.

```console
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

## <a name="update-the-sample"></a>Aktualizace ukázky

Po získání ukázky nakonfigurujte aplikaci tak, aby používala klienta Azure AD B2C, registraci aplikace a toky uživatelů.

V kořenovém adresáři projektu:

1. Přejmenujte soubor *app_config. py* na *app_config. py. old* .
1. Přejmenujte *app_config_b2c. py* na *app_config. py*

Aktualizujte nově přejmenované *app_config. py* s hodnotami pro vaši Azure AD B2C klienta a registraci aplikace, kterou jste vytvořili jako součást požadavků.

1. V editoru otevřete soubor *app_config. py* .
1. Aktualizujte `b2c_tenant` hodnotu názvem vašeho tenanta Azure AD B2C, například *contosob2c*.
1. Aktualizujte všechny `*_user_flow` hodnoty tak, aby odpovídaly názvům uživatelských toků, které jste vytvořili v rámci požadavků.
1. Aktualizujte `CLIENT_ID` hodnotu pomocí **ID aplikace (klienta)** webové aplikace, kterou jste zaregistrovali v rámci požadavků.
1. Aktualizujte `CLIENT_SECRET` hodnotu hodnotou **tajného klíče klienta** , který jste vytvořili v části požadavky. Pro zvýšení zabezpečení zvažte, že se místo toho uloží do **proměnné prostředí** , jak se doporučuje v komentářích.

Horní část *app_config. py* by teď měla vypadat podobně jako následující fragment kódu:

```python
import os

b2c_tenant = "contosob2c"
signupsignin_user_flow = "B2C_1_signupsignin1"
editprofile_user_flow = "B2C_1_profileediting1"
resetpassword_user_flow = "B2C_1_passwordreset1"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "22222222-2222-2222-2222-222222222222" # Placeholder - for use ONLY during testing.
# In a production app, we recommend you use a more secure method of storing your secret,
# like Azure Key Vault. Or, use an environment variable as described in Flask's documentation:
# https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
# CLIENT_SECRET = os.getenv("CLIENT_SECRET")
# if not CLIENT_SECRET:
#     raise ValueError("Need to define CLIENT_SECRET environment variable")
```

> [!WARNING]
> Jak je uvedeno v komentářích fragmentů kódu, doporučujeme **Neukládat tajné klíče do prostého textu** v kódu aplikace. Proměnná pevně zakódované se používá v ukázce kódu *jenom pro pohodlí*. Zvažte použití proměnné prostředí nebo úložiště tajného kódu, jako Azure Key Vault.

## <a name="run-the-sample"></a>Spuštění ukázky

1. V konzole nebo terminálu přejděte do adresáře, který obsahuje ukázku. Například:

    ```console
    cd ms-identity-python-webapp
    ```
1. Spuštěním následujících příkazů nainstalujte požadované balíčky z PyPi a spusťte webovou aplikaci na místním počítači:

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    V okně konzoly se zobrazí číslo portu místně spuštěné aplikace:

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on http://localhost:5000/ (Press CTRL+C to quit)
    ```

1. Přejděte k `http://localhost:5000` zobrazení webové aplikace spuštěné v místním počítači.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-01.png" alt-text="Webový prohlížeč, na kterém běží webová aplikace v Pythonu v místním prostředí":::

### <a name="sign-up-using-an-email-address"></a>Registrace pomocí e-mailové adresy

Tato ukázková aplikace podporuje registraci, přihlášení a resetování hesla. V tomto kurzu se přihlásíte pomocí e-mailové adresy.

1. Vyberte možnost **Přihlásit** se a zahajte tok *B2C_1_signupsignin1ho* uživatele, který jste zadali v předchozím kroku.
1. Azure AD B2C zobrazí přihlašovací stránku, která obsahuje odkaz na registraci. Vzhledem k tomu, že ještě nemáte účet, vyberte odkaz **Registrovat nyní** .
1. Pracovní postup registrace představuje stránku pro shromáždění a ověření identity uživatele pomocí e-mailové adresy. Pracovní postup registrace také shromažďuje heslo uživatele a požadované atributy definované v toku uživatele.

    Použijte platnou e-mailovou adresu a proveďte ověření pomocí ověřovacího kódu. Nastavte heslo. Zadejte hodnoty požadovaných atributů.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-02.png" alt-text="Stránka pro registraci zobrazená pomocí Azure AD B2C toku uživatele":::

1. Vyberte **vytvořit** k vytvoření místního účtu v adresáři Azure AD B2C.

Když vyberete **vytvořit**, aplikace zobrazí jméno přihlášeného uživatele.

:::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-03.png" alt-text="Webový prohlížeč zobrazující webovou aplikaci v Pythonu s přihlášeným uživatelem":::

Pokud chcete otestovat přihlášení, vyberte odkaz pro **odhlášení** a pak vyberte **Přihlásit** se a přihlaste se pomocí e-mailové adresy a hesla, které jste zadali při registraci.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali webovou aplikaci Pythonu pro práci s uživatelským tokem v tenantovi Azure AD B2C, abyste mohli poskytovat možnost registrace a přihlášení. Dokončili jste tyto kroky:

> [!div class="checklist"]
> * Do aplikace zaregistrované ve vašem Azure AD B2C tenantovi se přidala adresa URL odpovědi.
> * Stáhli ukázku kódu z GitHubu
> * Upravení kódu ukázkové aplikace pro spolupráci s vaším klientem
> * Zaregistrovali jste se pomocí uživatelského toku pro registraci a přihlašování.

V dalším kroku se dozvíte, jak přizpůsobit uživatelské rozhraní stránek toku uživatele zobrazených uživatelům Azure AD B2C:

> [!div class="nextstepaction"]
> [Kurz: přizpůsobení rozhraní uživatelského prostředí v Azure AD B2C >](tutorial-customize-ui.md)