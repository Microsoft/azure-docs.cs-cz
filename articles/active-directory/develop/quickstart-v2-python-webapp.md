---
title: 'Rychlý Start: přidání přihlášení pomocí Microsoftu do webové aplikace v Pythonu | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak se webová aplikace v Pythonu může přihlašovat uživatelům, získat přístupový token z platformy Microsoft identity a volat rozhraní Microsoft Graph API.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 383f7f37e93b4705419ba1f93f509c86eaab192b
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030633"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Rychlý Start: Přidání přihlašování do webové aplikace v Pythonu pomocí Microsoftu

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která ukazuje, jak se webová aplikace v Pythonu může přihlašovat uživatelům a získat přístupový token pro volání rozhraní Microsoft Graph API. Uživatelé s osobním účtem Microsoft nebo účtem v jakékoli Azure Active Directory (Azure AD) se můžou k aplikaci přihlásit.

Podívejte [se, jak ukázka funguje](#how-the-sample-works) pro ilustraci.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) nebo [Python 3 +](https://www.python.org/downloads/release/python-364/)
- [Baňka](http://flask.pocoo.org/), [baňka – relace](https://pypi.org/project/Flask-Session/), [žádosti](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
>
> Máte dvě možnosti, jak spustit aplikaci pro rychlý Start: Express (možnost 1) a ruční (možnost 2).
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu
>
> 1. Přejít na [Registrace aplikací Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs).
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
>
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
> 1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
> 1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
> 1. Zadejte **název** vaší aplikace, například `python-webapp` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
> 1. V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
> 1. Vyberte **Zaregistrovat**.
> 1. Na stránce **Přehled** aplikace si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.
> 1. V části **Spravovat** vyberte **ověřování**.
> 1. Vyberte **Přidat**  >  **Web** platformy.
> 1. Přidejte `http://localhost:5000/getAToken` jako **identifikátory URI přesměrování**.
> 1. Vyberte **Konfigurovat**.
> 1. V části **Spravovat** vyberte **certifikáty & tajných**  kódů a v části **tajné klíče klienta** vyberte **nový tajný klíč klienta**.
> 1. Zadejte popis klíče (například pro tajný klíč aplikace), ponechte výchozí hodnotu vypršení platnosti a vyberte **Přidat**.
> 1. Poznamenejte si **hodnotu** **tajného klíče klienta** pro pozdější použití.
> 1. V části **Spravovat** vyberte **oprávnění rozhraní API**  >  **Přidat oprávnění**.
>1.  Ujistěte se, že je vybraná karta **rozhraní API Microsoftu** .
> 1. V části *běžně používaná rozhraní Microsoft API* vyberte **Microsoft Graph**.
> 1. V části **delegovaná oprávnění** zkontrolujte, že jsou zaškrtnutá správná oprávnění: **User. ReadBasic. All**. V případě potřeby použijte vyhledávací pole.
> 1. Klikněte na tlačítko **Přidat oprávnění** .
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1: Nakonfigurujte si aplikaci na portálu Azure Portal
>
> Ukázku kódu pro tento rychlý Start, který funguje, je třeba:
>
> 1. Přidejte adresu URL odpovědi jako `http://localhost:5000/getAToken` .
> 1. Vytvořte tajný klíč klienta.
> 1. Přidat uživatele. ReadBasic rozhraní API Microsoft Graph. všechna delegovaná oprávnění.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Provést tyto změny pro mě]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-aspnet-webapp/green-check.png) Vaše aplikace je nakonfigurovaná s tímto atributem

#### <a name="step-2-download-your-project"></a>Krok 2: Stáhněte si projekt
> [!div renderon="docs"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Stáhněte si projekt a extrahujte soubor zip do místní složky blíže ke kořenové složce – například **C:\Azure-Samples**
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Krok 3: Konfigurace aplikace
>
> 1. Extrahujte soubor ZIP do místní složky bližší ke kořenové složce, třeba **C:\Azure-Samples**.
> 1. Pokud používáte integrované vývojové prostředí, otevřete ukázku v oblíbeném INTEGROVANÉm vývojovém prostředí (volitelné).
> 1. Otevřete soubor **app_config. py** , který najdete v kořenové složce, a nahraďte ho následujícím fragmentem kódu:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Kde:
>
> - `Enter_the_Application_Id_here` je ID aplikace, kterou jste zaregistrovali.
> - `Enter_the_Client_Secret_Here` – je **tajný klíč klienta** , který jste vytvořili v části **certifikáty & tajných**  kódů pro aplikaci, kterou jste zaregistrovali.
> - `Enter_the_Tenant_Name_Here` – je hodnota **ID adresáře** aplikace, kterou jste zaregistrovali.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Krok 3: spuštění ukázky kódu

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Krok 4: spuštění ukázky kódu

1. Pro správu relací na straně serveru a požadavky pomocí PIP budete muset nainstalovat MSAL Python Library, Flask-Sessions, a to následujícím způsobem:

    ```Shell
    pip install -r requirements.txt
    ```

2. Spusťte app.py z prostředí nebo příkazového řádku:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Tato aplikace rychlý Start používá k identifikaci jako důvěrného klienta tajný klíč klienta. Vzhledem k tomu, že se tajný klíč klienta přidá do souborů projektu jako prostý text, doporučuje se místo toho použít certifikát namísto tajného klíče klienta před tím, než aplikaci vyberou jako produkční aplikaci. Další informace o použití certifikátu najdete v [těchto pokynech](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Další informace

### <a name="how-the-sample-works"></a>Jak ukázka funguje
![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Získání MSAL
MSAL je knihovna používaná k přihlašování uživatelů a žádosti o tokeny používané pro přístup k rozhraní API chráněnému platformou Microsoft identity.
MSAL Python můžete do své aplikace přidat pomocí PIP.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL
Odkaz na MSAL Python můžete přidat přidáním následujícího kódu do horní části souboru, kde budete používat MSAL:

```Python
import msal
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o webových aplikacích, které přihlásí uživatele v naší řadě scénářů s více částmi.

> [!div class="nextstepaction"]
> [Scénář: webová aplikace, která se přihlásí uživatelům](scenario-web-app-sign-user-overview.md)
