---
title: Microsoft Identity Platform Python Web App Starter | Azure
description: Naučte se implementovat přihlašování Microsoftu na webové aplikaci v Pythonu pomocí OAuth2.
services: active-directory
documentationcenter: dev-center-name
author: abhidnya13
editor: ''
ms.assetid: 9551f0b5-04f2-44d7-87b5-756409180fe9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: 241935afa023162a35559cd3c46206efa7a7835f
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240184"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Rychlý Start: Přidání přihlašování do webové aplikace v Pythonu pomocí Microsoftu

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

V tomto rychlém startu se dozvíte, jak integrovat webovou aplikaci v Pythonu s platformou Microsoft identity. Vaše aplikace se přihlásí k uživateli, získá přístupový token pro volání rozhraní API Microsoft Graph a vytvoří požadavek na rozhraní Microsoft Graph API.

Po dokončení průvodce bude aplikace přijímat přihlašovacíky osobních účtů Microsoft (včetně outlook.com, live.com a dalších) a pracovních nebo školních účtů z jakékoli společnosti nebo organizace, která používá Azure Active Directory.

![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>Požadované součásti

K provedení této ukázky budete potřebovat:

- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) nebo [Python 3 +](https://www.python.org/downloads/release/python-364/)
- [Baňka](http://flask.pocoo.org/), [baňka – relace](https://pythonhosted.org/Flask-Session/), [žádosti](https://requests.kennethreitz.org//en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý Start
>
> Máte dvě možnosti, jak spustit aplikaci pro rychlý Start: Express (možnost 1) a ruční (možnost 2).
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: registrace a Automatická konfigurace aplikace a stažení ukázky kódu
>
> 1. Přejít na [Registrace aplikací Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Vyberte **Nová registrace**.
> 1. Zadejte název vaší aplikace a vyberte **zaregistrovat**.
> 1. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: registrace a ruční konfigurace vaší aplikace a ukázky kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: registrace aplikace
>
> K registraci aplikace a přidání registračních informací aplikace do řešení použijte následující postup:
>
> 1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
> 1. Pokud vám váš účet poskytne přístup k více než jednomu klientovi, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na stránku [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Microsoft Identity Platform for Developers.
> 1. Vyberte **Nová registrace**.
> 1. Jakmile se zobrazí stránka **Registrovat aplikaci** , zadejte informace o registraci vaší aplikace:
>      - V části **název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `python-webapp`.
>      - V části **podporované typy účtů**vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft**.
>      - V části **identifikátor URI pro přesměrování** vyberte v rozevíracím seznamu **webovou** platformu a nastavte hodnotu na `http://localhost:5000/getAToken`.
>      - Vyberte **Registrovat**. Na stránce **Přehled** aplikace si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.
> 1. V nabídce vlevo vyberte **certifikáty & tajných** kódů a v části **tajné klíče klienta** klikněte na **nový tajný klíč klienta** :
>
>      - Zadejte popis klíče (instance tajného kódu aplikace).
>      - Vyberte dobu trvání klíče **v intervalu 1 roku**.
>      - Po kliknutí na tlačítko **Přidat**se zobrazí hodnota klíče.
>      - Zkopírujte hodnotu klíče. Budete ho potřebovat později.
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
>
> Ukázku kódu pro tento rychlý Start, který funguje, je třeba:
>
> 1. Přidejte adresu URL odpovědi jako `http://localhost:5000/getAToken`.
> 1. Vytvořte tajný klíč klienta.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Tuto změnu pro mě udělat]()
> > [!div id="appconfigured" class="alert alert-info"]
> > @no__t – 0Already nakonfigurovaná na @ no__t-1 aplikace je nakonfigurovaná s tímto atributem.

#### <a name="step-2-download-your-project"></a>Krok 2: stažení projektu

[Stažení ukázky kódu](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>Krok 3: Konfigurace aplikace

1. Extrahujte soubor zip do místní složky blíž ke kořenové složce, například **C:\Azure-Samples**
1. Pokud používáte integrované vývojové prostředí, otevřete ukázku v oblíbeném INTEGROVANÉm vývojovém prostředí (volitelné).
1. Otevřete soubor **app_config. py** , který najdete v kořenové složce a nahraďte následujícím fragmentem kódu:

```python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
```

> [!div renderon="docs"]
> Kde:
>
> - `Enter_the_Application_Id_here` – je ID aplikace, kterou jste zaregistrovali.
> - `Enter_the_Client_Secret_Here`-je **tajný klíč klienta** , který jste vytvořili v části **certifikáty & tajných** kódů pro zaregistrovanou aplikaci.

#### <a name="step-4-run-the-code-sample"></a>Krok 4: spuštění ukázky kódu

1. Budete muset nainstalovat MSAL knihovny Pythonu, architekturu baněk, baňky pro správu relací na straně serveru a požadavky pomocí PIP následujícím způsobem:

   ```Shell
   pip install -r requirements.txt
   ```

2. Spusťte app.py z prostředí nebo příkazového řádku:

   ```Shell
   python app.py
   ```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o webových aplikacích, které přihlásí uživatele, a potom zavolá webová rozhraní API:

> [!div class="nextstepaction"]
> [Scénář: webové aplikace, které přihlásí uživatele](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
