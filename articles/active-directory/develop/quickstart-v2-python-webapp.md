---
title: Přidání přihlášení se společností Microsoft do webové aplikace Python platformy Microsoft pro identity | Azure
description: Zjistěte, jak implementovat Microsoft Sign-In na Python Web App pomocí OAuth2
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: 0affae56ef6998efe4bb370287ff3688f83f3878
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873952"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Úvodní příručka: Přidání přihlášení s Microsoftem do webové aplikace Pythonu

V tomto rychlém startu se dozvíte, jak integrovat webovou aplikaci Pythonu s platformou identit Microsoftu. Vaše aplikace se přihlásí k uživateli, získá přístupový token pro volání rozhraní Microsoft Graph API a požádá o rozhraní Microsoft Graph API.

Po dokončení příručky bude vaše aplikace přijímat přihlášení k osobním účtům Microsoft (včetně outlook.com, live.com a dalších) a pracovních nebo školních účtů od jakékoli společnosti nebo organizace, která používá Azure Active Directory. (Viz [jak ukázka funguje](#how-the-sample-works) pro ilustraci.)


## <a name="prerequisites"></a>Požadavky

Chcete-li spustit tuto ukázku, budete potřebovat:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) nebo [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Baňka](http://flask.pocoo.org/), [Baňka-Session](https://pythonhosted.org/Flask-Session/), [žádosti](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
>
> Máte dvě možnosti spuštění aplikace pro rychlý start: express (možnost 1) a ruční (možnost 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Registrace a automatická konfigurace aplikace a následné stažení vzorového kódu
>
> 1. Přejděte na [portál Azure – registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs).
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace aplikace a vzorového kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
>
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na platformu identit Microsoftpro vývojáře [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
> 1. Vyberte **možnost Nová registrace**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>      - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `python-webapp`.
>      - V části **Podporované typy účtů**vyberte Účty ve všech **organizačních adresářích a osobních účtech Microsoft**.
>      - Vyberte **Zaregistrovat**.
>      - Na stránce **Přehled** aplikace si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.
> 1. V nabídce vyberte **ověřování** a přidejte následující informace:
>    - Přidejte konfiguraci **webové** platformy. Přidat `http://localhost:5000/getAToken` jako **přesměrování identifikátorů URI**.
>    - Vyberte **Uložit**.
> 1. V nabídce na levé straně zvolte **Certifikáty & tajných kódů** a klikněte na **Nový tajný klíč klienta** v části **Tajné klíče klienta:**
>
>      - Zadejte popis klíče (tajného klíče aplikace instance).
>      - Vyberte klíčovou dobu trvání **Do 1 roku**.
>      - Po kliknutí na **přidat**se zobrazí hodnota klíče.
>      - Zkopírujte hodnotu klíče. Budete ho potřebovat později.
> 1. Výběr oddílu **oprávnění rozhraní API**
>
>      - Klikněte na tlačítko **Přidat oprávnění** a potom
>      - Ujistěte se, že je vybraná karta **Microsoft API**
>      - V části *Běžně používaná rozhraní MICROSOFT API* klikněte na Microsoft **Graph**
>      - V části **Delegovaná oprávnění** zkontrolujte, zda jsou zaškrtnuta správná oprávnění: **User.ReadBasic.All**. V případě potřeby použijte vyhledávací pole.
>      - Vyberte tlačítko **Přidat oprávnění.**
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1: Nakonfigurujte si aplikaci na portálu Azure Portal
>
> Pro ukázku kódu pro tento rychlý start do práce, je třeba:
>
> 1. Přidejte adresu `http://localhost:5000/getAToken`URL odpovědi jako .
> 1. Vytvořte tajný klíč klienta.
> 1. Přidejte rozhraní User.ReadBasic.All delegovaného oprávnění rozhraní Microsoft Graph API.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Proveďte tyto změny pro mě]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-aspnet-webapp/green-check.png) Vaše aplikace je nakonfigurovaná s tímto atributem

#### <a name="step-2-download-your-project"></a>Krok 2: Stáhněte si projekt
> [!div renderon="docs"]
> [Stáhnout ukázku kódu](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Stáhněte projekt a extrahujte soubor ZIP do místní složky blíže ke kořenové složce – například **C:\Azure-Samples**
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Stáhnout ukázku kódu](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Krok 3: Konfigurace aplikace
>
> 1. Extrahujte soubor ZIP do místní složky bližší ke kořenové složce, třeba **C:\Azure-Samples**.
> 1. Pokud používáte integrované vývojové prostředí, otevřete ukázku ve vašem oblíbeném integrovaném vývojovém prostředí (volitelné).
> 1. Otevřete soubor **app_config.py,** který najdete v kořenové složce, a nahraďte je následujícím fragmentem kódu:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Kde:
>
> - `Enter_the_Application_Id_here` je ID aplikace, kterou jste zaregistrovali.
> - `Enter_the_Client_Secret_Here`- je **tajný klíč klienta,** který jste vytvořili v **& tajných kódů** pro aplikaci, kterou jste zaregistrovali.
> - `Enter_the_Tenant_Name_Here`- je **ID adresáře (tenanta)** aplikace, kterou jste zaregistrovali.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Krok 3: Spuštění ukázky kódu

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Krok 4: Spuštění ukázky kódu

1. Budete muset nainstalovat knihovnu MSAL Python, flask framework, Flask-Sessions pro správu relací na straně serveru a požadavky pomocí pipu následujícím způsobem:

    ```Shell
    pip install -r requirements.txt
    ```

2. Spusťte app.py z prostředí nebo příkazového řádku:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Tato aplikace rychlého startu používá tajný klíč klienta k identifikaci jako důvěrný klient. Vzhledem k tomu, že tajný klíč klienta je přidán jako prostý text do souborů projektu, z bezpečnostních důvodů se doporučuje použít certifikát namísto tajného klíče klienta před zvážením aplikace jako produkční aplikace. Další informace o použití certifikátu naleznete v [těchto pokynech](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Další informace

### <a name="how-the-sample-works"></a>Jak ukázka funguje
![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Získání MSAL
MSAL je knihovna používaná k přihlášení uživatelů a vyžádání tokenů používaných pro přístup k rozhraní API chráněnému platformou identity společnosti Microsoft.
MSAL Python můžete přidat do aplikace pomocí Pipu.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL
Odkaz na MSAL Python můžete přidat přidáním následujícího kódu do horní části souboru, kde budete používat MSAL:

```Python
import msal
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o webových aplikacích, které přihlašují uživatele a pak volají webová api:

> [!div class="nextstepaction"]
> [Scénář: Webové aplikace, které přihlašují uživatele](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
